---
title: Vad är Translator Text API? - Translator Text API
titlesuffix: Azure Cognitive Services
description: Integrera Translator Text API i program, på webbplatser och i verktyg och andra lösningar för att tillhandahålla användarupplevelser för flera språk.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: f6b55163440b25bf69c76e9b1217c439b0884d5d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331050"
---
# <a name="what-is-the-translator-text-api"></a>Vad är Translator Text API?

Translator Text API är enkelt att integrera i dina program, webbplatser, verktyg och lösningar. Det gör att du kan flerspråksanvändarupplevelser med [fler än 60 språk](languages.md), och det kan användas på alla maskinvaruplattformar med alla operativsystem för text-till-text-språköversättningar.

Translator Text API är en del av Azure [Cognitive Services API](https://docs.microsoft.com/azure/?pivot=products&panel=ai)-samlingen med maskininlärnings- och AI-algoritmer i molnet, och det kan enkelt användas i dina utvecklingsprojekt.

## <a name="about-microsoft-translator"></a>Om Microsoft Translator

Microsoft Translator är en molnbaserad maskinöversättningstjänst. Den viktigaste tjänsten är Translator Text API, som driver ett antal produkter och tjänster från Microsoft och används av tusentals företag över hela världen i program och arbetsflöden, vilket gör att deras innehåll kan nå en världsomspännande publik.

Talöversättning, som drivs av Translator Text API, är också tillgängligt via [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Den kombinerar funktioner från Translator Speech API och Custom Speech Service till en enhetlig och helt anpassningsbar tjänst.Speech Service ersätter Translator Speech API, som tas ur drift 15 oktober 2019.

## <a name="language-support"></a>Stöd för språk

Microsoft Translator ger flerspråksstöd för översättning, translitteration, språkidentifiering och ordlistor. Se [språkstöd](language-support.md) för en fullständig lista, eller kom åt listan programmatiskt med [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neural maskinöversättning med Microsoft Translator

Neural maskinöversättning är den nya standarden för högkvalitativ AI-driven maskinöversättning. Den ersätter den äldre statistiska maskinöversättningen, som nådde en kvalitetsplatå i mitten av 2010-talet.

Neural maskinöversättning ger bättre översättningar än den statistiska varianten. Den ger bättre överensstämmelse betydelsemässigt och låter dessutom mer mänsklig. Den viktigaste orsaken till det är att hela meningskontexten används när ord översätts. I den statistiska översättningen användes bara direkta samband med några få ord före och efter varje ord.

Neurala modeller är kärnan i API:et och visas inte för slutanvändaren. Den enda märkbara skillnaden är förbättrad översättning, och särskilt för språk som arabiska, kinesiska och japanska.

Läs mer om [hur neural maskinöversättning fungerar](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Språkanpassning

Den anpassade översättningstjänsten är ett tillägg till Microsofts grundläggande översättningstjänst. Den kan användas tillsammans med Translator Text API för att anpassa det neutrala översättningssystemet, så att specifik terminologi och stil översätts bättre.

Med den anpassade översättningstjänsten kan du skapa översättningssystem som hanterar den terminologi som används inom företaget eller branschen. Det anpassade översättningssystemet är enkelt att integrera i befintliga program, arbetsflöden och webbplatser och på olika typer av enheter, via vanliga Microsoft Translator Text API, med hjälp av kategoriparametern.

Läs mer om [språkanpassning](customization.md)

## <a name="next-steps"></a>Nästa steg

- [Registrera dig ](translator-text-how-to-signup.md) och få en åtkomstnyckel.
- [API-referensen](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) innehåller den tekniska dokumentationen för API:erna.
- [Pris information](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
