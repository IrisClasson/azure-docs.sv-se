---
title: Språkstöd – Translator Text API
titleSuffix: Azure Cognitive Services
description: En lista med naturligt språk som stöds av Translator Text API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: 924324b11f49a50bfb5f00e117b33c0cc572e3bb
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514989"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Stöd för språk och din region för Translator Text API

Translator Text API stöder följande språk för översättning till text. Neural maskinöversättning NMT är den nya standarden för AI-driven maskinöversättningar i hög kvalitet och är tillgänglig som standard med V3 av Translator Text API när ett neural system är tillgänglig.

[Mer information om hur maskinöversättning fungerar](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Översättning

**V2 Translator API**

> [!NOTE]
> V2 upphörde den 30 April 2018. Migrera dina program till V3 för att kunna dra nytta av nya funktioner som är tillgängliga i V3.

* Statistisk: Inga neural system är tillgänglig för det här språket.
* Neural tillgängliga: Det finns ett neural system. Använd parametern `category=generalnn` till neural systemet.
* Neural standard: Neural är standard translation system. Använd parametern `category=smt` till statistiska systemet för användning med Microsoft Translator Hub.
* Neural endast: Endast neural översättning är tillgänglig.

**V3 Translator API** V3 Translator API är neural som standard och statistiska system är bara tillgängliga när det finns inga neural system.

> [!NOTE]
> För närvarande en delmängd av språk som neural är tillgängliga i anpassade Translator och vi lägger till gradvis fler håller på att. [Visa språk som är tillgängliga i anpassade Translator](#customization).

|Språk|  Språkkod|  V2 API| V3 API|
|:-----|:-----:|:-----|:-----|
|Afrikaans| `af`    |Statistisk endast|  Neural|
|Arabiska|    `ar`    |Neural tillgängliga|  Neural|
|Bangla|    `bn`    |Neural tillgängliga|  Neural|
|Bosniska (latinsk)|   `bs`    |Neural tillgängliga|  Neural|
|Bulgariska| `bg`    |Neural tillgängliga|  Neural|
|Kantonesiska (traditionell)|   `yue`   |Statistisk endast|  Statistisk|
|Katalanska|   `ca`    |Statistisk endast|  Statistisk|
|Kinesiska, förenklad|    `zh-Hans`   |Neural standard |Neural|
|Kinesiska, traditionell|   `zh-Hant`   |Neural standard |Neural|
|Kroatiska|  `hr`    |Neural tillgängliga|  Neural|
|Tjeckiska| `cs`    |Neural tillgängliga|  Neural|
|Danska|    `da`    |Neural tillgängliga   |Neural|
|Nederländska| `nl`    |Neural tillgängliga|  Neural|
|Svenska|   `en`    |Neural tillgängliga|  Neural|
|Estniska|  `et`    |Neural tillgängliga|  Neural|
|Fijianska|    `fj`    |Statistisk endast|  Statistisk|
|Filippinska|  `fil`   |Statistisk endast|  Statistisk|
|Finska|   `fi`    |Neural tillgängliga|  Neural|
|Franska|    `fr`    |Neural tillgängliga|  Neural|
|Tyska|    `de`    |Neural tillgängliga|  Neural|
|Grekiska| `el`    |Neural tillgängliga|  Neural|
|Haitiska|    `ht`    |Statistisk endast   |Statistisk|
|Hebreiska |`he`   |Neural tillgängliga   |Neural|
|Hindi| `hi`    |Neural standard|    Neural|
|Hmong Daw| `mww`   |Statistisk endast|  Statistisk|
|Ungerska| `hu`    |Neural tillgängliga|  Neural|
|Isländska| `is`    |Endast Neural|   Neural|
|Indonesiska|    `id`    |Statistisk endast|  Statistisk|
|Italienska|   `it`    |Neural tillgängliga|  Neural|
|Japanska|  `ja`    |Neural tillgängliga|  Neural|
|Kiswahili| `sw`    |Statistisk endast|  Statistisk|
|Klingon|   `tlh`   |Statistisk endast|  Statistisk|
|Klingon (plqaD)|   `tlh-Qaak`  |Statistisk endast|  Statistisk|
|Koreanska |`ko`   |Neural tillgängliga|  Neural|
|Lettiska|   `lv`    |Neural tillgängliga|  Neural|
|Litauiska|    `lt`    |Neural tillgängliga|  Neural|
|Madagaskisk|  `mg`    |Statistisk endast|  Statistisk|
|Malajiska| `ms`    |Statistisk endast   |Statistisk|
|Maltesiska|   `mt`    |Statistisk endast|  Statistisk|
|Norska| `nb`    |Neural tillgängliga|  Neural|
|Persiska|   `fa`    |Statistisk endast|  Statistisk|
|Polska|    `pl`    |Neural tillgängliga|  Neural|
|Portugisiska|    `pt`    |Neural tillgängliga|  Neural|
|Queretaro Otomi|   `otq`   |Statistisk endast|  Statistisk|
|Rumänska|  `ro`    |Neural tillgängliga|  Neural|
|Ryska|   `ru`    |Neural tillgängliga|  Neural|
|Samoa|    `sm`    |Statistisk endast|  Statistisk|
|Serbiska (kyrillisk)|    `sr-Cyrl`   |Statistisk endast|  Statistisk|
|Serbiska (latinsk)|   `sr-Latn`   |Statistisk endast   |Statistisk|
|Slovakiska|    `sk`    |Neural tillgängliga|  Neural|
|Slovenska| `sl`    |Neural tillgängliga|  Neural|
|Spanska|   `es`    |Neural tillgängliga|  Neural|
|Svenska|   `sv`    |Neural tillgängliga   |Neural|
|Tahitian|  `ty`    |Statistisk endast|  Statistisk|
|Tamilska| `ta`    |Statistisk endast|  Statistisk|
|Telugu|    `te`    |Endast Neural|   Neural|
|Thai|  `th`    |Neural tillgängliga|  Neural|
|Tongan|    `to`    |Statistisk endast|  Statistisk|
|Turkiska|   `tr`    |Neural tillgängliga   |Neural|
|Ukrainska| `uk`    |Neural tillgängliga|  Neural|
|Urdu|  `ur`    |Statistisk endast|  Statistisk|
|Vietnamesiska|    `vi`    |Neural tillgängliga|  Neural|
|Walesiska| `cy`    |Neural tillgängliga|  Neural|
|Yucatec Maya|  `yua`   |Statistisk endast|  Statistisk|

## <a name="transliteration"></a>Translitteration

Metoden Transliterate stöder följande språk. I den ”till och från”, ”<>--” anger att språket kan vara Translittererad till från eller till någon av de skript som visas. Den ”-->” anger att språket kan bara vara Translittererad till från ett skript till en annan.

| Språk    | Språkkod | Skript | Till och från | Skript|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabiska | `ar` | Arabiska `Arab` | <--> | Latin `Latn` |
|Bangla  | `bn` | Bengali `Beng` | <--> | Latin `Latn` |
| Förenklad kinesiska | `zh-Hans` | Kinesiska, förenklad `Hans`| <--> | Latin `Latn` |
| Förenklad kinesiska | `zh-Hans` | Kinesiska, förenklad `Hans`| <--> | Kinesiska, traditionell `Hant`|
| Traditionell kinesiska | `zh-Hant` | Kinesiska, traditionell `Hant`| <--> | Latin `Latn` |
| Traditionell kinesiska | `zh-Hant` | Kinesiska, traditionell `Hant`| <--> | Kinesiska, förenklad `Hans` |
| Gujarati | `gu`  | Gujarati `Gujr` | --> | Latin `Latn` |
| Hebreiska | `he` | Hebreiska `Hebr` | <--> | Latin `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latin `Latn` |
| Japanska | `ja` | Japanska `Jpan` | <--> | Latin `Latn` |
| Kannada | `kn` | Kannada `Knda` | --> | Latin `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | --> | Latin `Latn` |
| Marathi | `mr` | Devanagari `Deva` | --> | Latin `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latin `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latin `Latn`  |
| Serbiska (kyrillisk) | `sr-Cyrl` | Cyrillic `Cyrl`  | --> | Latin `Latn` |
| Serbiska (latinsk) | `sr-Latn` | Latin `Latn` | --> | Cyrillic `Cyrl`|
| Tamilska | `ta` | Tamil `Taml` | --> | Latin `Latn` |
| Telugu | `te` | Telugu `Telu` | --> | Latin `Latn` |
| Thai | `th` | Thai `Thai` | <--> | Latin `Latn` |

## <a name="dictionary"></a>Ordlista

Ordlistan stöder följande språk till eller från engelska med hjälp av Sök- och exempelmetoder.

| Språk    | Språkkod |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
| Katalanska      | `ca`          |
| Kinesiska, förenklad      | `zh-Hans`          |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Estniska      | `et`          |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Haitiska      | `ht`          |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Ungerska      | `hu`          |
| Isländska    | `is`  |
| Indonesiska      | `id`          |
| Italienska      | `it`          |
| Japanska      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
| Malajiska      | `ms`          |
| Maltesiska      | `mt`          |
| Norska      | `nb`          |
| Persiska      | `fa`          |
| Polska      | `pl`          |
| Portugisiska      | `pt`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
| Serbiska (latinsk)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Tamilska      | `ta`          |
| Thai      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Urdu      | `ur`          |
| Vietnamesiska      | `vi`          |
| Walesiska      | `cy`          |

## <a name="detect"></a>Detect

Translator Text API identifierar alla språk som är tillgängliga för översättning och transkriberingsspråk.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programmässig åtkomst Språklista Translator Text API

Du kan hämta en lista över språk som stöds för Translator Text API-v3.0 med metoden språk. Du kan visa listan efter funktionen, språkkod samt språkets namn på engelska eller något annat språk som stöds. Den här listan uppdateras automatiskt av tjänsten Microsoft Translator när nya språk blir tillgängliga.

[Visa referensdokumentation för språk-åtgärden](reference/v3-0-languages.md)

## <a name="customization"></a>Anpassning

Följande språk är tillgängliga för anpassning till eller från engelska med [anpassad Translator](https://aka.ms/CustomTranslator).

| Språk    | Språkkod |
|:----------- |:-------------:|
| Arabiska       | `ar`          |
| Bangla      | `bn`          |
| Bosniska (latinsk)      | `bs`          |
| Bulgariska      | `bg`          |
| Kinesiska, förenklad      | `zh-Hans`          |
|Kinesiska, traditionell|   `zh-Hant`   |
| Kroatiska      | `hr`          |
| Tjeckiska      | `cs`          |
| Danska      | `da`          |
| Nederländska      | `nl`          |
| Svenska    | `en`     |
| Estniska      | `et`          |
| Finska      | `fi`          |
| Franska      | `fr`          |
| Tyska      | `de`          |
| Grekiska      | `el`          |
| Hebreiska      | `he`          |
| Hindi      | `hi`          |
| Ungerska      | `hu`          |
| Isländska | `is` |
| Indonesiska|   `id`    |
| Italienska      | `it`          |
| Japanska      | `ja`          |
|Kiswahili| `sw`    |
| Koreanska      | `ko`          |
| Lettiska      | `lv`          |
| Litauiska      | `lt`          |
|Madagaskisk|  `mg`    |
| Norska      | `nb`          |
| Polska      | `pl`          |
| Portugisiska      | `pt`          |
| Rumänska      | `ro`          |
| Ryska      | `ru`          |
|Samoa|    `sm`    |
| Serbiska (latinsk)      | `sr-Latn`          |
| Slovakiska     | `sk`          |
| Slovenska      | `sl`          |
| Spanska      | `es`          |
| Svenska      | `sv`          |
| Thai      | `th`          |
| Turkiska      | `tr`          |
| Ukrainska      | `uk`          |
| Vietnamesiska      | `vi`          |
| Walesiska | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Komma åt listan på webbplatsen Microsoft Translator

Webbplatsen Microsoft Translator visar alla språk som stöds av Translator Text och tal-API: er för en snabb titt på språk. Den här listan innehåller inte developer-specifik information, till exempel språkkoder.

[Se en lista över språk](https://www.microsoft.com/translator/languages.aspx)
