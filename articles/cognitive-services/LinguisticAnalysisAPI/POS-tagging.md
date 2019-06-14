---
title: En del av tal-märkning – API för Lingvistisk analys
description: 'Lär dig hur taggning av ordklasser i API: T för Lingvistisk analys identifierar kategori eller en del av tal i varje ord i texten.'
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 0269397b0f8da66d2bafecfb427ba705fdfff001
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394497"
---
# <a name="part-of-speech-tagging"></a>En del av tal-märkning

> [!IMPORTANT]
> Förhandsversionen av Språkanalys upphörde den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

## <a name="background-and-motivation"></a>Bakgrund och motivationen

När en text har separerats i meningar och token, är nästa steg i analysis att identifiera den kategori eller en del av tal i varje ord.
Dessa inkluderar kategorier som *substantiv* (som vanligtvis motsvarar personer, platser, saker, idéer, osv.) och *verb* (vanligtvis som representerar åtgärder, ändrar tillstånd osv. För vissa ord, en del av tal är entydiga (exempelvis *quagmire* är endast ett substantiv), men för många andra är det svårt att se.
*Tabellen* kan vara en plats där du finns (eller 2D-layout med tal), men du kan också ”tabellen en diskussion”.

## <a name="list-of-part-of-speech-tags"></a>Lista över taggar som en del av tal

| Tagg | Beskrivning | Exempel ord |
|-----|-------------|---------------|
| $ | dollar | $ |
| \`\` | inledande citattecken | \` \`\` |
| '' | avslutande citattecken | ' '' |
| ( | vänsterparentes | ( [ { |
| ) | högerparentes | ) ] } |
| , | kommaavgränsad | , |
| -- | streck | -- |
| . | mening Begränsare | . ! ? |
| : | kolon eller ellipsen | : ; ... |
| CC | tillsammans, samordna | och men eller ännu|
| CD | siffror, väsentliga | nio 20 1980 ' 96 |
| DT | determiner |en på en alla båda varken|
| T.EX. | existentiell där | Det |
| VB | främmande ord | enfant terrible hoi polloi je ne sais quoi |
| IN | preposition eller underordna tillsammans| i inuti om vid om |
| JJ | adjektiv eller siffror, ordningstal | nionde ganska execrable multimodal |
| JJR | adjektiv, jämförande | bättre snabbare billigare |
| JJS | adjektiv, superlative | bästa snabbaste billigaste |
| LS | lista över objekt markör | (a) (b) 1 2 A B A. B. |
| MD | modal assistent | kan kan skall kommer kunde kanske bör borde |
| NN | substantiv, vanliga, rapportanvändare eller lagringsenheter | ljus pengar sko |
| NNP | substantiv rätt, rapportanvändare | Kennedy Roosevelt Chicago Weehauken |
| NNPS | substantiv rätt, plural | Springfields buskar |
| NNS | substantiv vanliga, plural | delar möss fält |
| (stillahavstid) | pre-determiner | alla båda hälften många ganska sådana till detta |
| POS | genitive markör | ' 's |
| PRP | pronomen, personliga | hon han det jag vi de du |
| PRP$ | pronomen, Possessiv | hennes hans dess min vår sina din |
| RB | adverb | kliniskt endast |
| RBR | ett adverb jämförande | ytterligare gloomier grander graver större grimmer svårare harsher sundare tyngre högre men större senare mer slimmade längre mindre perfekt mindre lonelier längre starkare lägre mer... |
| RBS | ett adverb superlative | bästa största bluntest tidigaste längst bort första längst svåraste heartiest högsta största minst mindre de flesta närmaste andra hårdaste sämsta |
| RP | Particle | på av uppåt reda på om |
| SYM | symbol | % & |
| ATT | ”till” som preposition eller infinitive markör | till |
| UH | interjection | hoppsan hurra howdy hello |
| VB | verb, basformulär | ge tilldela direkt |
| VBD | verb, tidigare Tempus | gav tilldelade reste |
| VBG | verb, finns participle eller gerund | ge tilldela flyga |
| VBN | verb, tidigare participle | beroende tilldelade leds |
| VBP | verb, presens, inte 3 person rapportanvändare | ge tilldela direkt |
| VBZ | verb, presens, 3 person rapportanvändare | ger tilldelar sig |
| WDT | WH-determiner | som vilken som |
| WP | WH-pronoun | vem som |
| WP$ | Vad pronomen Possessiv | vars |
| WRB | Wh-adverb | hur men när det är där |

## <a name="specification"></a>Specifikationen

För tokenisering, vi förlitar sig på specifikation från den [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42).
