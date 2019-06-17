---
title: Microsoft Bing Speech Service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd Microsoft Speech API för att lägga till talstyrda åtgärder i dina appar, inklusive i realtid interaktionen med användarna.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d2c7211831658a18e65e04aa753607f4eb22dac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515091"
---
# <a name="what-is-bing-speech"></a>Vad är Bing-taligenkänning?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Molnbaserade Microsoft Bing Speech API får utvecklare ett enkelt sätt att skapa kraftfulla funktioner som stöd för tal i sina program, t.ex. röst kommandokontroll, dialogruta för användare med hjälp av naturligt språk konversation, och taltranskription och diktering. Microsoft Speech API stöder både *tal till Text* och *Text till tal* konvertering.

- **Tal till Text** API omvandlar tal till text som kan användas som indata eller kommandon för att kontrollera ditt program.
- **Text till tal** API: texten omvandlas till ljudströmmar som kan spelas upp för användare av ditt program.

## <a name="speech-to-text-speech-recognition"></a>Tal till text (Taligenkänning)

Microsoft API för taligenkänning *transkriberar* ljudströmmar till text som ditt program kan visa för användaren eller vidta åtgärder som kommandot indata. Det finns två sätt för utvecklare att lägga till tal i sina appar: REST API: erna **eller** Websocket-baserade klientbibliotek.

- [REST API: er](GetStarted/GetStartedREST.md): Utvecklare kan använda HTTP-anrop från sina appar till tjänsten för taligenkänning.
- [Klientbibliotek](GetStarted/GetStartedClientLibraries.md): För avancerade funktioner, kan utvecklare hämta Microsoft Speech-klientbibliotek och länka till sina appar.  Klientbiblioteken är tillgängliga på olika plattformar (Windows, Android, iOS) med olika språk (C#, Java, JavaScript, ObjectiveC). Till skillnad från REST API: er använda klientbiblioteken Websocket-baserat protokoll.

| Användningsfall | [REST API:er](GetStarted/GetStartedREST.md) | [Klientbibliotek](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertera en kort sagt talat ljud till exempel kommandon (ljud längd < 15 s) utan mellanliggande resultat | Ja | Ja |
| Omvandla en lång (> 15 s) | Nej | Ja |
| Stream ljud med mellanliggande resultat som önskas | Nej | Ja |
| Förstå den text som konverterats från ljud med LUIS | Nej | Ja |

Oavsett vilken metod utvecklare välja (REST API: er eller klientbibliotek), Microsoft speech-tjänsten har stöd för följande:

- Avancerade tal igenkänning av tekniker från Microsoft som används av Cortana, diktering för Office, Office Translator och andra Microsoft-produkter.
- I realtid kontinuerlig erkännande. Taligenkänningens API kan du transkribera ljud till text i realtid och har stöd för att ta emot mellanresultat av ord som har blivit erkänt hittills. Speech-tjänsten stöder också slutet av tal-identifiering. Dessutom kan användare välja ytterligare formatering funktioner, som versaler och skiljetecken, maskning svordomar och normalisering av text.
- Har stöd för optimerad tal resultat för *interaktiva*, *konversationen*, och *diktering* scenarier. För scenarier som kräver anpassade språkmodeller och akustiska modeller [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) kan du skapa talmodeller som skräddarsys för ditt program och dina användare.
- Stöd många talat språk i flera dialekter. En fullständig lista över språk som stöds i varje läge för igenkänning av Se [språk](api-reference-rest/supportedlanguages.md).
- Integrering med language understanding. Förutom att konvertera indata ljud till text, den *tal till Text* ger program en ytterligare funktion att förstå vad det innebär att texten. Den använder den [Language Understanding Intelligent Service(LUIS)](../LUIS/what-is-luis.md) att extrahera avsikter och entiteter från den tolkade texten.

### <a name="next-steps"></a>Nästa steg

- Kom igång med att använda Microsoft speech igenkänning av tjänsten med [REST API: er](GetStarted/GetStartedREST.md) eller [klientbibliotek](GetStarted/GetStartedClientLibraries.md).
- Kolla in [programexempel](samples.md) i din prioriterade programmeringsspråket.
- Gå till avsnittet referens för att hitta [Microsoft Speech Protocol](API-Reference-REST/websocketprotocol.md) information och API-referenser.

## <a name="text-to-speech-speech-synthesis"></a>Text till tal (talsyntes)

*Text till tal* API: er Använd REST för att omvandla strukturerade text till en ljudström. API: er ger snabb text till talad konversation i olika röster och språk. Användare har också möjlighet att ändra ljud egenskaper som uttal, volym, försäljningsargument osv. med hjälp av SSML taggar.

### <a name="next-steps"></a>Nästa steg

- Börja använda Microsoft text till tal-tjänst: [API-referens för text till tal](api-reference-rest/bingvoiceoutput.md). En fullständig lista över språk och röster som stöds av Text till tal, se [stöd för nationella inställningar och Rösttyper](api-reference-rest/bingvoiceoutput.md#SupLocales).
