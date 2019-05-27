---
title: Övervaka hälsotillståndet för Azure IoT Hub | Microsoft Docs
description: Använda Azure Monitor och Azure Resource Health för att övervaka din IoT Hub och diagnostisera problem snabbt
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 6dea1add1e329cfc894068732898a856a69c9b4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66166195"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Övervaka hälsotillståndet för Azure IoT Hub och diagnostisera problem snabbt

Företag som implementerar Azure IoT Hub förväntar sig pålitlig prestanda från sina resurser. För att hjälpa dig att upprätthålla en Stäng bevakning på din verksamhet, IoT Hub är helt integrerat med [Azure Monitor](../azure-monitor/index.yml) och [Azure Resource Health](../service-health/resource-health-overview.md). De här två tjänsterna fungerar så att du får de data du behöver för att hålla din IoT-lösningar som körs i ett felfritt tillstånd.

Azure Monitor är en enda källa för övervakning och loggning för alla dina Azure-tjänster. Du kan skicka diagnostikloggar som Azure Monitor genererar Azure Monitor-loggar, Event Hubs eller Azure Storage för anpassad bearbetning. Azure Monitor-mått och diagnostik för inställningarna ger dig insyn i prestanda för dina resurser. Fortsätt att läsa den här artikeln om du vill lära dig hur du [Använd Azure Monitor](#use-azure-monitor) med IoT-hubben. 

> [!IMPORTANT]
> De händelser som genereras av IoT Hub-tjänsten med hjälp av Azure Monitor-diagnostikloggar är inte garanterat tillförlitliga och ordnade. Vissa händelser kan tappas bort eller levereras i ordning. Diagnostikloggar också är inte avsedda att vara i realtid och det kan ta flera minuter innan händelser som loggas ditt val av mål.

Azure Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. En instrumentpanel ger aktuella och tidigare hälsostatus för var och en av dina IoT-hubbar. Fortsätta till avsnittet längst ned i den här artikeln om du vill lära dig hur du [Använd Azure Resource Health](#use-azure-resource-health) med IoT-hubben. 

IoT Hub tillhandahåller även dess egna mått som du kan använda för att förstå tillståndet för dina IoT-resurser. Mer information finns i [förstå IoT Hub mått](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Azure Monitor innehåller diagnostikinformation för Azure-resurser, vilket innebär att du kan övervaka åtgärder som äger rum i din IoT-hubb.

Azure Monitor diagnostik inställningar ersätter övervaka för IoT Hub-åtgärder. Om du använder åtgärdsövervakning, bör du migrera dina arbetsflöden. Mer information finns i [migrera från åtgärder som övervakar Diagnostics inställningar](iot-hub-migrate-to-diagnostics-settings.md).

Mer information om specifika mått och händelser som Azure Monitor bevakar finns [stöds mått med Azure Monitor](../azure-monitor/platform/metrics-supported.md) och [tjänster, scheman och kategorier som stöds för diagnostikloggar för Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Förstå loggar

Azure Monitor övervakar olika åtgärder som inträffar i IoT Hub. Varje kategori har ett schema som definierar hur händelser i den kategorin rapporteras.

#### <a name="connections"></a>Anslutningar

Anslutningar kategorin spårar enheten ansluta och koppla bort händelser från en IoT-hubb, samt fel. Den här kategorin är användbara för att identifiera obehöriga anslutningsförsök och eller aviseringar när du har förlorat anslutningen till enheter.

> [!NOTE]
> Tillförlitlig anslutningsstatus för enheter Kontrollera [enheten pulsslag](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Moln till enhet-kommandon

Moln till enhet kommandon kategorin spårar fel som inträffar på IoT-hubben och som är relaterade till moln till enhet meddelandepipeline. Den här kategorin innehåller fel som uppstår från:

* Skicka meddelanden från moln till enhet (till exempel obehöriga sändaren),
* Ta emot meddelanden från molnet till enheten (till exempel leverans för många fel), och
* Mottagande moln till enhet-meddelande (förfallet fel som feedback).

Den här kategorin fånga inte fel när moln-till-enhet-meddelande levereras men felaktigt hanteras av enheten.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Identitet åtgärder

Enhetskategorin identitet operations spårar fel som uppstår vid försök att skapa, uppdatera eller ta bort en post i din IoT-hubbens identitetsregister. Spårning av den här kategorin är användbart för att etablera scenarier.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Vägar

Meddelandet routning kategorin spårar fel som inträffar när meddelandet vägen utvärdering och slutpunktshälsa anser vara av IoT Hub. Den här kategorin omfattar händelser som:

* En regel som utvärderar till ”odefinierad”
* IoT Hub markerar en slutpunkt som dött, eller
* Eventuella fel togs emot från en slutpunkt. 

Den här kategorin omfattar inte specifika fel om själva meddelandena (till exempel enhet begränsningsfel) som har rapporterats under kategorin ”enhetstelemetri”.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Enhetstelemetri

Telemetri enhetskategorin spårar fel som inträffar på IoT-hubben och som är relaterade till pipelinen telemetri. Den här kategorin innehåller fel som uppstår när du skickar telemetrihändelser (t.ex begränsningar) och ta emot händelser (till exempel obehöriga läsare). Den här kategorin identifierar inte fel som orsakats av kod som körs på själva enheten.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Filöverföringsåtgärder

Filen uppladdning kategorin spårar fel som inträffar på IoT-hubben och som är relaterade till filuppladdning. Den här kategorin omfattar:

* Fel som inträffar med SAS-URI, t.ex när den upphör att gälla innan en enhet meddelar hubb för en överförda.

* Det gick inte överföringar som rapporteras av enheten.

* Fel som uppstår när en fil inte hittas i storage när IoT Hub-meddelande meddelande skapas.

Den här kategorin identifierar inte fel som sker under tiden enheten laddar upp en fil till lagring.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Moln till enhet twin åtgärder

Moln till enhet twin åtgärdskategori spårar tjänstinitierade händelser på enhetstvillingar. De här åtgärderna kan inkludera get twin, uppdatera eller ersätta taggar, och uppdatera eller ersätta önskade egenskaper.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Enhet-till-moln-twin-åtgärder

Enhet-till-moln twin åtgärdskategori spårar enhet-initierad händelser på enhetstvillingar. De här åtgärderna kan inkludera get twin, uppdatera rapporterade egenskaper och prenumerera på önskade egenskaper.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Twin frågor

Kategorin twin frågor rapporterar om frågebegäranden efter enhetstvillingar som startas i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Jobbåtgärder

Jobb åtgärdskategori rapporterar om jobbförfrågningar uppdatera enhetstvillingar eller anropa direktmetoder på flera enheter. Dessa begäranden initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Direkta metoder

Direkta metoder kategorin spårar begäranden och svar-interaktioner som skickas till enskilda enheter. Dessa begäranden initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Distribuerad spårning (förhandsversion)

Distribuerad spårning kategorin spårar Korrelations-ID: N för meddelanden som rubriken trace kontext. Innan du kan utnyttja de här loggarna, klientkod måste uppdateras genom att följa [analysera och diagnostisera IoT program slutpunkt till slutpunkt med IoT Hub distribuerad spårning (förhandsversion)](iot-hub-distributed-tracing.md).

Observera att `correlationId` överensstämmer med den [W3C Trace kontext](https://github.com/w3c/trace-context) förslag, om den innehåller en `trace-id` samt en `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (enhet-till-moln) loggar

IoT Hub registrerar den här loggen när ett meddelande som innehåller egenskaper för giltiga spår anländer till IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Här kan `durationMs` beräknas inte som IoT-hubbens klockan inte är synkroniserade med enhetens klocka och därför en beräkning för varaktighet kan vara vilseledande. Vi rekommenderar att skriva logik med tidsstämplar i den `properties` avsnitt för att avbilda toppar i svarstid för enhet-till-moln.

| Egenskap  | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Storlek för enhet-till-moln-meddelande i byte |
| **deviceId** | Sträng med ASCII 7 bitar alfanumeriska tecken | Identiteten för enheten |
| **callerLocalTimeUtc** | UTC-tidsstämpel | Skapandetid för meddelandet som rapporteras av den lokala klockan för enhet |
| **calleeLocalTimeUtc** | UTC-tidsstämpel | Ankomsttiden meddelande till IoT-hubbens gateway som rapporterats av IoT Hub-tjänsten på klientsidan klockan |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub ingress-loggar

IoT Hub registrerar den här loggen när meddelandet som innehåller egenskaper för giltiga spår skriver till interna eller inbyggda Event Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

I den `properties` avsnittet, den här loggfilen innehåller mer information om inkommande meddelande.

| Egenskap  | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | True eller false, anger huruvida meddelanderoutning är aktiverad i IoT Hub |
| **parentSpanId** | String | Den [span-id](https://w3c.github.io/trace-context/#parent-id) meddelandets överordnade som i det här fallet är spårningen för D2C-meddelande |

##### <a name="iot-hub-egress-logs"></a>IoT Hub utgående loggar

IoT Hub poster detta logga när [routning](iot-hub-devguide-messages-d2c.md) är aktiverad och meddelandet skrivs till en [endpoint](iot-hub-devguide-endpoints.md). Om routning inte är aktiverat, registrera IoT Hub inte den här loggfilen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

I den `properties` avsnittet, den här loggfilen innehåller mer information om inkommande meddelande.

| Egenskap  | Typ | Beskrivning |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | Namnet på slutpunkten som Routning |
| **EndpointType** | String | Vilken typ av Routning slutpunkten |
| **parentSpanId** | String | Den [span-id](https://w3c.github.io/trace-context/#parent-id) meddelandets överordnade som i det här fallet är IoT Hub inkommande meddelande spårningen |

### <a name="read-logs-from-azure-event-hubs"></a>Läs loggar från Azure Event Hubs

Du kan skapa program som läser in loggarna så att du kan vidta åtgärder baserat på informationen i dem när du har konfigurerat händelseloggning via diagnostikinställningar. Den här exempelkoden hämtar loggar från en händelsehubb:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Använda Azure Resource Health

Använd Azure Resource Health för att övervaka om IoT-hubben är igång. Du kan också lära dig om ett regionalt strömavbrott påverkar hälsotillståndet för din IoT-hubb. För att förstå specifik information om hälsotillståndet för Azure IoT Hub, rekommenderar vi att du [Använd Azure Monitor](#use-azure-monitor).

Azure IoT Hub anger hälsotillstånd på regional nivå. Om ett regionalt strömavbrott påverkar din IoT-hubb, hälsostatus visas som **okänd**. Mer information finns i [resurstyper och hälsokontroller i Azure resource health](../service-health/resource-health-checks-resource-types.md).

Följ dessa steg för att kontrollera hälsotillståndet för din IoT-hubbar:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till **Tjänstehälsa** > **resurshälsa**.

3. I listrutorna väljer du din prenumeration och välj sedan **IoT Hub** som resurstypen.

Läs mer om hur du tolkar hälsodata i [översikt över hälsotillståndet för Azure-resurs](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Förstå IoT Hub-mått](iot-hub-metrics.md)
* [IoT fjärrövervakning och aviseringar med Azure Logic Apps ansluter dina IoT-hubb och postlåda](iot-hub-monitoring-notifications-with-azure-logic-apps.md)