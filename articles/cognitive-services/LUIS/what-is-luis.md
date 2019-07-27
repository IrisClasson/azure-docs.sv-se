---
title: Vad är Language Understanding Intelligent Service (LUIS)?
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 41c5e2f01678996406c586eb20043516beaf2184
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563187"
---
# <a name="what-is-language-understanding-luis"></a>Vad är Language Understanding Intelligent Service (LUIS)?

Language Understanding Intelligent Service (LUIS) är en molnbaserad API-tjänst som använder anpassad maskininlärningsinformation på en användares naturliga konversationsspråk för att förutsäga den övergripande betydelsen och hämta relevant, detaljerad information. 

Ett klientprogram för LUIS är alla konversationsanpassade program som kommunicerar med en användare på naturligt språk för att utföra en uppgift. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.  

![Konceptbild av tre klientprogram som fungerar med Cognitive Services Language Understanding Intelligent Service (LUIS)](./media/luis-overview/luis-entry-point.png "Konceptbild av tre klientprogram som fungerar med Cognitive Services Language Understanding Intelligent Service (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Använda LUIS i en chattrobot

<a name="Accessing-LUIS"></a>

När LUIS-appen har publicerats skickar ett klient program yttranden (text) till LUISs slut punkts- [API][endpoint-apis] för naturlig språk bearbetning och tar emot resultatet som JSON-svar. Ett vanligt klientprogram för LUIS är en chattrobot.


![Konceptbilder av LUIS som fungerar med chattrobot för att förutsäga användartext med förståelse av naturligt språk (NLP)](./media/luis-overview/luis-overview-process-2.png "Konceptbilder av LUIS som fungerar med chattrobot för att förutsäga användartext med förståelse av naturligt språk (NLP")

|Steg|Action|
|:--|:--|
|1|Klientprogrammet skickar användarens _yttrande_ (text med användarens egna ord), "I want to call my HR rep." (”Jag vill ringa HR-personalen.”) till LUIS-slutpunkten som en HTTP-begäran.|
|2|LUIS tillämpar den inlärda modellen på texten med naturligt språk för att tillhandahålla intelligent förståelse om användardata. LUIS returnerar ett JSON-formaterat svar, med en främsta avsikt, "HRContact" (”HR-kontakt”). Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan även extrahera data som entiteten Kontakttyp.|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur användarens begäranden sak uppfyllas. Dessa beslut kan inkludera ett beslutsträd i Bot Framework-koden och anropar andra tjänster. |

LUIS-appen tillhandahåller information så att klientprogrammet kan fatta smarta beslut. LUIS har inte dessa alternativ. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Bearbetning av naturligt språk

En LUIS-app innehåller en domänspecifik modell för naturligt språk. Du kan starta LUIS-appen med en fördefinierad domänmodell, bygga din egen modell eller blanda delar av en fördefinierad modell med egen anpassad information.

* LUIS med**fördefinierad modell** har många fördefinierade domänmodeller som innehåller avsikter, yttranden och fördefinierade yttranden. Du kan använda de fördefinierade entiteterna utan att behöva använda den fördefinierade modellens avsikter och yttranden. [Fördefinierade domänmodeller](luis-how-to-use-prebuilt-domains.md) innehåller hela designen för dig och är ett bra sätt att börja använda LUIS snabbt.

* LUIS med **anpassade entiteter** ger dig flera sätt att identifiera dina egna avsikter och entiteter, till exempel maskininlärda entiteter, specifika eller exakta entiteter, och en kombination av maskininlärda och exakta.

## <a name="build-the-luis-model"></a>Skapa LUIS-modellen
Skapa modellerna med [redigerings](https://go.microsoft.com/fwlink/?linkid=2092087)-API:er eller med LUIS-portalen.

LUIS-modellen börjar med kategorier av användaravsikter som kallas för **[avsikter](luis-concept-intent.md)** . Varje avsikt behöver exempel på **[yttranden](luis-concept-utterance.md)** från användaren. Varje yttrande kan ge olika data som måste extraheras med **[entiteter](luis-concept-entity-types.md)** . 

|Exempel på användaryttrande|Avsikt|Entiteter|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?" (”Boka en resa till Seattle?”)|BookFlight (Boka flyg)|Seattle|
|"When does your store __open__?" (”När öppnar butiken”)|StoreHoursAndLocation (Butikens öppettider och plats)|open (öppen)|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution" (”Boka ett möte kl. 13 med Bob”)|ScheduleMeeting (Boka möte)|1pm, Bob (kl. 13, Bob)|

## <a name="query-prediction-endpoint"></a>Slutpunkt för frågeförutsägelse

När modellen har skapats och publicerats på slutpunkten skickar klientprogrammet yttranden till den API:et för [slutpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) för förutsägelse. API:et tillämpar modellen på texten som ska analyseras. API:et svarar med förutsägelseresultatet i JSON-format.  

Det minsta JSON-slutpunktssvaret innehåller frågeyttrandet och avsikten med högsta poäng. Det kan även extrahera data, som följande **Kontakttyp**-entitet. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Förbättra modellförutsägelse

När en LUIS-modell har publicerats och får verkliga användaryttranden har LUIS flera metoder att förbättra förutsägelsernas precision: [aktiv inlärning](luis-concept-review-endpoint-utterances.md) för slutpunktsyttranden, [fraslistor](luis-concept-feature.md) för inkludering av domänord och [mönster](luis-concept-patterns.md) för att minska antalet yttranden som krävs.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Utvecklingscykel
LUIS tillhandahåller verktyg, versionshantering och samarbete med andra LUIS-författare för att integrera i den fullständiga utvecklingscykeln på nivån för klientprogrammet och språkmodellen. 

## <a name="implementing-luis"></a>Implementera LUIS
LUIS kan, som REST-API, användas med alla produkter, tjänster eller ramverk som gör en HTTP-begäran. Följande lista innehåller de främsta Microsoft-produkterna och -tjänsterna som används med LUIS.

Det vanligaste klientprogram för LUIS är:
* [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) skapar snabbt en LUIS-aktiverad chattrobot för att prata med en användare via textinmatning. Använder [bot Framework][bot-framework] version [4. x](https://github.com/Microsoft/botbuilder-dotnet) för en fullständig bot-upplevelse.

Verktyg för att snabbt och enkelt använda LUIS med en robot:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) NPM-paketet ger redigering och förutsägelse med antingen ett fristående kommando rads verktyg eller som import. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen är ett verktyg för att generera starkt typbestämd C#- och TypeScript-källkod från en exporterad LUIS-modell.
* Med [Dispatch](https://aka.ms/dispatch-tool) kan flera LUIS- och QnA Maker-appar användas via en överordnad app som använder en dispatcher-modell.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown är ett kommandoradsverktyg som hjälper dig att hantera språkmodeller för din robot.

Andra Cognitive Services-tjänster som används med LUIS:
* [QNA Maker][qnamaker] tillåter att flera typer av text kombineras till en fråga och besvarar kunskaps basen.
* [API för stavningskontroll i Bing](../bing-spell-check/proof-text.md) kontrollerar texten innan förutsägelse. 
* [Speech Service](../Speech-Service/overview.md) konverterar begäranden med talat språk till text. 
* Med [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) kan du skapa robotkonversationer snabbare med LUIS.
* [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) för att hantera robotsmåprat.

Exempel som använder LUIS:
* GitHub-lagringsplatsen [Conversational AI](https://github.com/Microsoft/AI).
* Azure-exempel för [Språkförståelse](https://github.com/Azure-Samples/cognitive-services-language-understanding)

## <a name="next-steps"></a>Nästa steg

Skapa en ny LUIS-app med en [fördefinierad](luis-get-started-create-app.md) eller [anpassad](luis-quickstart-intents-only.md) domän. [Fråga slutpunkten för förutsägelse](luis-get-started-cs-get-intent.md) för en offentlig IoT-app.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
