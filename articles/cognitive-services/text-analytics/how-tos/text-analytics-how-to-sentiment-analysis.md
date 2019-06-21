---
title: Attitydanalys med hjälp av Textanalys från Azure Cognitive Services | Microsoft Docs
description: Lär dig att identifiera attityd med hjälp av den REST API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: d4269a99a8e535692e4897630a7edd9b27347d41
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304041"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>Exempel: Så identifierar du attityd med Textanalys

[API:et för attitydanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) utvärderar textinmatningen och returnerar ett attitydpoäng för varje dokument som sträcker sig från 0 (negativ) till 1 (positiv).

Den här funktionen är användbar för att upptäcka positiva och negativa åsikter i sociala medier, kundrecensioner och diskussionsforum. Innehållet kommer från modeller, utbildningsdata och tillhandahålls av tjänsten.

Attitydanalys stöder för närvarande engelska, tyska, spanska och franska. Övriga språk är i förhandsversion. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för attitydanalys, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="concepts"></a>Begrepp

Textanalys använder en klassificering i maskininlärningsalgoritmen för att generera ett attitydpoäng mellan 0 och 1. Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd. Modellen har förtränats med en omfattande textmassa med attitydassociationer. Det är för närvarande inte möjligt att ange dina egna träningsdata. Modellen använder en kombination av metoder under textanalys, inklusive textbearbetning, ordklassanalys, ordplacering och ordassociationer. Läs mer om algoritmen [Introduktion till textanalys](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/).

Attitydanalys utförs i hela dokumentet, till skillnad från åsiktsextrahering för en viss affärsenhet i texten. I praktiken finns det en tendens för bedömningsprecisionen att bli bättre när dokumentet innehåller en eller två meningar i stället för ett stort textblock. Under en objektivitetsutvärderingsfasen avgör modellen om ett dokument som helhet är objektivt eller innehåller attityd. Ett dokument som framför allt är objektivt hamnar inte i attityddetektionsfasen, vilket leder till 0,50 poäng utan ytterligare bearbetning. För dokument som fortsätter i pipelinen genererar nästa fas ett värde över eller under 0,50, beroende på nivån av attityd som detekterades i dokumentet.

## <a name="preparation"></a>Förberedelse

Attitydanalys ger resultat av högre kvalitet när du ger det mindre segment för att arbeta med. Detta är motsatsen till nyckelfrasextrahering vilket fungerar på bättre på större textblock. För att få bästa resultat från båda åtgärder kan du överväga att omstrukturera indata därefter.

Du måste ha JSON-dokument i det här formatet: ID, text, språk

Dokumentstorleken måste vara under 5 120 tecken per dokument, och du kan ha upp till 1 000 objekt (ID:n) per samling. Samlingen skickas i begäranstexten. Följande är ett exempel på innehåll som du kan skicka in för attitydanalys.

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om begäransdefinitionen finns i [Hur anropar man textanalys API:et](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [API för attitydanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

+ Ange HTTP-slutpunkten för attitydanalys, med hjälp av en Textanalys-resurs på Azure eller en instansierad [Textanalys-container](text-analytics-how-to-install-containers.md). Den måste innehålla `/sentiment`-resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [Hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Se den [databegränsningar](../overview.md#data-limits) avsnittet i översikten för information om storlek och antal begäranden som du kan skicka per minut och sekund.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Attitydanalysen klassificerar text som främst positiv eller negativ och ger den ett värde i intervallet 0 till 1. Värden nära 0,5 är neutrala eller obestämda. Ett resultat på 0,5 anger neutralitet. När en sträng inte kan analyseras efter attityd eller inte har någon attityd är poängen exakt alltid 0,5. Om du skickar in en spansk sträng i kod för engelska är poängen 0,5.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

I följande exempel visas svaret för dokumentsamlingen i den här artikeln.

```
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt känna begrepp och arbetsflöden för attitydanalys med hjälp av textanalys i Cognitive Services. Sammanfattning:

+ [Attitydanalys API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) är tillgängligt för utvalda språk.
+ JSON-dokument i begärandetexten innehålla ett ID, text och språk-kod.
+ POST-begäran riktas till en `/sentiment`-slutpunkt med hjälp av en personligt anpassad [åtkomstnyckel och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för din prenumeration.
+ Svarsutdata, vilka består av attitydpoäng för varje dokument-ID, kan strömmas till vilken app som helst som tar emot JSON, inklusive Excel och Power BI bara för att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera nyckelfraser](text-analytics-how-to-keyword-extraction.md)
