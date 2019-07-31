---
title: Vad är API för textanalys? Trådlösa
titleSuffix: Azure Cognitive Services
description: Använd API för textanalys från Azure Cognitive Services för sentiment-analys, extrahering av nyckel fraser, språk identifiering och enhets igenkänning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: f84d980dd01d1e9f3ffcc00d73f712211524cb42
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667648"
---
# <a name="what-is-text-analytics-api"></a>Vad är API för textanalys?

API för textanalys är en molnbaserad tjänst som tillhandahåller avancerad bearbetning av naturligt språk över rå text och innehåller fyra huvud funktioner: sentiment analys, extrahering av nyckel fraser, språk identifiering och enhets igenkänning.

API:et är en del av [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), en samling maskininlärnings- och AI-algoritmer i molnet för dina utvecklingsprojekt.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Text analyser kan betyda olika saker, men i Cognitive Services tillhandahåller API för textanalys fyra typer av analys enligt beskrivningen nedan.

## <a name="sentiment-analysis"></a>Attitydanalys
Använd [sentiment analys](how-tos/text-analytics-how-to-sentiment-analysis.md) för att ta reda på vad kunderna tycker om ditt varumärke eller ditt ämne genom att analysera rå text för LED trådar om positiv eller negativ sentiment. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva.<br /> Analysmodellen tränas i förväg med hjälp av en omfattande textmängd och tekniker för naturligt språk från Microsoft. För [utvalda språk](text-analytics-supported-languages.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. Du kan använda [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) -API: et eller [.net](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK.

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser
[Extrahera automatiskt viktiga fraser](how-tos/text-analytics-how-to-keyword-extraction.md) för att snabbt identifiera huvud punkterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. Du kan använda [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) -API: et här eller [.net](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK.

## <a name="language-detection"></a>Språkidentifiering
Du kan [identifiera vilket språk som indatamängden skrivs i](how-tos/text-analytics-how-to-language-detection.md) och rapportera en enda språkkod för varje dokument som skickas på begäran i en rad olika språk, varianter, dialekter och vissa regionala/kulturella språk. Språkkoden paras med poäng som anger styrkan hos poängen. Du kan använda [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) -API: et eller [.net](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK.

## <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet
[Identifiera och kategorisera entiteter](how-tos/text-analytics-how-to-entity-linking.md) i din text som personer, platser, organisationer, datum/tid, kvantiteter, procent, valutor och mycket annat. Välkända entiteter identifieras även länkas till mer information på webben. Du kan använda [rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) -API: et.

## <a name="use-containers"></a>Använda containrar

[Använd textanalyscontainrarna](how-tos/text-analytics-how-to-install-containers.md) för att extrahera nyckelfraser, identifiera språk och analysera sentiment lokalt genom att installera standardiserade Docker-containrar närmare dina data.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkelt: du skickar data för analys och hanterar utdata i din kod. Analysverktyg förbrukas i befintligt skick, utan någon ytterligare konfiguration eller anpassning.

1. [Registrera dig ](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) och få en [åtkomstnyckel](how-tos/text-analytics-how-to-access-key.md). Nyckeln måste skickas för varje begäran.

2. [Formulera en begäran](how-tos/text-analytics-how-to-call-api.md#json-schema) som innehåller dina data som rå ostrukturerad text, i JSON.

3. Publicera begäran till slutpunkten som etablerades vid registreringen och lägg till önskad resurs: attitydanalys, extrahering av diskussionsämne, språkidentifiering och entitetsidentifiering.

4. Strömma eller lagra svaret lokalt. Beroende på begäran är resultatet attitydpoäng, en samling extraherade nyckelfraser eller en språkkod.

Utdata returneras som ett enda JSON-dokument, med resultat för varje textdokument du har publicerat, baserat på ID. Du kan därefter analysera, visualisera eller kategorisera resultatet i användbara insikter.

Data lagras inte på ditt konto. Åtgärder som utförs av API för textanalys är tillståndslösa, vilket betyder att texten du anger bearbetas och resultaten returneras omedelbart.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalys för flera programmerings miljö nivåer

Du kan börja använda API för textanalys i dina processer, även om du inte har mycket erfarenhet av programmering. Använd de här självstudierna för att lära dig hur du kan använda API: t för att analysera text på olika sätt för att passa din erfarenhets nivå. 

* Minimal programmering krävs:
    * [Använd API för textanalys och MS Flow för att identifiera sentiment av kommentarer i en Yammer-grupp](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrera Power BI med API för textanalys för att analysera feedback från kunder](tutorials/tutorial-power-bi-key-phrases.md)
* Programmerings upplevelse rekommenderas:
    * [Sentimentanalys på strömmade data med hjälp av Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Bygg en kolv-app för att översätta text, analysera sentiment och syntetiskt tal](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Det här innehållet finns i [Språk som stöds i API för textanalys](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla av slutpunkterna för API för textanalys accepterar råtextdata. Den aktuella gränsen är 5 120 tecken för varje dokument. Om du behöver analysera större dokument kan du dela upp dem i mindre bitar. Om du fortfarande kräver en högre gräns kan du [kontakta oss](https://azure.microsoft.com/overview/sales-number/) så att vi kan diskutera dina krav.

| Gräns | Value |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 120 tecken enligt [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximal storlek på hela begäran | 1 MB |
| Maximalt antal dokument i en begäran | 1 000 dokument |

Din hastighets gräns varierar beroende på pris nivå.

| Nivå          | Begär Anden per sekund | Begär Anden per minut |
|---------------|---------------------|---------------------|
| Multi-service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Begär Anden mäts för varje Textanalys funktion separat. Du kan till exempel skicka maximalt antal begär Anden för din pris nivå till varje funktion på samma tillfälle.      

## <a name="unicode-encoding"></a>Unicode-kodning

API för textanalys använder Unicode-kodning för textrepresentation och beräkningar av antal tecken. Begäranden kan skickas i både UTF-8 och UTF-16 utan mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för teckenlängd och anses motsvara för databegränsningar för textanalys. Om du använder [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) för att hämta antalet tecken använder du samma metod som vi använder för att mäta datastorlek.

## <a name="next-steps"></a>Nästa steg

+ [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för textanalys för att få en nyckel och slut punkt för dina program.

+ [Snabbstart](quickstarts/csharp.md) är en genomgång av REST API-anropen skrivna i C#. Lär dig hur du skickar text, väljer en analys och visar resultat med minimal kod. Om du vill kan du starta med python- [snabb](quickstarts/python.md) starten i stället.

+ Se [vad som är nytt i API för textanalys](whats-new.md) för information om nya versioner och funktioner.

+ Lär dig mer om den här självstudien om [sentiment analys](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) med hjälp av Azure Databricks.

+ Kolla in vår lista med blogg inlägg och fler videor om hur du använder API för textanalys med andra verktyg och tekniker på sidan för den [externa & community-innehåll](text-analytics-resource-external-community.md).
