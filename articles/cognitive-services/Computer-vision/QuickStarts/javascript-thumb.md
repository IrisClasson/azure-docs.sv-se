---
title: 'Snabbstart: Skapa en miniatyrbild – REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1d07bc12f33df7253a849b605fdaff1f2f0123dd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974554"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-javascript"></a>Snabbstart: Generera en miniatyrbild med REST-API:et för visuellt innehåll och JavaScript

I den här snabbstarten skapar du en miniatyrbild från en bild med REST-APIN för visuellt innehåll. Du anger höjd och bredd, som kan skilja sig i proportioner från indatabilden. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri testversionsnyckel från [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Du kan också följa instruktionerna i [Skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Datorseende och få din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och `COMPUTER_VISION_SUBSCRIPTION_KEY` tjänstslutpunktssträngen, med namnet respektive `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample"></a>Skapa och köra exemplet

Så här skapar du och kör exemplet:

1. Kopiera följande kod till en textredigerare.
1. Du kan också ersätta värdet för `value`-attributet för `inputImage`-kontrollen med webbadressen till en annan bild som du vill analysera.
1. Spara koden som en fil med tillägget `.html`. Till exempel `get-thumbnail.html`.
1. Öppna ett webbläsarfönster.
1. Dra och släpp filen till webbläsarfönstret i webbläsaren.
1. När webbsidan visas i webbläsaren väljer du knappen **Generera miniatyrbild**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Thumbnail Sample</title>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
        let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
        if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }
        
        var uriBase = endpoint + "vision/v2.1/generateThumbnail";

        // Request parameters.
        var params = "?width=100&height=150&smartCropping=true";

        // Display the source image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Prepare the REST API call:

        // Create the HTTP Request object.
        var xhr = new XMLHttpRequest();

        // Identify the request as a POST, with the URL and parameters.
        xhr.open("POST", uriBase + params);

        // Add the request headers.
        xhr.setRequestHeader("Content-Type","application/json");
        xhr.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        // Set the response type to "blob" for the thumbnail image data.
        xhr.responseType = "blob";

        // Process the result of the REST API call.
        xhr.onreadystatechange = function(e) {
            if(xhr.readyState === XMLHttpRequest.DONE) {

                // Thumbnail successfully created.
                if (xhr.status === 200) {
                    // Show response headers.
                    var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                    document.getElementById("responseTextArea").value =
                        JSON.stringify(xhr.getAllResponseHeaders(), null, 2);

                    // Show thumbnail image.
                    var urlCreator = window.URL || window.webkitURL;
                    var imageUrl = urlCreator.createObjectURL(this.response);
                    document.querySelector("#thumbnailImage").src = imageUrl;
                } else {
                    // Display the error message. The error message is the response
                    // body as a JSON string. The code in this code block extracts
                    // the JSON string from the blob response.
                    var reader = new FileReader();

                    // This event fires after the blob has been read.
                    reader.addEventListener('loadend', (e) => {
                        document.getElementById("responseTextArea").value =
                            JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                    });

                    // Start reading the blob as text.
                    reader.readAsText(xhr.response);
                }
            }
        }

        // Make the REST API call.
        xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
    };
</script>

<h1>Generate thumbnail image:</h1>
Enter the URL to an image to use in creating a thumbnail image,
then click the <strong>Generate thumbnail</strong> button.
<br><br>
Image for thumbnail:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg" />
<button onclick="processImage()">Generate thumbnail</button>
<br><br>
<div id="wrapper" style="width:1160px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
    <div id="thumbnailDiv" style="width:140px; display:table-cell;">
        Thumbnail:
        <br><br>
        <img id="thumbnailImage" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar returneras i form av binärdata som representerar bilddata för miniatyrbilden. Om begäran lyckas så genereras miniatyrbilden från binärdata i svaret och visas i webbläsarfönstret. Om förfrågningen misslyckas visas svaret i konsolfönstret. Svaret för en misslyckad begäran innehåller en felkod och ett meddelande som hjälper dig att avgöra vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska ett JavaScript-program som använder Visuellt innehåll för att utföra optisk teckenläsning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriv visuella funktioner, inklusive ansikten, i en bild. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Självstudie: API för visuellt innehåll med JavaScript](../Tutorials/javascript-tutorial.md)
