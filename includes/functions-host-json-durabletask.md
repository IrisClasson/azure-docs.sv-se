---
title: inkludera fil
description: inkludera fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80117072"
---
Konfigurations inställningar för [Durable Functions](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2. x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

Namn på uppgifts hubbar måste börja med en bokstav och får bara bestå av bokstäver och siffror. Om inget värde anges är standard namnet på uppgifts navet för en Function-app **DurableFunctionsHub**. Mer information finns i [aktivitets nav](../articles/azure-functions/durable-functions-task-hubs.md).

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternativa [aktivitets Hubbs](../articles/azure-functions/durable-functions-task-hubs.md) namn kan användas för att isolera flera Durable Functions program från varandra, även om de använder samma lagrings Server del.|
|controlQueueBatchSize|32|Antalet meddelanden att hämta från kontroll kön i taget.|
|controlQueueBufferThreshold|256|Antalet kontrollmeddelanden som kan buffras i minnet i taget, vid vilken tidpunkt Dispatchern väntar innan eventuella ytterligare meddelanden tas ur kö.|
|partitionCount |4|Antalet partitioner för kontroll kön. Kan vara ett positivt heltal mellan 1 och 16.|
|controlQueueVisibilityTimeout |5 minuter|Timeout för visning av köade kontrollmeddelanden.|
|workItemQueueVisibilityTimeout |5 minuter|Tids gränsen för visning av meddelanden i kö för arbets objekt i kö.|
|maxConcurrentActivityFunctions |10X antalet processorer på den aktuella datorn|Det maximala antalet aktivitets funktioner som kan bearbetas samtidigt på en enda värd instans.|
|maxConcurrentOrchestratorFunctions |10X antalet processorer på den aktuella datorn|Det maximala antalet Orchestrator-funktioner som kan bearbetas samtidigt på en enda värd instans.|
|maxQueuePollingInterval|30 sekunder|Det maximala avsöknings intervallet för kontroll och arbets objekt kön i formatet *hh: mm: SS* . Högre värden kan resultera i högre meddelande fördröjning. Lägre värden kan resultera i högre lagrings kostnader på grund av ökade lagrings transaktioner.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Namnet på den app-inställning som har Azure Storage anslutnings strängen som används för att hantera de underliggande Azure Storage resurserna.|
|trackingStoreConnectionStringName||Namnet på en anslutnings sträng som ska användas för tabellerna historik och instans. Om inget anges `azureStorageConnectionStringName` används anslutningen.|
|trackingStoreNamePrefix||Det prefix som ska användas för historik-och instans tabellerna när `trackingStoreConnectionStringName` har angetts. Om den inte anges kommer standardvärdet för prefixet att vara `DurableTask` . Om `trackingStoreConnectionStringName` inte har angetts använder historik-och instans tabellerna `hubName` värdet som prefix, och alla inställningar för `trackingStoreNamePrefix` kommer att ignoreras.|
|traceInputsAndOutputs |falskt|Ett värde som anger om indata och utdata för funktions anrop ska spåras. Standard beteendet vid körning av spårning av funktions händelser är att inkludera antalet byte i de serialiserade indata och utdata för funktions anrop. Det här beteendet ger minimal information om vad indata och utdata ser ut som om de inte bloating loggar eller oavsiktligt exponerar känslig information. Om den här egenskapen ställs in på Sant loggas hela innehållet i funktions inmatning och utdata.|
|logReplayEvents|falskt|Ett värde som anger om omdirigerings händelser ska skrivas till Application Insights.|
|eventGridTopicEndpoint ||URL för en Azure Event Grid anpassad ämnes slut punkt. När den här egenskapen anges publiceras Dirigerings livs cykel meddelande händelser till den här slut punkten. Den här egenskapen stöder App Settings-matchning.|
|eventGridKeySettingName ||Namnet på den app-inställning som innehåller den nyckel som används för att autentisera med Azure Event Grid anpassat ämne på `EventGridTopicEndpoint` .|
|eventGridPublishRetryCount|0|Antalet gånger som försök görs om publicering till Event Grid avsnittet Miss lyckas.|
|eventGridPublishRetryInterval|5 minuter|Återförsöks intervallet för Event Grid publiceras i formatet *hh: mm: SS* .|
|eventGridPublishEventTypes||En lista med händelse typer att publicera till Event Grid. Om inget värde anges kommer alla händelse typer att publiceras. Tillåtna värden är `Started` , `Completed` , `Failed` , `Terminated` .|
|useGracefulShutdown|falskt|Förhandsgranskningsvyn Aktivera Stäng av på ett smidigt sätt för att minska risken för att det inte går att stänga av värden i process funktionens körningar.|

Många av de här inställningarna är för att optimera prestanda. Mer information finns i [prestanda och skalning](../articles/azure-functions/durable-functions-perf-and-scale.md).
