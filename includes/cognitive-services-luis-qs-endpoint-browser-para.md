---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 67c95ffcdbdbcfbb9a86e15c91d984953d7bbffc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187656"
---
Om du vill förstå vad som returneras från en LUIS-slutpunkt för förutsägelser kan du granska resultatet i en webbläsare. Om du vill köra frågor mot en offentlig app behöver du en egen nyckel och appens id. Den offentliga IoT-appens id, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, ingår i webbadressen i steg ett.

Formatet för webbadressen för en **GET**-slutpunktsförfrågan är:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Slutpunkten för den offentliga IoT-appen har följande format: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Kopiera webbadressen och ersätt värdet `<YOUR_KEY>` med din nyckel.

2. Klistra in URL:en i ett webbläsarfönster och tryck på Retur. Du ser ett JSON-resultat i webbläsaren som indikerar att LUIS har identifierat avsikten `HomeAutomation.TurnOn` som troligaste avsikt och entiteten `HomeAutomation.Room` med värdet `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Ändra värdet för parametern `q=` i URL: en till `turn off the living room light`, och tryck på Retur. Resultatet visar nu att LUIS identifierat avsikten `HomeAutomation.TurnOff` som troligaste avsikt och entiteten `HomeAutomation.Room` med värdet `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
