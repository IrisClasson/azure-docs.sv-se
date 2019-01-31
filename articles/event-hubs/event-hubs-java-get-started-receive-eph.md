---
title: Ta emot händelser med Java – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Java-program som tar emot händelser från Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 0fc9b8b6a8bcd62aafda7c04697ab8b9c096b17e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296587"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Ta emot händelser från Azure Event Hubs med Java

Händelsehubbar är en mycket skalbar inmatning system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När uppgifterna väl samlats i Event Hubs, kan du omvandla och lagra data med hjälp av en leverantör av realtidsanalyser eller lagringskluster.

Mer information finns i den [översikt av Händelsehubbar][Event Hubs overview].

Den här självstudien visar hur du tar emot händelser till en händelsehubb med ett konsolprogram som skrivits i Java.

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande krav:

* En Java-utvecklingsmiljön. I den här självstudiekursen förutsätter vi att [Eclipse](https://www.eclipse.org/).
* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.

Koden i den här självstudien är baserad på den [EventProcessorSample kod på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), som du kan kontrollera för att se fullständiga fungerande program.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ta emot meddelanden med EventProcessorHost i Java

**EventProcessorHost** är en javaklass som förenklar mottagandet av händelser från Event Hubs genom att hantera permanenta kontrollpunkter och parallella mottaganden från Event Hubs. Med EventProcessorHost kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder EventProcessorHost för en enda mottagare.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Om du vill använda EventProcessorHost, måste du ha en [Azure Storage-konto][Azure Storage account]:

1. Logga in på den [Azure-portalen][Azure portal], och klicka på **+ skapa en resurs** på vänster sida av skärmen.
2. Klicka på **Lagring** och sedan på **Lagringskonto**. I den **skapa lagringskonto** fönstret anger du ett namn för lagringskontot. Slutför resten av fälten, Välj önskad region och klicka sedan på **skapa**.
   
    ![Skapa lagringskonto](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Klicka på det nyligen skapade lagringskontot och klicka sedan på **åtkomstnycklar**:
   
    ![Hämta åtkomstnycklar](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Kopiera key1-värdet till en tillfällig plats. Du använder det senare i den här självstudien.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Skapa ett Java-projekt med EventProcessor-värden

Java-klientbibliotek för Event Hubs är tillgängliga för användning i Maven-projekt från den [Maven Central Repository][Maven Package], och kan refereras med följande beroendedeklaration i din Maven projektfilen: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

För olika typer av versionsmiljöer kan du uttryckligen hämta de senast utgivna JAR-filerna från den [Maven Central Repository][Maven Package].  

1. För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Klassen kallas `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Använd följande kod för att skapa en ny klass som kallas `EventProcessorSample`. Ersätt platshållarna med värden som används när du har skapat kontot för event hub och lagring:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Skapa en mer klass med namnet `EventProcessor`, med följande kod:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Den här guiden använder en enda instans av EventProcessorHost. För att öka dataflödet rekommenderar vi att du kör flera instanser av EventProcessorHost, helst på separata datorer.  Det ger även redundans. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för EventProcessorHost-instanser baserat på de datorer (eller roller) som de har distribuerats i.

## <a name="publishing-messages-to-eventhub"></a>Publicera meddelanden till EventHub

Innan meddelanden hämtas av användare, har de publiceras till partitionerna först av utgivarna. Det är värt att när meddelanden har publicerats till event hub synkront med metoden sendSync() com.microsoft.azure.eventhubs.EventHubClient-objektet visas meddelandet kan skickas till en specifik partition eller distribueras till alla tillgängliga partitioner på en resursallokering sätt beroende på om anges partitionsnyckel eller inte.

När du anger en sträng som representerar Partitionsnyckeln hashas nyckeln för att avgöra vilken partition som ska skicka händelsen till.

När Partitionsnyckeln inte har angetts, sedan är meddelanden resursallokering robined till alla tillgängliga partitioner

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementera en anpassad CheckpointManager för EventProcessorHost (EPH)

API: et är en mekanism för att implementera din anpassade kontrollpunktshanterare för scenarier där standardimplementering inte är kompatibla med ditt användningsområde.

Kontrollpunktshanterare standard använder blob-lagring men om du åsidosätter kontrollpunktshanterare som används av EPH med en egen implementering, du kan använda alla store som du vill säkerhetskopiera implementeringen kontrollpunkt manager.

Skapa en klass som implementerar gränssnittet com.microsoft.azure.eventprocessorhost.ICheckpointManager

Använd din anpassade implementering av kontrollpunktshanterare (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Inom din implementering kan du åsidosätta kontrollpunkter standardmekanismen och implementera vår egen kontrollpunkter som bygger på ditt eget datalager (SQL Server, CosmosDB, Azure Cache för Redis osv). Vi rekommenderar att store används för att säkerhetskopiera implementeringen kontrollpunkt manager är tillgänglig för alla EPH-instanser som bearbetar händelser för konsumentgruppen.

Du kan använda alla datalager som är tillgänglig i din miljö.

Klassen com.microsoft.azure.eventprocessorhost.EventProcessorHost ger dig två konstruktorer så att du kan åsidosätta kontrollpunktshanterare för din EventProcessorHost.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du ett Java-program som har fått meddelanden från en event hub. Läs hur du skickar händelser till en event hub med Java i [skicka händelser från event hub - Java](event-hubs-java-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
