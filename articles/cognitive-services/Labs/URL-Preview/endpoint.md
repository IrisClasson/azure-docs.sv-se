---
title: Slutpunkten för projekt-URL-förhandsgranskning
titlesuffix: Azure Cognitive Services
description: Sammanfattning av URL-förhandsgranskning-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 43254db734a48f3e7aaa5a26a7fbf3981c9e9d87
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592870"
---
# <a name="project-url-preview-endpoint"></a>Slutpunkten för projekt-URL-förhandsgranskning

URL: en förhandsversion API innehåller en slutpunkt.

## <a name="endpoint"></a>Slutpunkt
För att få en URL-förhandsgranskning kan du skicka en begäran till följande slutpunkt. Använd rubriker och URL-parametrar för andra specifikationer.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Frågeparametrar
|Namn|Värde|Type|Obligatoriskt|  
|----------|-----------|----------|--------------|  
|frågor och|URL: en för att förhandsgranska|String |Ja|
|säker sökning|Ogiltig vuxet innehåll eller oäkta innehåll har blockerats med felkoden 400, och *isFamilyFriendly* flaggan returneras inte. <p>Är det som gäller för juridiska vuxna nedan. Statuskod returnerar 200, och *isFamilyFriendly* flaggan är inställd på false.<ul><li>safeSearch = strikt: Rubrik, beskrivning, URL: en och avbildning returneras inte.</li><li>safeSearch = måttlig; Hämta rubrik, URL: en och beskrivning, men inte beskrivande avbildningen.</li><li>safeSearch =. Få svar objekt/allt – namn, URL: en, beskrivning, bild.</li></ul> |String|Krävs inte. </br> Som standard safeSearch = strikt.| 

## <a name="response-object"></a>Svarsobjekt

Svaret innehåller HTTP-huvuden och webbsidan objekt med attribut som visas i följande exempel: `name`, `url`, `description`, `isFamilyFriendly`, och `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
