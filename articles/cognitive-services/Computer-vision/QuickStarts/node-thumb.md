---
title: 'Snabbstart: Skapa en miniatyrbild – REST, Node.js'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8307b572eb73a96c23f3327cbda4ad2ac3126acc
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137710"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Snabbstart: Generera en miniatyr med hjälp av Visuellt innehåll REST API och Node. js

I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av REST-API:t i Visuellt innehåll. Med metoden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) kan du generera en miniatyrbild av en bild. Du anger höjden och bredden, som kan skilja sig från den ursprungliga bildens proportioner. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha [Node.js](https://nodejs.org) 4.x eller senare installerat.
- Du måste ha [npm](https://www.npmjs.com/) installerat.
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnads fri utvärderings nyckel från [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på visuellt innehåll och hämta din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och tjänst slut punkts strängen, `COMPUTER_VISION_SUBSCRIPTION_KEY` med `COMPUTER_VISION_ENDPOINT`namnet respektive.

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Installera npm-paketet [`request`](https://www.npmjs.com/package/request).
   1. Öppna ett kommandotolksfönster som administratör.
   1. Kör följande kommando:

      ```console
      npm install request
      ```

   1. Stäng kommandotolksfönstret när paketet har installerats.

1. Kopiera följande kod till en textredigerare.
1. Du kan också ersätta värdet för `imageUrl` med webbadressen till en annan bild som du vill analysera.
1. Spara koden som en fil med tillägget `.js`. Till exempel `get-thumbnail.js`.
1. Öppna ett kommandotolksfönster.
1. Kör filen i kommandotolken med kommandot `node`. Till exempel `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.0/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
});
```

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i form av binärdata som representerar bilddata för miniatyrbilden. Om förfrågningen misslyckas visas svaret i konsolfönstret. Svaret för en misslyckad begäran innehåller en felkod och ett meddelande som hjälper dig att avgöra vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska API:et för visuellt innehåll, som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
