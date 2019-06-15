---
title: Avancerade aggregeringar i Azure Monitor loggfrågor | Microsoft Docs
description: Beskriver några av de mer avancerade aggregering alternativen som är tillgängliga för Azure Monitor log-frågor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 56e87da0353a41504035a070d4c10bab0dda2279
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551761"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Avancerade aggregeringar i Azure Monitor log-frågor

> [!NOTE]
> Bör du genomföra [aggregeringar i Azure Monitor frågor](./aggregations.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskriver några av de mer avancerade aggregering alternativen som är tillgängliga för Azure Monitor-frågor.

## <a name="generating-lists-and-sets"></a>Skapa listor och uppsättningar
Du kan använda `makelist` att pivotera data av ordningen värden i en viss kolumn. Du kanske vill utforska de vanligaste spelas upp ordning händelser på dina datorer. Du kan i princip pivotera data av ordningen EventIDs på varje dator. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| Dator1 | [704,701,1501,1500,1085,704,704,701] |
| Dator2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` genererar en lista i den ordning som data skickades till den. Om du vill sortera händelser från äldsta till nyaste använda `asc` i order-instruktion i stället för `desc`. 

Det är också användbart för att skapa en lista över bara distinkta värden. Detta kallas en _ange_ och kan genereras med `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| Dator1 | [704,701,1501,1500,1085] |
| Dator2 | [326,105,302,301,300,102] |
| ... | ... |

Som `makelist`, `makeset` också fungerar med sorterade data och genererar matriser baserat på ordningen de rader som har skickats till den.

## <a name="expanding-lists"></a>Utöka listor
Inverterade driften av `makelist` eller `makeset` är `mvexpand`, vilket utökar en lista med värden att avgränsa rader. Det kan expandera över valfritt antal dynamiska kolumner, såväl JSON-matris. Du kan exempelvis kontrollera den *pulsslag* tabellen för lösningar som skickar data från datorer som har skickat ett pulsslag under den senaste timmen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Lösningar | 
|--------------|----------------------|
| Dator1 | "security", "updates", "changeTracking" |
| Dator2 | "security", "updates" |
| Dator3 | "antiMalware", "changeTracking" |
| ... | ... |

Använd `mvexpand` att visa alla värden i en separat rad i stället för en CSV-lista:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Lösningar | 
|--------------|----------------------|
| Dator1 | "security" |
| Dator1 | ”uppdateringar” |
| Dator1 | "changeTracking" |
| Dator2 | "security" |
| Dator2 | ”uppdateringar” |
| Dator3 | ”program mot skadlig kod” |
| Dator3 | "changeTracking" |
| ... | ... |


Du kan sedan använda `makelist` igen objekt tillsammans att gruppera och nu se en lista över datorer per lösning:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Lösningar | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| ”uppdateringar” | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| ”program mot skadlig kod” | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hantering av lagerplatser som saknas
En användbar tillämpning av `mvexpand` uppstår ofta behovet av att fylla standardvärdena i för saknas lagerplatser. Anta exempelvis att du letar efter drifttiden för en viss dator genom att utforska dess pulsslag. Vill du också se orsaken pulsslag som finns i den _kategori_ kolumn. Normalt använder vi ett enkelt summerar instruktionen på följande sätt:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direktagent | 2017-06-06T17:00:00Z | 15 |
| Direktagent | 2017-06-06T18:00:00Z | 60 |
| Direktagent | 2017-06-06T20:00:00Z | 55 |
| Direktagent | 2017-06-06T21:00:00Z | 57 |
| Direktagent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

I den här resulterar dock en bucket som är associerade med ”2017-06-06T19:00:00Z” saknar eftersom det inte finns några heartbeat-data för den timmen. Använd den `make-series` funktionen för att tilldela ett standardvärde till tom buckets. Detta genererar en rad för varje kategori med två extra matris kolumner, en för värden och en matchande tid buckets:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| Direktagent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Det tredje elementet i den *count_* matrisen är 0 som förväntat och det finns en matchande tidsstämpeln för ”2017-06-06T19:00:00.0000000Z” i den _TimeGenerated_ matris. Den här matrisformat är svåra att läsa om. Använd `mvexpand` Expandera matriserna och producerar utdata som genereras av samma format `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direktagent | 2017-06-06T17:00:00Z | 15 |
| Direktagent | 2017-06-06T18:00:00Z | 60 |
| Direktagent | 2017-06-06T19:00:00Z | 0 |
| Direktagent | 2017-06-06T20:00:00Z | 55 |
| Direktagent | 2017-06-06T21:00:00Z | 57 |
| Direktagent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Begränsa resultaten till en uppsättning element: `let`, `makeset`, `toscalar`, `in`
Ett vanligt scenario är att välja namnen på vissa specifika entiteter som baseras på en uppsättning villkor och sedan filtrera en annan datauppsättning till den uppsättningen entiteter. Du kan till exempel söka efter datorer som saknar uppdateringar och identifiera IP-adresser som de här datorerna som påpekas att:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Nästa steg

Se andra lektioner för att använda den [Kusto-frågespråket](/azure/kusto/query/) logga data med Azure Monitor:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)