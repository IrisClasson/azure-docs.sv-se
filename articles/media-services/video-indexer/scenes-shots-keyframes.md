---
title: Video Indexer-scener, skärmbilder och nyckelrutor - Azure
titlesuffix: Azure Media Services
description: Det här avsnittet ger en översikt över Video Indexer-scener, skärmbilder och nyckelrutor.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d312a93f83ef38fa1ae855a1e313280fc608948d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799407"
---
# <a name="scenes-shots-and-keyframes"></a>Scener, klipp och nyckelbilder

Video Indexer stöder segmentera videor i den temporala enheter baserat på strukturerad och semantisk egenskaper. Den här funktionen kan kunder enkelt bläddra, hantera och redigera deras videoinnehåll baserat på olika precision. Till exempel baserat på scener, skärmbilder och nyckelbildrutor, som beskrivs i det här avsnittet. Den **scenidentifiering** funktionen förhandsvisas just nu.   

![Scener, klipp och nyckelbilder](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Scen ansikten (Förhandsgranska)

Video Indexer avgör när en scen ändras i videon baserat på visuella tips. En scen visar en enskild händelse och den består av ett antal på varandra följande skärmbilder som semantiskt är relaterade. En scen miniatyr är den första bildrutan av dess underliggande som visar. Video indexer segment en video i scener utifrån färg samstämmighet mellan på varandra följande skärmbilder och hämtar början och i sluttid för varje scen. Scenidentifiering av anses vara en utmaning eftersom den innebär att kvantifiera semantiska aspekter av videor.

> [!NOTE]
> Gäller för videor som innehåller minst 3 scener.

## <a name="shot-detection"></a>Bildidentifiering

Video Indexer avgör när en som ändras i videon baserat på visuella tips genom att spåra både kraftig och gradvis övergångar i färgschemat för intilliggande bildrutor. På nedan metadata innehåller en start- och sluttid samt listan över nyckelrutor som ingår i den som visar. Skärmbilder är bildrutor som kommer från samma kameran på samma gång.

## <a name="keyframe-detection"></a>Nyckelbildsigenkänning

Väljer bildruta som bäst representerar på nedan. Nyckelbildrutor är representativt bildrutorna valt från hela videon baserat på estetiska egenskaper (till exempel kontrast och stableness). Video Indexer hämtar en lista över bildrutan ID: N som en del av på nedan metadata, baserat på vilken kunder kan extrahera bildrutan miniatyrbilden. 

Nyckelrutor är associerade med skärmbilder i JSON-utdata. 

## <a name="next-steps"></a>Nästa steg

[Granska Video Indexer-utdata som genereras av API: et](video-indexer-output-json-v2.md#scenes)