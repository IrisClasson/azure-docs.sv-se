---
title: 'Snabb start: utföra en nyhets sökning med SDK för python – Nyhetssökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter nyheter med SDK för nyhetssökning i Bing för Python och bearbeta svaret.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b84b5ee8682007191953bef34579973c7c24ca45
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448520"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Snabb start: utföra en nyhets sökning med Nyhetssökning i Bing SDK för python

Använd den här snabbstarten om du vill börja söka efter nyheter med SDK för nyhetssökning i Bing för Python. Även om Nyhetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Krav

* [Python](https://www.python.org/) 2.x eller 3.x

Vi rekommenderar att du använder en [virtuell miljö](https://docs.python.org/3/tutorial/venv.html) för din Python-utveckling. Du kan installera och initiera den virtuella miljön med [venv-modulen](https://pypi.python.org/pypi/virtualenv). Du måste installera en virtualenv för Python 2.7. Du kan skapa en virtuell miljö med:

```console
python -m venv mytestenv
```

Du kan installera beroendena för SDK för Nyhetssökning i Bing med det här kommandot:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och importera följande bibliotek. Skapa en variabel för din prenumerationsnyckel eller sökterm.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Initiera klienten och skicka en begäran

1. Skapa en instans av `CognitiveServicesCredentials`. Instansiera klienten:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Skicka en sökfråga till API:et för nyhetssökning i Bing och lagra svaren.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Parsa svaret

Om några sökresultat hittas skriver du ut resultatet på den första webbsidan:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-news-search-single-page-app.md)
