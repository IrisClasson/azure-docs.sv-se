---
title: 'Snabbstart: API för stavningskontroll i Bing, Node.js'
titlesuffix: Azure Cognitive Services
description: Hämta information och kodexempel som hjälper dig att snabbt komma igång med API:et för stavningskontroll i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: aahi
ms.openlocfilehash: e98d487723201836a7f1ab1590db1e9d7777d5a7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310783"
---
# <a name="quickstart-for-bing-spell-check-api-with-nodejs"></a>Snabbstart för API:et för stavningskontroll i Bing med Node.js 

Den här artikeln visar hur du använder [API:et för stavningskontroll i Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) med Node.js. API:et för stavningskontroll returnerar en lista med ord som det inte kan identifiera tillsammans med föreslagna ersättningar. Vanligtvis skickar du text till det här API:et och sedan gör du antingen föreslagna ersättningar i texten eller visar dem för användaren av programmet så att de kan avgöra om de vill göra ersättningarna. Den här artikeln visar hur du skickar en begäran som innehåller texten ”Hollo, wrld!”. Föreslagna ersättningar blir ”Hello” och ”world”.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Node.js 6](https://nodejs.org/en/download/) för att kunna köra den här koden.

Du måste ha ett [API-konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med **API v7 för stavningskontroll i Bing**. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/#lang) för den här snabbstarten. Du behöver den åtkomstnyckel som du fick när du aktiverade din kostnadsfria utvärderingsversion, eller så kan du använda en betald prenumerationsnyckel från instrumentpanelen i Azure.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="get-spell-check-results"></a>Få stavningskontrollsresultat

1. Skapa ett nytt Node.js-projekt i valfri IDE.
2. Lägg till koden nedan.
3. Ersätt värdet `subscriptionKey` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

```nodejs
'use strict';

let https = require ('https');

let host = 'api.cognitive.microsoft.com';
let path = '/bing/v7.0/spellcheck';

/* NOTE: Replace this example key with a valid subscription key (see the Prequisites section above). Also note v5 and v7 require separate subscription keys. */
let key = 'ENTER KEY HERE';

// These values are used for optional headers (see below).
// let CLIENT_ID = "<Client ID from Previous Response Goes Here>";
// let CLIENT_IP = "999.999.999.999";
// let CLIENT_LOCATION = "+90.0000000000000;long: 00.0000000000000;re:100.000000000000";

let mkt = "en-US";
let mode = "proof";
let text = "Hollo, wrld!";
let query_string = "?mkt=" + mkt + "&mode=" + mode;

let request_params = {
    method : 'POST',
    hostname : host,
    path : path + query_string,
    headers : {
        'Content-Type' : 'application/x-www-form-urlencoded',
        'Content-Length' : text.length + 5,
        'Ocp-Apim-Subscription-Key' : key,
//        'X-Search-Location' : CLIENT_LOCATION,
//        'X-MSEdge-ClientID' : CLIENT_ID,
//        'X-MSEdge-ClientIP' : CLIENT_ID,
    }
};

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        console.log (body);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

**Svar**

Ett svar som anger att åtgärden lyckades returneras i JSON, som du ser i följande exempel: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie för stavningskontroll i Bing](../tutorials/spellcheck.md)

## <a name="see-also"></a>Se även

- [Översikt över stavningskontroll i Bing](../proof-text.md)
- [API-referens för stavningskontroll i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
