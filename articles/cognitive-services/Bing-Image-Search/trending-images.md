---
title: Hämta trend bilder med API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Sök efter dagens trendbaserade bilder från webben med API för bildsökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2936b94d7ba791b1a4e5a9b95aca3ca3ecdb5904
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "66383439"
---
# <a name="get-trending-images-from-the-web"></a>Hämta trend bilder från webben

Skicka följande GET-begäran för att hämta dagens trendbaserade bilder:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

API: et för Trends images stöder för närvarande endast följande marknader:  

- en-US (engelska, USA)  
- en-CA (engelska, Kanada)  
- en – AU (engelska, Australien)  
- zh-CN (kinesiska, Kina)

Svaret innehåller ett [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) -objekt som visar bilder efter kategori. Använd kategorins `title` för att gruppera bilderna i din användar upplevelse. Kategorierna kan ändras varje dag.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Varje panel innehåller en bild och alternativ för att hämta relaterade bilder. Om du vill hämta de relaterade avbildningarna kan du använda `text` frågan för att anropa [bildsökning-API: et](./search-the-web.md) och Visa de relaterade avbildningarna själv. Eller så kan du använda webb adressen i `webSearchUrl` för att ta användaren till Bing-sidan med bilder Sök resultat, som innehåller relaterade bilder.

Om du anropar [bildsökning-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) : et för att hämta de relaterade avbildningarna anger du ID-FRÅGEPARAMETERN till `id` ID i fältet. Genom att ange ID: t ser du till att svaret innehåller avbildningen (det är den första bilden i svaret) och dess relaterade avbildningar. Ställ också in [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) -Frågeparametern på texten i `query` objektets `text` fält.

I följande exempel visas hur du använder bild-ID: t för att hämta relaterade bilder av Mr. Smith i föregående API-svar för Trends bilder.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
