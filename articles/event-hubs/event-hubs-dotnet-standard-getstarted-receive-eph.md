---
title: Ta emot händelser från Azure Event Hubs med .NET Standard-bibliotek | Microsoft Docs
description: Börja ta emot meddelanden med EventProcessorHost i .NET Standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5abb2447fa90ea5900afb86746cc17eff62c2d2e
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166295"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Börja ta emot meddelanden med EventProcessorHost i .NET Standard

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

I den här självstudien får du lära dig att skriva ett .NET Core-konsolprogram som tar emot meddelanden från en Event Hub med biblioteket **Värd för händelsebearbetning**. Du kan köra [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)-lösningen i befintligt skick och ersätta strängarna med värdena för din händelsehubb och lagringskonto. Eller så kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Microsoft Visual Studio 2015 eller 2017](http://www.visualstudio.com). I exemplen i självstudien används Visual Studio 2017, men Visual Studio 2015 stöds också.
* [.NET Core Visual Studio 2015- eller 2017-verktyg](http://www.microsoft.com/net/core).
* En Azure-prenumeration.
* Ett namnområde för Azure Event Hubs och en händelsehubb.
* Ett Azure-lagringskonto.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb  

Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md) och fortsätter sedan med självstudien.  

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto  

1. Logga in på [Azure-portalen](https://portal.azure.com).  
2. I det vänstra navigeringsfönstret i portalen väljer du **Skapa en resurs**, sedan **Lagring** bland kategorierna och sedan **Lagringskonto – blob, fil, tabell, kö**.  
3. Fyll i fälten i fönstret **Skapa lagringskonto** och klicka på **Granska + skapa**. 

    ![Skapa lagringskonto][1]

4. På sidan **Granska + skapa** väljer du **Skapa** när du har granskat fältens värden. 
5. När du ser meddelandet **Distributionen är klar** väljer du det nya lagringskontots namn. 
6. I fönstret **Essentials** väljer du **Blobar**. 
7. Välj **+ Container** högst upp. Ge containern ett namn.  
8. Välj **Åtkomstnycklar** i fönstret till vänster och kopiera lagringscontainerns namn, lagringskontot och värdet för **key1**. 

    Spara det här värdet i Anteckningar eller på någon annan tillfällig plats.

## <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**. Skapa ett .NET Core-konsolprogram.

![Nytt projekt][2]

## <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

Lägg till [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) och [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard-bibliotekets NuGet-paket till projektet genom att följa dessa steg: 

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **Microsoft.Azure.EventHubs** och markera paketet **Microsoft.Azure.EventHubs**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
3. Upprepa steg 1 och 2 och installera paketet **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implementera gränssnittet IEventProcessor

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till** och sedan på **Klass**. Ge den nya klassen namnet **SimpleEventProcessor**.

2. Öppna filen SimpleEventProcessor.cs och lägg till följande `using`-uttryck överst i filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementera gränssnittet `IEventProcessor`. Ersätt hela innehållet i klassen `SimpleEventProcessor` med följande kod:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Skriv en huvudkonsolmetod som använder SimpleEventProcessor-klassen för att ta emot meddelanden

1. Lägg till följande `using`-instruktioner överst i Program.cs-filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Lägg till konstanter till `Program`-klassen för händelsehubbens anslutningssträng, händelsehubbens namn, namn på lagringskontocontainern, lagringskontots namn och lagringskontonyckeln. Lägg till följande kod och ersätt platshållarna med motsvarande värden:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Lägg till en ny metod som heter `MainAsync` till `Program`-klassen enligt följande:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Lägg till följande kodrad till `Main`-metoden:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Så här bör din Program.cs-fil se ut:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Kör programmet och kontrollera att det inte finns några fel.

Grattis! Du har nu fått meddelanden från en händelsehubb med värden för händelsebearbetning.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
