---
title: inkludera fil
description: inkludera fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279557"
---
Anger hur många funktions anrop som ska aggregeras när du [beräknar mått för Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Egenskap |Default  | Beskrivning |
|---------|---------|---------| 
|batchSize|1000|Maximalt antal begär Anden som ska aggregeras.| 
|flushTimeout|00:00:30|Maximal tids period som ska aggregeras.| 

Funktions anrop sammanställs när den första av de två gränserna nås.
