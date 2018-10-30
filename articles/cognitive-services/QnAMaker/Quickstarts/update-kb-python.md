---
title: 'Snabbstart: Uppdatera kunskapsbas – REST, Python – QnA Maker'
description: Den här REST-baserade snabbstarten visar hur du uppdaterar ditt exempel på en QnA Maker-kunskapsbas (KB) programmatiskt. Via JSON-definitionen som du använder för att uppdatera en KB kan du lägga till, ändra eller ta bort frågor och svar-par.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 35ef0222de6093e7bae3b41377db2ea3fd3c3d75
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645922"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-python"></a>Snabbstart: Uppdatera en kunskapsbas i QnA Maker med hjälp av Python

Den här snabbstarten går igenom programmatisk uppdatering av en befintlig QnA Maker-kunskapsbas (KB).  Med denna JSON kan du uppdatera en KB genom att lägga till nya datakällor, ändra datakällor eller ta bort datakällor.

Det här API:et motsvarar redigering, och använd sedan knappen för att **spara och öva** i QnA Maker-portalen.

Den här snabbstarten anropar API:er för QnA Maker:
* [Uppdatera](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) – Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran. 
* [Hämta åtgärdsinformation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-python.md).

1. Skapa ett nytt Python-projekt i valfri IDE.
1. Lägg till koden nedan.
1. Ersätt värdet `subscriptionKey` med en giltig prenumerationsnyckel.
1. Ersätt värdet `kb` med ett giltigt kunskapsbas-ID. Du hittar värdet i någon av dina [QnA Maker-kunskapsbaser](https://www.qnamaker.ai/Home/MyServices). Välj den kunskapsbas som du vill uppdatera. När du är på sidan letar du reda på ”kdid=” i URL:en som visas nedan. Använd värdet för kodexemplet.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

1. Kör programmet.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ADD KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ADD ID HERE';

# Represents the various elements used to create HTTP request path
# for QnA Maker operations.
host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

'''
Formats and indents JSON for display.
:param content: The JSON to format and indent.
:type: string
:return: Formatted and indented JSON.
:rtype: string
'''
def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

'''
Sends a PATCH HTTP request.
:param path: The URL path of your request.
:param content: The contents of your PATCH request.
:type: string
:return: URL with status of PATCH request in updating the kb, actual response.
:rtype: string, string
'''
def update_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
    return response.getheader('Location'), response.read ()

'''
Gets the status of the specified QnA Maker operation.
:param path: The URL of the request.
:type: string
:return: Header from retrying of the request (if retry is needed), response of the retry.
:rtype: string, string
'''
def check_status (path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse ()
    # If the operation is not finished, /operations returns an HTTP header named
    # 'Retry-After' with the number of seconds to wait before querying the operation again.
    return response.getheader('Retry-After'), response.read ()

'''
Dictionary that holds the knowledge base.
Modifications to the knowledge base are made here, using 'update', 'delete' and so on.
'''
req = {
    'add': {
        'qnaList': [
            {
              'id': 1,
              'answer': 'You can change the default message if you use the QnAMakerDialog. '
                      + 'See this for details:  https://docs.botframework.com/en-us'
                      + '/azure-bot-service/templates/qnamaker/#navtitle',
              'source': 'Custom Editorial',
              'questions': [
                  'How can I change the default message from QnA Maker?'
              ],
              'metadata': []
            }
        ],
        'urls': []
    },
    'update' : {
        'name' : 'New KB Name'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}

# Builds the path URL.
path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
# Retrieve the operation ID to check status, and JSON result.
operation, result = update_kb(path, content)
# Print request response in JSON with presentable formatting.
print(pretty_print(result))

'''
Iteratively gets the operation state, updating the knowledge base.
Once state is no longer "Running" or "NotStarted", the loop ends.
'''
done = False
while False == done:
    path = service + operation
    # Gets the status of the operation.
    wait, status = check_status(path)
    # Print status checks in JSON with presentable formatting
    print(pretty_print(status))

    # Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
    # If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True # request has been processed, if successful, knowledge base is updated
```

## <a name="understand-what-qna-maker-returns"></a>Förstå vad QnA Maker returnerar

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel. Dina resultat kan skilja sig något. Om det sista anropet returnerar statusen ”Lyckades” har kunskapsbasen uppdaterats. Om du vill felsöka refererar du till svarskoden för [uppdatering av kunskapsbasen](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) för QnA Maker-API:et.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917rt4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917rt4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

När kunskapsbasen har uppdaterats kan du visa den i QnA Maker-portalen, på sidan [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Mina kunskapsbaser). Obs! Ett nytt QnA-par har lagts till och namnet på databasen är nu ”New KB Name”.

Om du vill ändra andra element i kunskapsbasen refererar du till [JSON-schemat](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) i QnA Maker och ändrar strängen `req`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
