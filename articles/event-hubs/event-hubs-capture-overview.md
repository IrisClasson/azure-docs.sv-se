---
title: Avbilda strömmande händelser – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över avbildningsfunktionen som gör det möjligt att samla in händelser som strömmas via Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4ba3109460616be98b5330ec7175f161a6a3b750
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68326171"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Samla in händelser via Azure Event Hubs i Azure Blob Storage eller Azure Data Lake Storage
Händelsehubbar i Azure kan du automatiskt samla in strömmande data i Event Hubs i en [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) eller [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) konto föredrar, med den ökade flexibiliteten Ange ett tids- eller storleksintervall. Det går snabbt att ställa in Capture, det finns inga administrativa kostnader för att köra den och den skalar automatiskt med Event Hubs [genomflödesenheter](event-hubs-scalability.md#throughput-units). Event Hubs Capture är det enklaste sättet att läsa in strömmande data i Azure och kan du fokusera på databearbetning i stället för datainsamling.

Event Hubs Capture kan du bearbeta realtidsdata och batch-baserade pipelines för samma dataström. Det innebär att du kan skapa lösningar för dina behov över tid. Om du skapar batch-baserade system idag med ett öga mot framtida realtidsbearbetning, eller om du vill lägga till en effektiv kalla sökvägen till en befintlig lösning i realtid, kan Event Hubs Capture arbeta med strömmande data enklare.

## <a name="how-event-hubs-capture-works"></a>Så här Event Hubs Capture fungerar

Event Hubs är en stabil buffert tid kvarhållning för telemetri ingress, liknar en distribuerad logg. Nyckeln till skalning i Event Hubs är den [konsumentmönster indelat i partitioner modellen](event-hubs-scalability.md#partitions). Varje partition är en oberoende segment av data och används oberoende av varandra. Med tiden åldrarna dessa data av, baserat på konfigurerbara kvarhållningsperioden. Därför kan det finns en viss händelsehubb för aldrig hämtar ”full”.

Event Hubs Capture kan du ange ditt eget Azure Blob storage-konto och en behållare eller ett Azure Data Lake Store-konto, som används för att lagra insamlade data. Dessa konton kan vara i samma region som din event hub eller i en annan region, att lägga till funktionen Event Hubs Capture flexibilitet.

Insamlade data skrivs i [Apache Avro][Apache Avro] -format: ett kompakt, fast binärformat som ger omfattande data strukturer med infogat schema. Det här formatet används ofta i Hadoop-ekosystemet, Stream Analytics och Azure Data Factory. Mer information om hur du arbetar med Avro finns senare i den här artikeln.

### <a name="capture-windowing"></a>Avbilda fönsterhantering

Event Hubs Capture kan du konfigurera ett fönster för att styra avbildning. Det här fönstret är en minsta storlek och konfiguration av en med en ”första wins-princip” vilket innebär att den första utlösaren som påträffades orsakar en insamlingen. Om du har en femton minuter fönstret capture 100 MB och skicka 1 MB per sekund, utlösare för storlek fönster innan tidsfönstret. Varje partition samlar in oberoende av varandra och skriver en slutförd blockblob vid tidpunkten för insamlingen, med namnet för den tid då capture intervallet påträffades. Namngivningskonventionen för lagring är följande:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Observera att datumvärdena är fylls ut med nollor; ett filnamn som exempel kan vara:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

Om din Azure Storage-BLOB är tillfälligt otillgänglig, kommer Event Hubs fånga att behålla dina data för den data lagrings period som kon figurer ATS på händelsehubben och fylla i data på nytt när ditt lagrings konto är tillgängligt igen.

### <a name="scaling-to-throughput-units"></a>Skala till dataflödesenheter

Trafik för Event Hubs styrs av [genomflödesenheter](event-hubs-scalability.md#throughput-units). En genomflödesenhet kan 1 MB per sekund eller 1 000 händelser per sekund för ingångshändelser och två gånger det beloppet utgående data. Standard Event Hubs kan konfigureras med 1-20-dataflödes enheter och du kan köpa mer med en kvot som ökar [support förfrågan][support request]. Användning utöver din köpta genomflödesenheter är begränsad. Event Hubs Capture kopierar data direkt från den interna lagringen för Event Hubs, vilket kringgår throughput unit utgående kvoter och spara din utgående trafik för andra läsare för bearbetning, till exempel Stream Analytics- eller Spark.

När du konfigurerat Event Hubs Capture körs automatiskt när du skickar din första händelsen och fortsätter att köras. Om du vill göra det enklare för dina nedströms bearbetning veta att processen fungerar, skriver Händelsehubbar tomma filer när det finns inga data. Den här processen ger en förutsägbar takt och markör som kan mata batch-processorer.

## <a name="setting-up-event-hubs-capture"></a>Ställa in Event Hubs Capture

Du kan konfigurera avbildningsfunktionen på den event hub skapas med den [Azure-portalen](https://portal.azure.com), eller med hjälp av Azure Resource Manager-mallar. Mer information finns i följande artiklar:

- [Aktivera Event Hubs Capture i Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Utforska de hämtade filerna och arbeta med Avro

Event Hubs Capture skapar filer i Avro-format som anges på den konfigurerade tidsperioden. Du kan visa dessa filer i alla verktyg som [Azure Storage Explorer][Azure Storage Explorer]. Du kan hämta filer lokalt för att arbeta med dem.

De filer som skapas av Event Hubs Capture har följande Avro-schemat:

![Avro-schema][3]

Ett enkelt sätt att utforska Avro-filer är genom att använda [Avro-verktygen][Avro Tools] jar från Apache. Du kan också använda [Apache-granskning][Apache Drill] för en låg SQL-driven upplevelse eller [Apache Spark][Apache Spark] för att utföra komplex distribuerad bearbetning på inmatade data. 

### <a name="use-apache-drill"></a>Använda Apache-granskning

[Apache-granskning][Apache Drill] är en SQL-frågemotor med öppen källkod för stor data utforskning som kan fråga strukturerade och delvis strukturerade data var de än är. Motorn kan köras som en fristående nod eller som ett stort kluster för fantastisk prestanda.

Ett inbyggt stöd för Azure Blob Storage är tillgängligt, vilket gör det enkelt att fråga efter data i en Avro-fil, enligt beskrivningen i dokumentationen:

[Apache-granskning: Azure Blob Storage-plugin-program][Apache Drill: Azure Blob Storage Plugin]

För att enkelt fråga insamlade filer kan du skapa och köra en virtuell dator med Apache-granskning aktiverat via en behållare för att få åtkomst till Azure Blob Storage:

https://github.com/yorek/apache-drill-azure-blob

Ett fullständigt exempel på slut punkt till slut punkt är tillgängligt i strömningen vid skalnings lagring:

[Strömning i skala: Samla in Event Hubs]

### <a name="use-apache-spark"></a>Använd Apache Spark

[Apache Spark][Apache Spark] är en "enhetlig analys motor för storskalig data bearbetning". Den har stöd för olika språk, inklusive SQL, och kan enkelt komma åt Azure Blob Storage. Det finns två alternativ för att köra Apache Spark i Azure och båda ger enkel åtkomst till Azure Blob Storage:

- [HDInsight Adressera filer i Azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: Azure Blob Storage][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Använda Avro-verktyg

[Avro-verktyg][Avro Tools] är tillgängliga som jar-paket. När du har hämtat jar-filen kan du se schemat för en speciell avro-fil genom att köra följande kommando:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Det här kommandot returnerar

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Du kan också använda Avro-verktyg för att konvertera filen till JSON-format och utföra andra bearbetning.

Om du vill utföra mer avancerad bearbetning, hämta och installera Avro för valfri plattform. När detta skrivs det finns implementeringar för C, C++, C\#, Java, NodeJS, Perl, PHP, Python och Ruby.

Apache Avro har slutfört Komma igång guider för [Java][Java] och [python][Python]. Du kan också läsa den [komma igång med Event Hubs Capture](event-hubs-capture-python.md) artikeln.

## <a name="how-event-hubs-capture-is-charged"></a>Hur Event Hubs Capture debiteras

Event Hubs Capture mäts på samma sätt dataflödesenheter: som en timme. Avgiften gäller direkt proportion till antalet dataflödesenheter som har köpt för namnområdet. Eftersom dataflödesenheter ökar och minskar, Event Hubs Capture taxor öka och minska för att tillhandahålla matchande prestanda. Mätarna inträffa tillsammans. Information om prissättning finns i [priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

Observera att insamlaren inte förbrukar utgångs kvoten när den faktureras separat. 

## <a name="integration-with-event-grid"></a>Integrering med Event Grid 

Du kan skapa en Azure Event Grid-prenumeration med ett namnområde för Event Hubs som källa. Följande självstudie visar hur du skapar en Event Grid-prenumeration med en Event Hub som en källa och en Azure Functions app som mottagare: [Bearbeta och migrera insamlade Event Hubs data till en SQL Data Warehouse med event Grid och Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Nästa steg

Event Hubs Capture är det enklaste sättet att hämta data till Azure. Med Azure Data Lake, Azure Data Factory och Azure HDInsight kan du utföra batchbearbetning och andra analytics med hjälp av välbekanta verktyg och plattformar du väljer, i valfri skala du behöver.

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Börja skicka och ta emot händelser](event-hubs-dotnet-framework-getstarted-send.md)
* [Event Hubs-översikt][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.9.0/java/avro-tools-1.9.0.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Strömning i skala: Samla in Event Hubs]: https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
