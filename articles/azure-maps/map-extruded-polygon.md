---
title: Lägg till ett polygon extrusion-lager till en karta | Microsoft Azure Maps
description: Så här lägger du till ett polygon extrusion-lager i Microsoft Azure Maps-webbsdk.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: cd12242367c6f3b5b2c64df28f7f5e52cf1a4f97
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282882"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Lägg till ett polygon extrusion-lager på kartan

Den här artikeln visar hur du använder polygon extrusion-lagret för att återge områden i `Polygon` och `MultiPolygon` funktions Geometries som extruderade former. Azure Maps Web SDK stöder åter givning av cirkel-Geometries som definierats i det [utökade INTERjson-schemat](extend-geojson.md#circle). Dessa cirklar kan omvandlas till polygoner när de återges på kartan. Alla funktioner i Geometries kan uppdateras enkelt vid omslutning med [atlasen. Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) -klass.

## <a name="use-a-polygon-extrusion-layer"></a>Använda ett polygon extrusion-lager

Anslut [polygon extrusion-skiktet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) till en data källa. Sedan läser du in den på kartan. Polygon extrusion-lagret återger områdena i en- `Polygon` och- `MultiPolygon` funktioner som extruderade former. `height`Egenskaperna och `base` för polygonens extrusion-lager definierar bas avståndet från den malda formens och höjden i **meter**. Följande kod visar hur du skapar en polygon, lägger till den i en data källa och återger den med hjälp av skikt klassen polygon extrusion.

> [!Note]
> `base`Värdet som definieras i polygon extrusion-lagret ska vara mindre än eller lika med för `height` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extruderad polygon" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>förskjutande polygon</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Lägg till data drivna polygoner

En choropleth-karta kan återges med hjälp av polygon extrusion-lagret. Ange `height` egenskaperna och `fillColor` för extrusion-lagrets mått för den statistiska variabeln i `Polygon` `MultiPolygon` funktionen och Geometries. I följande kod exempel visas en extruderad choropleth karta för U. S baserat på måttet för populationens densitet per tillstånd.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Karta över extruderad choropleth" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/eYYYNox'>choropleth-kartan</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Lägg till en cirkel till kartan

Azure Maps använder en utökad version av det injson-schema som tillhandahåller en definition för cirklar som anges [här](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). En extruderad cirkel kan återges på kartan genom att skapa en `point` funktion med en `subType` egenskap av `Circle` och en numrerad `Radius` egenskap som representerar radien i **meter**. Till exempel:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK konverterar dessa `Point` funktioner till `Polygon` funktioner under huven. Dessa `Point` funktioner kan återges på kartan med polygon extrusion-skiktet som visas i följande kod exempel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drönare luft rummets polygon" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polygon drönare-luft rummets polygon</a> med Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Anpassa ett polygon extrusion-lager

Polygon extrusion-lagret har flera format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> av Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Polygonlasso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygon extrusion-lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Ytterligare resurser:

> [!div class="nextstepaction"]
> [Tillägg för Azure Mapsal JSON-specifikation](extend-geojson.md#circle)
