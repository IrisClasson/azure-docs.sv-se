---
title: Hämta avsikt, Node.js
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext. Skicka med hjälp av Node.js användarens avsikt som text till den offentliga appens HTTP-slutpunkt för förutsägelse.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/17/2019
ms.author: diberry
ms.openlocfilehash: 161056aa2cfe43375d5e555197b57f85685ca219
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594098"
---
# <a name="quickstart-get-intent-using-nodejs"></a>Snabbstart: Hämta avsikt med Node.js

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Nödvändiga komponenter

* Programmeringsspråket [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* Offentligt app-ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> Den fullständiga Node.js-lösningen finns i [**Azure-Samples**-GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Hämta LUIS-nyckel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Hämta avsikter i en webbläsare

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Hämta avsikter programmatiskt

Du kan använda Node.js för att komma åt samma resultat som du såg i webbläsarfönstret i föregående steg.

1. Kopiera följande kodfragment:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Skapa filen `.env` med följande text eller ange dessa variabler i systemmiljön:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Ange miljövariabeln `LUIS_ENDPOINT_KEY` för nyckeln.

4. Installera beroenden genom att köra följande kommando på kommandoraden: `npm install`.

5. Kör koden med `npm start`. Det visar samma värden som du såg tidigare i webbläsarfönstret.

## <a name="luis-keys"></a>LUIS-nycklar

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort Node.js-filen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lägg till yttranden](luis-get-started-node-add-utterance.md)
