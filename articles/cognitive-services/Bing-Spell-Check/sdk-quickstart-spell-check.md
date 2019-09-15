---
title: 'Snabbstart: Kontrollera stavning med SDK för stavningskontroll i Bing för C#'
titleSuffix: Azure Cognitive Services
description: Kom igång med REST API för stavningskontroll i Bing för att kontrollera stavning och grammatik.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 74697d69fbeb9072f839f0b6d49c010c5a7a7a05
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996713"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Snabbstart: Kontrollera stavning med SDK för stavningskontroll i Bing för C#

Använd den här snabbstarten för att börja kontrollera stavning med SDK för stavningskontroll i Bing för C#. Även om Stavningskontroll i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Programberoenden

* En version av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/).
* [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) för Stavningskontroll i Bing

Om du vill lägga till Stavningskontroll i Bing SDK i projektet väljer du **Hantera NuGet-paket** från **Solution Explorer** i Visual Studio. Lägg till paketet `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. Paketet installerar även följande beroenden:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande `using`-instruktion.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Skapa en ny klass. Skapa sedan en asynkron funktion som heter `SpellCheckCorrection()` och tar en prenumerationsnyckel och skickar begäran om stavningskontroll.

3. Instansiera klienten genom att skapa ett nytt `ApiKeyServiceClientCredentials`-objekt. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Skicka begäran och läsa svaret

1. I funktionen ovan utför du följande steg. Skicka begäran om stavningskontroll med klienten. Lägg till den text som ska kontrolleras i parametern `text` och ange läget till `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Hämta det första resultatet av stavningskontrollen om det finns ett sådant. Skriv ut det första felstavade ord (token) som returnerats, tokentypen samt antalet förslag.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Hämta den första föreslagna korrigeringen, om det finns en sådan. Skriv ut förslags poängen och det föreslagna ordet. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)