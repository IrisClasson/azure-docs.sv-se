---
title: Modul för rit verktyg | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du ställer in ritnings alternativ data med hjälp av Microsoft Azure Maps-webbsdk
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 4c00639c02f8834a24a3828ac5b8ba828d0e1733
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282831"
---
# <a name="use-the-drawing-tools-module"></a>Använda modulen för ritningsverktyg

Azure Maps Web SDK tillhandahåller en *modul för rit verktyg*. Den här modulen gör det enkelt att rita och redigera former på kartan med en indataenhet, till exempel en mus eller touch-skärm. Huvud klassen för den här modulen är [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Ritnings hanteraren innehåller alla funktioner som behövs för att rita och redigera former på kartan. Den kan användas direkt och är integrerad med ett anpassat verktygsfälts gränssnitt. Du kan också använda den inbyggda verktygs klassen [Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) . 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Läsa in modulen för rit verktyg på en webb sida

1. Skapa en ny HTML-fil och [implementera kartan som vanligt](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Läs in modulen Azure Maps Drawing tools. Du kan läsa in den på ett av två sätt:
    - Använd den globalt värdbaserade Azure Content Delivery Network-versionen av modulen Azure Maps tjänster. Lägg till referens i Java Script och CSS-formatmallar i `<head>` filens element:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Du kan också läsa in modulen för rit verktyg för att Azure Maps Web SDK-källkod lokalt genom att använda NPM-paketet [Azure-Maps-Drawing tools](https://www.npmjs.com/package/azure-maps-drawing-tools) och sedan vara värd för det med din app. Det här paketet innehåller även TypeScript-definitioner. Använd det här kommandot:
    
        > **NPM installera Azure-Maps-Draw-tools**
    
        Lägg sedan till en referens till JavaScript-och CSS-formatmallar i `<head>` filens element:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Använda Drawing Manager direkt

När modulen Drawing Tools har lästs in i ditt program kan du aktivera funktioner för att rita och redigera med [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Du kan ange alternativ för ritnings hanteraren när du instansierar den eller också använder du `drawingManager.setOptions()` funktionen.

### <a name="set-the-drawing-mode"></a>Ange rit läge

Följande kod skapar en instans av Drawing Manager och anger alternativet för rit **läge** . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Koden nedan är ett komplett exempel på hur du ställer in ett ritläge i Drawing Manager. Klicka på kartan för att börja rita en polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rita en polygon" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan genom att <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Rita en polygon</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Ange interaktions typ

Drawing Manager har stöd för tre olika sätt att interagera med kartan för att rita former.

* `click`-Koordinater läggs till när musen eller touchen klickas.
* `freehand `-Koordinater läggs till när musen eller touchen dras på kartan. 
* `hybrid`-Koordinater läggs till när musen eller touchen klickas eller dras.

Följande kod aktiverar polygonens ritläge och anger vilken typ av ritnings interaktion som ritnings hanteraren ska följa `freehand` . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Det här kod exemplet implementerar funktionerna i att rita en polygon på kartan. Håll bara ned den vänstra mus knappen och dra den runt, fritt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kostnads fri ritning" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se rit <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>fri hands ritningen</a> med Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Anpassa ritnings alternativ

I föregående exempel visas hur du anpassar ritnings alternativ när du instansierar ritnings hanteraren. Du kan också ange alternativ för Drawing Manager med hjälp av `drawingManager.setOptions()` funktionen. Nedan finns ett verktyg för att testa anpassningen av alla alternativ för Drawing Manager med hjälp av funktionen setOptions.

<br/>

<iframe height="685" title="Anpassa Drawing Manager" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Se hur pennan <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>får form data</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Lägga till ett verktygsfält för ritning](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagera på rithändelser](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
