---
title: 'Snabb start: anropa din Anpassad sökning i Bing-slutpunkt med Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten till att börja begära sökresultat från instansen av anpassad Bing-sökning med hjälp av Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 97bf62c61aee63ce97496e54b99ad641d6150876
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972717"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Snabb start: anropa din Anpassad sökning i Bing slut punkt med Node. js

Använd den här snabbstarten till att börja begära sökresultat från din instans av anpassad Bing-sökning. Även om det här programmet är skrivet i JavaScript är API:et för anpassad Bing-sökning en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Källkoden till det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Krav

- En instans av anpassad Bing-sökning. Se [snabb start: skapa din första anpassad sökning i Bing-instans](quick-start.md) för mer information.

- [Node.js](https://www.nodejs.org/)

- [Begäransbiblioteket för JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i valfri IDE eller redigeringsprogram och lägg till en `require()`-instruktion för biblioteket för begäranden. Skapa variabler för prenumerationsnyckeln, ID för anpassad konfiguration och en sökterm. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Skapa en variabel för att lagra informationen som skickas i din begäran. Konstruera en begäran-URL genom att lägga till söktermen i `q=`-frågeparametern och sökinstansens ID för anpassad konfiguration i `customconfig=`. Avgränsa parametrarna med ett `&`-tecken. Du kan använda den globala slut punkten nedan eller den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Använd begäransbiblioteket för JavaScript för att skicka en sökbegäran till instansen för anpassad Bing-sökning och skriv ut information om resultat, inklusive dess namn, URL:en och det datum då webbsidan senast crawlades.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)
