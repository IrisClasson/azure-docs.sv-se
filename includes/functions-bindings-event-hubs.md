---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 421e0db48f045c5cbce52a0641902e6d2a11276e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132450"
---
## <a name="trigger"></a>Utlösare

Använd funktionen utlösaren för att svara på en händelse som skickas till en event hub-händelseström. Du måste ha läsbehörighet till den underliggande händelsehubben för att konfigurera utlösaren. När funktionen har utlösts skrivit meddelandet som skickades till funktionen som en sträng.

## <a name="trigger---scaling"></a>Utlösa - skalning

Varje instans av en som utlöste händelsefunktion backas upp av en enda [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instans. Utlösaren (drivs av Event Hubs) säkerställer att endast en [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instans kan få ett lån på en given partition.

Anta exempelvis att en Händelsehubb på följande sätt:

* 10 partitioner
* 1 000 händelser fördelas jämnt över alla partitioner med 100 meddelanden i varje partition

När funktionen aktiveras först, finns det endast en instans av funktionen. Vi kan kalla den första instansen av funktionen `Function_0`. Den `Function_0` funktionen har en enda instans av [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) som har ett lån på alla tio partitioner. Den här instansen läser händelser från partitioner 0-9. Från och med nu händer något av följande:

* **Den nya funktionen instanser behövs inte**: `Function_0` kan bearbeta alla 1 000 händelser innan de funktioner som skalning logic träder i kraft. I det här fallet alla 1 000 meddelandena behandlas av `Function_0`.

* **En ytterligare funktion-instans läggs**: Om funktionerna skalning logic avgör att `Function_0` har fler meddelanden än den kan bearbeta, en ny funktion app-instansen (`Function_1`) har skapats. Den här nya funktionen har också en associerad instans av [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Medan den underliggande Händelsehubbar identifierar att en ny värdinstans som försöker läsa meddelanden, den belastningsutjämnar partitioner över på dess värddatorinstanser. Till exempel partitioner 0-4 kan ha tilldelats `Function_0` och partitionerar 5 – 9 till `Function_1`.

* **N fler instanser av funktionen läggs**: Om funktionerna skalning logic avgör att båda `Function_0` och `Function_1` har fler meddelanden än vad de kan bearbeta, nya `Functions_N` funktionen app-instanser har skapats.  Appar skapas till punkten där `N` är större än antalet händelsenavspartitioner. I vårt exempel Händelsehubbar igen belastningsutjämnar partitioner, i det här fallet mellan instanserna `Function_0`... `Functions_9`.

När funktionerna skalor, `N` instanser är ett tal som är större än antalet händelsenavspartitioner. Detta görs för att säkerställa [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instanser är tillgängliga att hämta lås på partitioner när de blir tillgängliga från andra instanser. Du debiteras bara för de resurser som används när funktionen-instansen körs. Du debiteras alltså inte av den här överetablering.

När alla funktionen körningen har slutförts (med eller utan fel) har kontrollpunkter lagts till det associerade lagringskontot. När kontrollpunkter lyckas, hämtas alla 1 000 meddelanden aldrig igen.

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](../articles/azure-functions/functions-dotnet-class-library.md) som loggar brödtexten i event hub-utlösare.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Du får åtkomst till [händelsemetadata](#trigger---event-metadata) i Funktionskoden, binda till en [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objekt (kräver en med hjälp av instruktionen för `Microsoft.Azure.EventHubs`). Du kan också komma åt samma egenskaper med hjälp av bindningen uttryck i Metodsignaturen.  I följande exempel visas båda sätten att hämta samma data:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Om du vill ta emot händelser i en batch göra `string` eller `EventData` en matris.  

> [!NOTE]
> När du tar emot i en batch som du inte kan bindas till metodparametrar precis som i exemplet ovan med `DateTime enqueuedTimeUtc` och måste ta emot dessa från var och en `EventData` objekt  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [C#-skriptfunktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen loggar brödtexten i event hub-utlösare.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil.

#### <a name="version-2x"></a>Version 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är C#-skriptkoden:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Du får åtkomst till [händelsemetadata](#trigger---event-metadata) i Funktionskoden, binda till en [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objekt (kräver en med hjälp av instruktionen för `Microsoft.Azure.EventHubs`). Du kan också komma åt samma egenskaper med hjälp av bindningen uttryck i Metodsignaturen.  I följande exempel visas båda sätten att hämta samma data:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Om du vill ta emot händelser i en batch göra `string` eller `EventData` en matris:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Utlösare – F# exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [ F# funktionen](../articles/azure-functions/functions-reference-fsharp.md) som använder bindningen. Funktionen loggar brödtexten i event hub-utlösare.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. 

#### <a name="version-2x"></a>Version 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är den F# kod:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen läser [händelsemetadata](#trigger---event-metadata) och loggar meddelandet.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil.

#### <a name="version-2x"></a>Version 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Om du vill ta emot händelser i en batch, ange `cardinality` till `many` i den *function.json* fil, enligt följande exempel.

#### <a name="version-2x"></a>Version 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Utlösare – Python-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen läser [händelsemetadata](#trigger---event-metadata) och loggar meddelandet.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är Python-kod:

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande exempel visas en Event Hub-utlösare bindning i en *function.json* fil och en [Java funktionen](../articles/azure-functions/functions-reference-java.md) som använder bindningen. Funktionen loggar brödtexten i utlösaren Event Hub.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `EventHubTrigger` anteckning om parametrar vars värde skulle hämtas från Event Hub. Med dessa anteckningar orsaka funktionen ska köras när en händelse tas emot.  Den här anteckningen kan användas med interna Java-typer, Pojo eller kan ha värdet null-värden med hjälp av valfritt<T>.

## <a name="trigger---attributes"></a>Utlösare - attribut

I [C#-klassbibliotek](../articles/azure-functions/functions-dotnet-class-library.md), använda den [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) attribut.

Attributets konstruktorn tar namnet på händelsehubben, namnet på konsumentgruppen och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i den [utlösa konfigurationsavsnittet](#trigger---configuration). Här är en `EventHubTriggerAttribute` attributet exempel:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHubTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till `eventHubTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar objektet händelse i funktionskoden. |
|**Sökväg** |**EventHubName** | Fungerar endast 1.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Fungerar endast 2.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. |
|**consumerGroup** |**consumerGroup** | En valfri egenskap som anger den [konsumentgrupp](../articles/event-hubs/event-hubs-features.md)#event-konsumenter) används för att prenumerera på händelser i hubben. Om det utelämnas används den `$Default` konsumentgrupp används. |
|**kardinalitet** | Saknas | För Javascript. Ange `many` för att aktivera Batchbearbetning.  Om detta utelämnas eller värdet `one`, enskilt meddelande som skickades till funktionen. |
|**anslutning** |**anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till namnområdet för event hub. Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformation** för den [namnområde](../articles/event-hubs/event-hubs-create.md)#create-en-event-hubs-namnområde), inte händelsehubben själva. Den här anslutningssträngen måste ha minst läsbehörighet till aktivera utlösaren.|
|**Sökväg**|**EventHubName**|Namnet på händelsehubben. Kan refereras via appinställningar `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Utlösare - metadata för händelser

Event Hubs-utlösare innehåller flera [metadataegenskaper](../articles/azure-functions/./functions-bindings-expressions-patterns.md). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för den [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) klass.

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Den `PartitionContext` instans.|
|`EnqueuedTimeUtc`|`DateTime`|Kötid i UTC.|
|`Offset`|`string`|Förskjutningen av data i förhållande till Event Hub partition dataströmmen. Förskjutningen är en markör eller identifieraren för en händelse i Event Hubs-dataströmmen. Identifieraren är unika inom en partition av Händelsehubbar dataströmmen.|
|`PartitionKey`|`string`|Den partition som vilken händelse som data ska skickas.|
|`Properties`|`IDictionary<String,Object>`|Användaregenskaper av händelsedata.|
|`SequenceNumber`|`Int64`|Logiska sekvensnumret för händelsen.|
|`SystemProperties`|`IDictionary<String,Object>`|Systemegenskaper inklusive händelsedata.|

Se [kodexempel](#trigger---example) som använder de här egenskaperna tidigare i den här artikeln.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

Den [host.json](../articles/azure-functions/functions-host-json.md#eventhub) filen innehåller inställningar som styr beteendet för Event Hubs-utlösare.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Resultat

Använda Event Hubs-utdatabindning till skriva händelser till en händelseström. Du måste ha skicka behörighet till en event hub att skriva händelser till den.

Kontrollera att nödvändiga paketet refererar till är uppfyllda: 1.x-funktioner eller funktioner 2.x

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [C#-funktion](../articles/azure-functions/functions-dotnet-class-library.md) som skriver ett meddelande till en händelsehubb med hjälp av metoden returvärdet som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

I följande exempel visas hur du använder den `IAsyncCollector` gränssnittet för att skicka en grupp med meddelanden. Det här scenariot är vanligt vid bearbetning av meddelanden som kommer från en Händelsehubb och skickar resultatet till en annan Event Hub.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [C#-skriptfunktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är C#-skriptkoden som skapar ett meddelande:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Här är C#-skriptkoden som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Utdata - F# exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [ F# funktionen](../articles/azure-functions/functions-reference-fsharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är den F# kod:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är JavaScript-kod som skickar ett enskilt meddelande:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Här är JavaScript-kod som skickar flera meddelanden:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Resultat – Python-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är Python-kod som skickar ett enskilt meddelande:

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Resultat – Java-exemplet

I följande exempel visas en Java-funktion som skriver ett meddelande innehåller den aktuella tiden till en Händelsehubb.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@EventHubOutput` anteckning om parametrar vars värde som ska publiceras till Event Hub.  Parametern måste vara av typen `OutputBinding<T>` , där T är en POJO eller några interna Java-datatypen.

## <a name="output---attributes"></a>Utdata - attribut

För [C#-klassbibliotek](../articles/azure-functions/functions-dotnet-class-library.md), använda den [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribut.

Attributets konstruktorn tar namnet på händelsehubben och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i [utdata - konfigurationen](#output---configuration). Här är en `EventHub` attributet exempel:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHub` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**type** | Saknas | Måste anges till ”eventHub”. |
|**direction** | Saknas | Måste anges till ”ut”. Den här parametern anges automatiskt när du skapar bindningen i Azure-portalen. |
|**name** | Saknas | Variabelnamnet som används i Funktionskoden som representerar händelsen. |
|**Sökväg** |**EventHubName** | Fungerar endast 1.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Fungerar endast 2.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. |
|**anslutning** |**anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till namnområdet för event hub. Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformation** för den *namnområde*, inte händelsehubben själva. Den här anslutningssträngen måste ha behörighet att skicka att skicka meddelandet till händelseströmmen.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C#-skript, skicka meddelanden genom att använda en metodparameter som `out string paramName`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. Du kan använda för att skriva flera meddelanden `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

Få åtkomst till händelsen utdata i JavaScript, med hjälp av `context.bindings.<name>`. `<name>` är värdet som angetts i den `name` egenskapen för *function.json*.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Händelsehubb | [Handboken](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

Det här avsnittet beskrivs de globala konfigurationsinställningarna som är tillgängliga för den här bindningen i version 2.x. Host.json-exempelfilen nedan innehåller bara till version 2.x inställningarna för den här bindningen. Mer information om konfigurationsinställningar i version 2.x kan se [host.json-referens för Azure Functions version 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|maxBatchSize|64|Det maximala antal mottagna händelser per receive-loop.|
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande EventProcessorHost.|
|batchCheckpointFrequency|1|Antal händelsebatchar att bearbeta innan du skapar en kontrollpunkt för EventHub-markör.|