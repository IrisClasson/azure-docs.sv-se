---
title: Vad är Custom Translator?
titleSuffix: Azure Cognitive Services
description: Custom Translator har liknande funktioner som Microsoft Translator Hub utför för statistisk maskinöversättning, men exklusivt för NMT-system (Neural Machine Translation – neural maskinöversättning).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d78767474150bc9571b25fe1f26135d6f41d1f20
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961242"
---
# <a name="what-is-custom-translator"></a>Vad är Custom Translator?

[Anpassad översättare](https://portal.customtranslator.azure.ai) är en funktion i tjänsten Translator, som gör det möjligt för företag, appar utvecklare och språk tjänst leverantörer att bygga anpassade NMT-system (neurala Machine Translation). De anpassade översättningssystemen integreras sömlöst i befintliga program, arbetsflöden och webbplatser. 

Översättnings system som skapats med [anpassad översättare](https://portal.customtranslator.azure.ai) är tillgängliga via samma molnbaserade, [säkra](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), högpresterande, mycket skalbara [Azure Cognitive Services Translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), som ger en miljard av översättningar varje dag.

Custom Translator stöder mer än tre dussin språk och mappas direkt till språken som är tillgängliga för neural maskinöversättning. En fullständig lista finns i [Translator-språk](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Funktioner

Custom Translator har olika funktioner för att skapa ett anpassat översättningssystem och därefter använda det.

|Funktion  |Beskrivning  |
|---------|---------|
|[Utnyttja neural maskinöversättningsteknik](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Förbättra översättningen genom att utnyttja neural maskinöversättning (NMT) som tillhandahålls av Custom Translator.       |
|[Skapa system som känner till din affärsterminologi](what-are-parallel-documents.md)     |  Anpassa och skapa översättningssystem med hjälp av parallella dokument, som förstår den terminologi som används i din egen verksamhet och bransch.       |
|[Använda ordlista till att skapa dina modeller](what-is-dictionary.md)     |   Om du inte har angett träningsdata kan du träna en modell med bara ordlistedata.       |
|[Samarbeta med andra](how-to-manage-settings.md#share-your-workspace)     |   Samarbeta med teamet genom att dela ditt arbete med olika personer.     |
|[Få åtkomst till din anpassade översättningsmodell](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Din anpassade översättnings modell kan nås när som helst av dina befintliga program/program via Translator v3.       |

## <a name="get-better-translations"></a>Få bättre översättningar

Translator släppte [neurala Machine Translation (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) i 2016. NMT innebär stora framsteg i översättningskvaliteten jämfört med standardtekniken inom översättningsindustrin, [statistisk maskinöversättning](https://en.wikipedia.org/wiki/Statistical_machine_translation). Eftersom NMT fångar kontexten för fullständiga meningar på ett bättre sätt innan de översätts ger tekniken översättningar med högre kvalitet som låter mer naturligt och är mer flytande. Med [Custom Translator](https://portal.customtranslator.azure.ai) får du NMT för dina anpassade modeller, vilket ger bättre översättningskvalitet.

Du kan använda tidigare översatta dokument och bygga ett översättningssystem. De här dokumenten innehåller en domän bestämd terminologi och stil, bättre än ett standard översättnings system. Användare kan ladda upp ALIGN-, PDF-, LCL-, HTML-, HTM-, XLF-, TMX-, XLIFF-, TXT-, DOCX- och XLSX-dokument.

Custom Translator accepterar även data som är parallella på dokumentnivå för att göra datainsamling och -förberedelse effektivare. Om användare har åtkomst till versioner med samma innehåll i flera språk men i separata dokument kan Custom Translator automatiskt matcha meningar mellan olika dokument.

Om den lämpliga typen och mängden träningsdata anges är det inte ovanligt att se ett ökat [BLEU-resultat](what-is-bleu-score.md) mellan 5 och 10 poäng genom att använda Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Var produktiv och kostnadseffektiv

Med [Custom Translator](https://portal.customtranslator.azure.ai) kräver träning och distribution av ett anpassat system inte några programmeringskunskaper.

Med hjälp av den säkra [Custom Translator](https://portal.customtranslator.azure.ai)-portalen kan användare ladda upp träningsdata, träna system, testa system och distribuera dem till en produktionsmiljö via ett intuitivt användargränssnitt. Systemet är sedan tillgängligt för användning skalanpassat inom några timmar (faktisk tid beror på storleken på träningsdata).

[Custom Translator](https://portal.customtranslator.azure.ai) kan även användas programmatiskt via ett [dedikerat API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (för närvarande en förhandsversion). Med API:et kan användare hantera skapandet eller uppdateringen av träning regelbundet via en egen app eller webbtjänst.

Kostnaden för att använda en anpassad modell för att översätta innehåll baseras på användarens pris nivå för översättare. Se [pris webb sidan](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) för Cognitive Services Translator för pris nivå information.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Översätt säkert när som helst, var som helst på alla dina appar och tjänster

Anpassade system kan nås sömlöst och integreras i alla produkter och affärs arbets flöden och på alla enheter via Translator via standard REST-teknik.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Med [Snabbstart](quickstart-build-deploy-custom-model.md) lär du dig att skapa en översättningsmodell i Custom Translator.
