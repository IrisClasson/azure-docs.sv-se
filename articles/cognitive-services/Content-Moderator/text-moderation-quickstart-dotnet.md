---
title: 'Snabb start: analysera text innehåll C# i Content moderator'
titleSuffix: Azure Cognitive Services
description: Analysera text innehåll för olika stötande material med hjälp av Content Moderator SDK för .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 51202597aaee2940a764d0385d4d93c7409f4905
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935930"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Snabbstart: Analysera textinnehåll och leta efter stötande material i C#

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med [Content Moderator SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Du lär dig hur du kör termbaserade filtrering och klassificering av text innehåll i syfte att kunna göra det potentiellt olämpligt material.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Krav
- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [skapa ett Cognitive Services konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content moderator. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en med namnet `CONTENT_MODERATOR_SUBSCRIPTION_KEY` respektive `CONTENT_MODERATOR_ENDPOINT`.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> Den här guiden använder en kostnadsfri nivå för Content Moderator-prenumeration. Information om vad ingår i varje prenumerationsnivå finns det på sidan [Priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. Skapa ett nytt projekt för **Konsolprogram (.NET Framework)** i Visual Studio och ge det namnet **TextModeration**. 
1. Om det finns andra projekt i din lösning väljer du den här kopian som det enda startprojektet.
1. Hämta det nödvändiga NuGet-paketet. Högerklicka på ditt projekt i Solution Explorer och välj **Hantera NuGet-paket**. Leta sedan reda på och installera det **`Microsoft.Azure.CognitiveServices.ContentModerator` paketet**. Du kan också köra följande kommando från lösnings katalogen:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>Lägga till kod för textmoderering

Nu ska du kopiera och klistra in koden från den här guiden i ditt projekt för att implementera ett grundläggande innehållsmodereringsscenario.

### <a name="include-namespaces"></a>Inkludera namnområden

Lägg till följande `using`-instruktioner överst i *Program.cs*-filen.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_using)]

### <a name="create-the-content-moderator-client"></a>Skapa Content Moderator-klienten

Lägg till följande kod i filen *Program.cs* för att skapa en Content Moderator-klient för din prenumeration. Lägg till klassen tillsammans med **program** klassen i samma namnrymd. Du måste uppdatera fälten **AzureBaseURL** och **CMSubscriptionKey** med värdena för din slut punkts-URL och prenumerations nyckel. Du hittar dessa på fliken **snabb start** i resursen i Azure Portal.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_client)]

### <a name="set-up-input-and-output-targets"></a>Konfigurera inkommande och utgående mål

Lägg till följande statiska fält till klassen **Program** i _Program.cs_. De här fälten anger filerna för text innehåll och utdata-JSON-innehåll.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_fields)]

Du måste skapa *TextFile. txt* -indatafilen och uppdatera sökvägen (sökvägar är i förhållande till körnings katalogen). Öppna _TextFile.txt_ och lägg till text som ska modereras. Den här snabbstarten använder följande exempeltext:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Läsa in indatatexten

Lägg till följande kod i metoden **Main**. Metoden **ScreenText** är en viktig åtgärd. Dess parametrar anger vilka åtgärder för innehållsmoderering som kommer att göras. I det här exemplet konfigureras metoden för att:
- Identifiera potentiellt olämpligt språk i texten.
- Normalisera texten och autokorrigera stavfel.
- Identifiera personliga data, till exempel USA och Storbritannien, telefonnummer, e-postadresser och e-postadresser för oss.
- Använd maskininlärningsbaserade modeller för att klassificera texten i tre kategorier.

Om du vill veta mer om vad de här åtgärderna gör följer du länken i avsnittet [Nästa steg](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?name=snippet_main)]

## <a name="run-the-program"></a>Köra programmet

Programmet skriver JSON-strängdata till filen _TextModerationOutput.txt_. Exempeltexten som används i den här snabbstarten ger följande utdata:

```json
Autocorrect typos, check for matching terms, check for personal data, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Nästa steg

Du har utvecklat ett enkelt .NET-program som använder Content Moderator-tjänsten för att hitta relevant information om ett angivet textexempel i den här snabbstarten. Därefter kan du läsa mer om vad de olika flaggorna och klassificeringarna betyder så att du kan avgöra vilka data du behöver och hur din app ska hantera dem.

> [!div class="nextstepaction"]
> [Guide för textmoderering](text-moderation-api.md)
