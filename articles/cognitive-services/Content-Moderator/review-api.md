---
title: Granskningar, arbets flöden och jobb koncept – Content Moderator
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig mer om huvud begreppen i gransknings verktyget. recensioner, arbets flöden och jobb.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221154"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Granskningar av innehålls moderatorer, arbets flöden och jobb

Content Moderator kombinerar dator-assisterad redaktör med funktioner för mänsklig inblandning för att skapa en optimal redigerings process för verkliga scenarier. Detta sker via det molnbaserad [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com). I den här guiden får du lära dig mer om huvud begreppen i gransknings verktyget: granskningar, arbets flöden och jobb.

## <a name="reviews"></a>Omdömen

I en granskning överförs innehållet till gransknings verktyget och visas på fliken **Granska** . Härifrån kan användare ändra de använda taggarna och tillämpa egna anpassade taggar efter behov. När en användare skickar en granskning skickas resultatet till en angiven slut punkt för återanrop och innehållet tas bort från platsen.

![Granska verktygs webbplats öppna i en webbläsare på fliken Granska](./Review-Tool-user-Guide/images/image-workflow-review.png)

I [guiden granska verktyg](./review-tool-user-guide/review-moderated-images.md) kan du komma igång med att skapa recensioner eller se [rest Apis guide](./try-review-api-review.md) för att lära dig hur du gör det program mässigt.

## <a name="workflows"></a>Arbetsflöden

Ett arbets flöde är ett molnbaserad anpassat filter för innehåll. Arbets flöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Med Content Moderator-anslutningen kan ett arbets flöde automatiskt tillämpa kontrollanter och skapa recensioner med innehåll som har skickats.

### <a name="view-workflows"></a>Visa arbets flöden

Om du vill visa dina befintliga arbets flöden går du till [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) och väljer **Inställningar** > **arbets flöden**.

![Standard arbets flöde](images/default-workflow-listed.PNG)

Arbets flöden kan beskrivas fullständigt som JSON-strängar som gör dem tillgängliga via programmering. Om du väljer **redigerings** alternativet för arbets flödet och sedan väljer fliken **JSON** visas ett JSON-uttryck som följande:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

I [guiden granska verktyg](./review-tool-user-guide/workflows.md) kan du komma igång med att skapa och använda arbets flöden, eller se [rest Apis guide](./try-review-api-workflow.md) för att lära dig hur du gör det program mässigt.

## <a name="jobs"></a>Jobb

Ett redigerings jobb fungerar som en typ av omslutning för funktionerna i Content moderatoring, arbets flöden och recensioner. Jobbet skannar ditt innehåll med hjälp av API: et för Content Moderator bild redigeringsprogram eller API för text redigering och kontrollerar det mot det angivna arbets flödet. Utifrån arbets flödes resultatet kan det hända att det inte går att skapa en granskning för innehållet i [gransknings verktyget](./review-tool-user-guide/human-in-the-loop.md). Både granskningar och arbets flöden kan skapas och konfigureras med deras respektive API: er, men jobb-API: et gör att du kan få en detaljerad rapport över hela processen (som kan skickas till en angiven slut punkt för återanrop).

Se [rest Apis guide](./try-review-api-job.md) för att komma igång med jobb.

## <a name="next-steps"></a>Nästa steg

* Testkör jobb- [API-konsolen](try-review-api-job.md)och Använd REST API kod exempel. Om du är bekant med Visual Studio och C# kan du också ta en titt på [jobben .net snabb start](moderation-jobs-quickstart-dotnet.md). 
* För recensioner kan du komma igång med [Granska API-konsolen](try-review-api-review.md)och använda REST API kod exempel. Se avsnittet recensioner i [.net-snabb](dotnet-sdk-quickstart.md)starten.
* För video granskningar använder du [snabb starten för video granskning](video-reviews-quickstart-dotnet.md)och lär dig hur du [lägger till avskrifter i video granskningen](video-transcript-reviews-quickstart-dotnet.md).
