---
title: Rendera täckning i Azure Maps | Microsoft Docs
description: Lär dig mer om rendering täckning i Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 536a74046f46c7f83907833846e9ec99e8d8a289
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370286"
---
# <a name="azure-maps-render-coverage"></a>Azure Maps rendera täckning

Azure Maps använder både raster paneler och vektorkartbilder för att skapa kartor. Med dess lägsta upplösning som är i hela världen som får plats på en panel. På den högsta upplösningen representerar en enskild panel 38 kvadratmeter. När du zoomar in på en karta se du därför allt mer information om kontinenter, regioner, orter och enskilda gator. Mer information finns i [zoomningsnivåer och rutnät](zoom-levels-and-tile-grid.md).

Maps har dock inte samma grad av information och Precision för alla regioner. Följande tabeller innehåller information om vilken detaljnivå återgivna som ingår i varje region.

## <a name="legend"></a>Teckenförklaring

| Symbol | Betydelse |
|--------|---------|
| ✓ | Region representeras med detaljerade data.   |
| Ø | Region representeras med förenklad data. |


## <a name="africa"></a>Afrika 


| Land/region | Raster Tiles Unified | Vektorkartbilder Unified |
| ------ | :------------------: | :------------------: |
| Algeriet                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Centralafrikanska republiken         | ✓ | Ø |
| Tchad                             | ✓ | Ø |
| Komorerna                          | ✓ | Ø |
| Republiken Kongo                            | ✓ | ✓ |
| Demokratiska republiken Kongo | ✓ | ✓ |
| Côte d'Ivoire                    | ✓ | Ø |
| Djibouti                         | ✓ | Ø |
| Egypten                            | ✓ | ✓ |
| Ekvatoralguinea                | ✓ | Ø |
| Eritrea                          | ✓ | Ø |
| Etiopien                         | ✓ | Ø |
| Gabon                            | ✓ | ✓ |
| Gambia                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ø |
| Guinea-Bissau                    | ✓ | Ø |
| Kenya                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | Ø |
| Libyen                            | ✓ | Ø |
| Madagaskar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauretanien                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marocko                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Sankta Helena, Ascension och Tristan da Cunha | ✓ | Ø |
| São Tomé och Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Sydafrika                     | ✓ | ✓ |
| Sydsudan                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Swaziland                        | ✓ | ✓ |
| Förenade republiken Tanzania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisien                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Nord- och Sydamerika

| Land/region | Raster Tiles Unified | Vektorkartbilder Unified |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua och Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   | ✓ | ✓ |
| Mångnationella staten Bolivia | ✓ | ✓ |
| Bonaire, Sint Eustatius och Saba | ✓ | ✓ |
| Brasilien                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Caymanöarna            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| $Clippertonön         | ✓ | ✓ |
| Colombia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| Dominikanska republiken        | ✓ | ✓ |
| Ecuador                   | ✓ | ✓ |
| Falklandsöarna | ✓ | ✓ |
| Franska Guyana             | ✓ | ✓ |
| Grönland (Danmark)                 | ✓ | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guyana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinique                | ✓ | ✓ |
| Mexiko                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Nordmarianerna  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Puerto Rico               | ✓ | ✓ |
| Quebec (Canada)           | ✓ | ✓ |
| Saint Barthélemy          | ✓ | ✓ |
| Saint Kitts och Nevis     | ✓ | ✓ |
| Saint Lucia               | ✓ | ✓ |
| Saint Martin (franska)     | ✓ | ✓ |
| Saint Pierre och Miquelon | ✓ | ✓ |
| Saint Vincent och Grenadinerna | ✓ | ✓ |
| Sint Maarten (nederländska)      | ✓ | ✓ |
| Sydgeorgien och Sydsandwichöarna | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trinidad och Tobago       | ✓ | ✓ |
| Turks- och Caicosöarna  | ✓ | ✓ |
| USA             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Jungfruöarna, Storbritannien   | ✓ | ✓ |
| Jungfruöarna, USA      | ✓ | ✓ |

## <a name="asia"></a>Asien 

| Land/region | Raster Tiles Unified | Vektorkartbilder Unified |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahrain                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bhutan                    |   | Ø |
| Brittiska territoriet i Indiska oceanen |   | Ø |
| Brunei                    | ✓ | ✓ |
| Kambodja                  |   | Ø |
| Kina                     |   | Ø |
| Kokosöarna   |   | Ø |
| Demokratiska folkrepubliken Korea |   | Ø |
| Dokdo och Takeshima       |   | Ø |
| Hongkong SAR                 | ✓ | ✓ |
| Indien                     | Ø | ✓ | 
| Indonesien                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Irak                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japan                     |   | Ø |
| Jordanien                    | ✓ | ✓ |
| Kazakhstan                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Kirgizistan                |   | Ø |
| Demokratiska folkrepubliken Laos |   | Ø |
| Libanon                   | ✓ | ✓ |
| Macao                     | ✓ | ✓ |
| Malaysia                  | ✓ | ✓ |
| Maldiverna                  |   | Ø |
| Mongoliet                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Filippinerna               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| Republiken Korea         | ✓ | Ø |
| Saudiarabien              | ✓ | ✓ |
| Senkaku-öarna/Diaoyutai-öarna i Oceanien och Västindien           |   | ✓ |
| Singapore                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Arabrepubliken Syrien      |   | Ø |
| Taiwan (Taiwan)                    | ✓ | ✓ |
| Tadzjikistan                |   | Ø |
| Thailand                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkmenistan              |   | Ø |
| Förenade Arabemiraten      | ✓ | ✓ |
| Förenta staternas mindre öar |   | Ø |
| Uzbekistan                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceanien

| Land/region | Raster Tiles Unified | Vektorkartbilder Unified |
| ------ | :------------------: | :------------------: |
| Amerikanska Samoa            |   | ✓ |
| Australien                 | ✓ | ✓ |
| Cooköarna              |   | Ø |
| Fiji                      |   | Ø |
| Franska Polynesien          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshallöarna          |   | Ø |
| Mikronesien                |   | Ø |
| Nauru                     |   | Ø |
| Nya Kaledonien             |   | Ø |
| Nya Zeeland               | ✓ | ✓ |
| Niue                      |   | Ø |
| Norfolkön            |   | Ø |
| Palau                     |   | Ø |
| Papua Nya Guinea          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoaöarna                     |   | Ø |
| Salomonöarna           |   | Ø|
| Tokelauöarna                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis- och Futunaöarna         |   | Ø |


## <a name="europe"></a>Europa

| Land/region | Raster Tiles Unified | Vektorkartbilder Unified |
| ------ | :------------------: | :------------------: |
| Albanien                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenien                   | ✓ | Ø |
| Österrike                   | ✓ | ✓ |
| Azerbajdzjan                | ✓ | Ø |
| Vitryssland                   | Ø | ✓ |
| Belgien                   | ✓ | ✓ |
| Bosnien och Hercegovina        | ✓ | ✓ |
| Bulgarien                  | ✓ | ✓ |
| Kroatien                   | ✓ | ✓ |
| Cypern                    | ✓ | ✓ |
| Tjeckien            | ✓ | ✓ |
| Danmark                   | ✓ | ✓ |
| Estland                   | ✓ | ✓ |
| Färöarna             | ✓ | Ø |
| Finland                   | ✓ | ✓ |
| Frankrike                    | ✓ | ✓ |
| Georgien                   | ✓ | Ø |
| Tyskland                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grekland                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Ungern                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irland                   | ✓ | ✓ |
| Isle of Man               | ✓ | ✓ |
| Italien                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Lettland                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Litauen                 | ✓ | ✓ |
| Luxemburg                | ✓ | ✓ |
| Irland                 | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldavien                   | ✓ | ✓ |
| Monaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Nederländerna               | ✓ | ✓ |
| Norge                    | ✓ | ✓ |
| Polen                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Rumänien                   | ✓ | ✓ |
| Ryska federationen        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbien                    | ✓ | ✓ |
| Slovakien                  | ✓ | ✓ |
| Slovenien                  | ✓ | ✓ |
| Södra Kurils           | ✓ | ✓ |
| Spanien                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Sverige                    | ✓ | ✓ |
| Schweiz               | ✓ | ✓ |
| Turkiet                    | ✓ | ✓ |
| Ukraina                   | ✓ | ✓ |
| Storbritannien            | ✓ | ✓ |
| Vatikanstaten              | ✓ | ✓ |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps rendering [zoomningsnivåer och rutnät](zoom-levels-and-tile-grid.md).

Lär dig mer om den [täckningsområden för Maps routning service](routing-coverage.md). 