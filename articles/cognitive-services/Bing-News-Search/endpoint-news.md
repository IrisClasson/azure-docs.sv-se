---
title: Slutpunkt för nyhetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Sammanfattning av nyheter search API-slutpunkt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 6ea218da23d65696c76008cb15e183fcc4bbda10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66383232"
---
# <a name="bing-news-search-api-endpoints"></a>Nyhetssökning i Bing-slutpunkter

Den **nyheter Search API** returnerar nyheter artiklar, webbsidor, bilder, videor, och [entiteter](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteter innehåller översiktsinformation om en person, plats eller ett ämne.

## <a name="endpoints"></a>Slutpunkter

För att få nyheter sökresultat med hjälp av den nyhetssökning i Bing kan skicka en `GET` begäran till någon av följande slutpunkter. Rubriker och URL-parametrar kan definiera ytterligare specifikationer.

### <a name="news-items-by-search-query"></a>Nyhetsartiklar av sökfråga

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Returnerar nyhetsobjekt baserat på en sökfråga. Om frågan är tom, returnerar API: et de senaste artiklarna från olika kategorier. Skicka en fråga efter url kodning sökordet och lägga till den till den`q=""` parametern. Tillgänglighet, se [länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>De senaste objekt efter kategori

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Returnerar de senaste artiklarna efter kategori. Mer specifikt kan du begära översta företag, sport och underhållning artiklar med `category=business`, `category=sports`, eller `category=entertainment`.  Den `category` parametern kan bara användas med den `/news` URL: en. Det finns vissa formella krav för att ange kategorier. referera till `category` i den [frågeparameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) dokumentation. Skicka en fråga efter url kodning sökordet och lägga till den till den`q=""` parametern. Tillgänglighet, se [länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Populära nyhetsämnen 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Returnerar nyhetsämnen som för närvarande är populära på sociala nätverk. När den `/trendingtopics` alternativet ingår, Bing search ignorerar flera andra parametrar, till exempel `freshness` och `?q=""`. Tillgänglighet, se [länder/regioner och marknader som stöds](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Nästa steg

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [nyhetssökning i Bing API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) referens.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med API för nyhetssökning finns [Sök på Bing News Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
