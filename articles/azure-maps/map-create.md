---
title: Skapa en karta med Azure Maps | Microsoft Docs
description: Så här skapar du en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108607"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln beskrivs olika sätt att skapa en karta och animera en karta.  

## <a name="understand-the-code"></a>Förstå koden

Det finns två sätt som du kan skapa en karta. Du kan ange kameran på kartan genom att ange mittpunkt zoomningsnivån eller du kan ange kamera gränser på kartan genom att ange sydväst och nordöst avgränsar punkter.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kameran

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta via `CameraOptions` </a>av Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) har skapats `new atlas.Map()` och ställs centrering och zoomning. Egenskaper för kartan, till exempel centrering och zoomning nivå är en del av [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange gränser för kamera

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta via `CameraBoundsOptions` </a>genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) skapas `new atlas.Map()`. Egenskaper för kartans som `CameraBoundsOptions` kan definieras [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) funktion i klassen kartan. Gränser och utfyllnadsegenskaper anges med `setCamera`.

### <a name="animate-map-view"></a>Animera kartvyn

<iframe height='500' scrolling='no' title='Animera kartvyn' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animera kartvyn</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket skapar en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. Egenskaper för kartan, till exempel centrering och zoomning nivå är en del av [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` Du kan definiera i konstruktorn kartan eller via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) funktion i klassen kartan. Den [mappa style](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) är inställd på `road`.

Andra kodblocket skapar en funktion för animera kartan som animeras ändring i kartvyn genom att definiera [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) funktion. Funktionen utlöses av knappen animera kartan för att generera en slumpmässig zoomnivå vid varje gång du klickar.

## <a name="try-out-the-code"></a>Prova att använda koden

Ta en titt på exempelkoden ovan. Du kan redigera JavaScript-kod på den **JS fliken** till vänster och se kartvyn ändringar på den **resultatet fliken** till höger. Du kan också klicka på den **Redigera på CodePen** knappen och redigera koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se kodexempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Välj kartan format](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägg till karta kontroller](map-add-controls.md)
