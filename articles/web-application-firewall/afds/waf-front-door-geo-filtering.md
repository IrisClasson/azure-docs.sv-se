---
title: Geo-filtrering på en domän för Azure-tjänsten för front dörr
description: I den här artikeln får du lära dig om geofiltreringprinciper för Azure Front Door Service
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 7c49892f97d9c15efcaecccb6133c67133e81c87
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137568"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>Vad är geo-filtrering på en domän för Azures front dörr?

Som standard svarar Azure Front Door Service på användarnas begäranden oavsett var användaren som skickar begäran finns. Men i vissa fall kanske du vill begränsa åtkomsten till dina webb program efter land/region. Med brand vägg för webbaserade program (WAF) i front dörren kan du definiera en princip med hjälp av anpassade åtkomst regler för en viss sökväg på slut punkten för att tillåta eller blockera åtkomst från angivna länder/regioner. 

En WAF-princip innehåller vanligt vis en uppsättning anpassade regler. En regel består av matchningsvillkor, en åtgärd och en prioritet. I matchningsvillkor definierar du en matchningsvariabel, en operator och ett matchningsvärde.  För en geofiltreringsregel är matchningsvariabeln REMOTE_ADDR, operatorn är GeoMatch och värdet är landskoden på två bokstäver av intresse. Du kan kombinera ett GeoMatch-villkor och ett matchningsvillkor för REQUEST_URI-sträng för att skapa en sökvägsbaserad geofiltreringsregel.

Du kan konfigurera en princip för geo-filtrering för din front dörr med hjälp av Azure Portal, [Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) eller vår [snabb starts mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Lands kods referens

|Landskod | Lands namn |
| ----- | ----- |
| AD | Andorra |
| AE | Förenade Arabemiraten|
| AF | Afghanistan|
| AG | Antigua och Barbuda|
| AL | Albanien|
| AM | Armenien|
| AO | Angola|
| AR | Argentina|
| AS | Amerikanska Samoa|
| AT | Österrike|
| AU | Australien|
| AZ | Azerbajdzjan|
| BA | Bosnien och Hercegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Belgien|
| BF | Burkina Faso|
| BG | Bulgarien|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | Sankt Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Vitryssland|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratiska republiken Kongo|
| CF | Centralafrikanska republiken|
| CH | Schweiz|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Kina|
| CO | Colombia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Kap Verde|
| CY | Cypern|
| CZ | Tjeckien|
| DE | Tyskland|
| DK | Danmark|
| DO | Dominikanska republiken|
| DZ | Algeriet|
| EC | Ecuador|
| EE | Estland|
| EG | Egypten|
| ES | Spanien|
| ET | Etiopien|
| FI | Finland|
| FJ | Fiji|
| FM | Mikronesien|
| FR | Frankrike|
| GB | Storbritannien|
| GE | Georgien|
| GF | Franska Guyana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Grekland|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong SAR|
| HN | Honduras|
| HR | Kroatien|
| HT | Haiti|
| HU | Ungern|
| ID | Indonesien|
| IE | Irland|
| IL | Israel|
| IN | Indien|
| IQ | Irak|
| IR | Islamiska republiken Iran|
| IS | Island|
| IT | Italien|
| JM | Jamaica|
| JO | Jordanien|
| JP | Japan|
| KE | Kenya|
| KG | Kirgizistan|
| KH | Kambodja|
| KI | Kiribati|
| KN | Saint Kitts och Nevis|
| KP | Nordkorea|
| KR | Sydkorea|
| KW | Kuwait|
| KY | Caymanöarna|
| KZ | Kazakhstan|
| LA | Demokratiska folkrepubliken Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litauen|
| LU | Luxemburg|
| LV | Lettland|
| LY | Libyen |
| MA | Marocko|
| MD | Moldavien|
| MG | Madagaskar|
| MK | Nordmakedonien|
| ML | Mali|
| MM | Myanmar|
| MN | Mongoliet|
| MO | Macao SAR|
| MQ | Martinique|
| MR | Mauretanien|
| MT | Malta|
| MV | Maldiverna|
| MW | Malawi|
| MX | Mexiko|
| MY | Malaysia|
| MZ | Moçambique|
| Ej tillämpligt | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Nederländerna|
| NO | Norge|
| NP | Nepal|
| NR | Nauru|
| NZ | Nya Zeeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filippinerna|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Réunion|
| RO | Rumänien|
| RS | Serbien|
| RU | Ryska federationen|
| RW | Rwanda|
| SA | Saudiarabien|
| SD | Sudan|
| SE | Sverige|
| SG | Singapore|
| SI | Slovenien|
| SK | Slovakien|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sydsudan|
| SV | El Salvador|
| SY | Arabrepubliken Syrien|
| SZ | Swaziland|
| TC | Turks- och Caicosöarna|
| TG | Togo|
| TH | Thailand|
| TN | Tunisien|
| TR | Turkiet|
| TT | Trinidad och Tobago|
| TW | Taiwan|
| TZ | Tanzania|
| UA | Ukraina|
| UG | Uganda|
| USA | USA|
| UY | Uruguay|
| UZ | Uzbekistan|
| VC | Saint Vincent och Grenadinerna|
| VE | Venezuela|
| VG | Jungfruöarna, Storbritannien|
| VI | Jungfruöarna, USA|
| VN | Vietnam|
| ZA | Sydafrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [säkerhet på programnivå med Front Door](../../frontdoor/front-door-application-security.md).
- Läs hur du [skapar en Front Door](../../frontdoor/quickstart-create-front-door.md).
