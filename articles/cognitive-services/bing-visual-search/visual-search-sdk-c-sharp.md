---
title: 'Quickstart: Get image insights using the SDK for C# - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Ta reda på hur du laddar upp en bild med hjälp av API för visuell sökning i Bing och får information om den.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 3db40f999770823052f07a328d3d8dc8c01ce345
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383585"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Quickstart: Get image insights using the Bing Visual Search SDK for C#

Använd den här snabbstarten om du vill få information om bilder från den visuella söktjänsten i Bing med hjälp av C# SDK. Även om den visuella sökningen i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Krav

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* NuGet-paketet för Visuell sökning. 
    - Från Solution Explorer i Visual Studio högerklickar du på projektet och väljer `Manage NuGet Packages` på menyn. Installera `Microsoft.Azure.CognitiveServices.Search.VisualSearch`-paketet. Installering av NuGet-paketet installerar även följande:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa ett nytt projekt i Visual Studio. Lägg sedan till följande direktiv.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Skapa instans av klienten med prenumerationsnyckeln.
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Skicka en sökbegäran 

1. Skapa en `FileStream` till bilderna (i det här fallet `TestImages/image.jpg`). Sedan använder du klienten för att skicka en sökbegäran med `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Parsa resultaten från den föregående frågan:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-visual-search-single-page-app.md)
