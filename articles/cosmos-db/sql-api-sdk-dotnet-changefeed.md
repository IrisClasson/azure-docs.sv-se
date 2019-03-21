---
title: 'Azure Cosmos DB: .NET ändringen Feed Processor API, SDK och resurser'
description: Lär dig allt om ändringen Feed Processor-API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av .NET-ändringen Feed Processor-SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: a878ab1937b06f06a27b18f793fc1bfa190969ed
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090346"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET change Feed Processor SDK: Ladda ned och viktig information
> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentation**|[Ändra Feed Processor-biblioteket API-referensdokumentation](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Kom igång**|[Kom igång med ändringen Feed Processor .NET SDK](change-feed.md)|
|**Aktuella framework som stöds**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Viktig information

### <a name="v2-builds"></a>v2-versioner

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Förbättrad hantering av Övervakare undantag.
* Mer omfattande information om Övervakare-fel:
  * När en observatör stängs på grund av ett undantag som utlöses av Övervakares ProcessChangesAsync, får CloseAsync nu orsak parametern inställd ChangeFeedObserverCloseReason.ObserverError.
  * Har lagts till spårningar identifiera fel i användarkod i observatör.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Stöd har lagts till för att hantera delning i samlingar som använder delade databasen dataflöde.
  * Den här versionen åtgärdas ett problem som kan uppstå under dela i samlingar med delade databasen dataflöde när dela resultatet i partitionen igen belastningsutjämning med endast en underordnad partitionsnyckelintervall skapas i stället för två. När detta sker kan Change Feed Processor fastna ta bort gamla partitionsnyckelintervall lånet och inte skapa nya lån. Problemet är löst i den här versionen.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Har lagts till ny egenskap ChangeFeedProcessorOptions.StartContinuation stöd från ändringen feed från begäran fortsättningstoken. Detta används bara när lånsamling är tom eller ett lån har inte angetts ContinuationToken. ContinuationToken används för lån i lånsamling som har angetts ContinuationToken, och ChangeFeedProcessorOptions.StartContinuation ignoreras.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Stöd har lagts till för att använda anpassade store för att bevara fortsättning token per partition.
  * En anpassad leasing store kan till exempel vara Azure Cosmos DB lånsamling partitioneras på något sätt med anpassade.
  * Anpassad leasing butiker kan använda nya utökningsbarhet punkt ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) och ILeaseStoreManager offentliga gränssnittet.
  * Omstrukturerats ILeaseManager gränssnitt till flera gränssnitt för rollen.
* Mindre icke-bakåtkompatibel ändring: borttagna utökningsbarhet tjänstanslutningspunkten ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), Använd ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) istället.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Den här versionen åtgärdas ett problem som uppstår under bearbetning av en delning i övervakade samling och använder en partitionerad lånsamling. Vid bearbetning av ett lån för partition split kan lånet som motsvarar den partitionen inte tas bort. Problemet är löst i den här versionen.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Beräkning av fast kostnadsuppskattning för flera Master-konton och ny Session tokenformat.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Tillagt stöd för partitionerade lånet samlingar. Partitionsnyckeln måste definieras som /id.
* Mindre icke-bakåtkompatibel ändring:-metoderna i gränssnittet IChangeFeedDocumentClient och klassen ChangeFeedDocumentClient har ändrats för att inkludera RequestOptions och CancellationToken parametrar. IChangeFeedDocumentClient är en avancerad utökningsbarhet som gör det möjligt att tillhandahålla anpassad implementering av Dokumentklienten att använda med Change Feed Processor, t.ex. skapa snygga DocumentClient och komma åt alla anrop till det att göra extra spårning felhantering , osv. Med den här uppdateringen måste den kod som implementerar IChangeFeedDocumentClient ändras för att inkludera nya parametrar i implementeringen.
* Mindre diagnostikförbättringar.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Lagt till nya API: et, uppgift&lt;IReadOnlyList&lt;RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Detta kan användas för att få uppskattade arbetet för varje partition.
* Har stöd för Microsoft Azure documentdb SDK 2.0. Kräver Microsoft.Azure.DocumentDB 2.0 eller senare.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Har lagts till ChangeFeedEventHost.HostName offentlig egenskap för kompatibilitet med v1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Fasta ett konkurrenstillstånd som uppstår under partition split. Konkurrenstillstånd kan leda till hämtar lånet och omedelbart att förlora den under partition split och orsakar konkurrens. RAS villkor problemet är löst med den här versionen.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* GA-SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Åtgärdat följande problem:
  * Om partition split inträffar kan uppstå det dubbla bearbetning av dokument som har ändrats innan delningen.
  * GetEstimatedRemainingWork API: et returnerade 0 när det fanns inga lån i samlingen lånet.

* Följande undantag görs offentliga. Tillägg som implementerar IPartitionProcessor orsaka sådana undantag.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Mindre ändringar i API:
  * Ta bort ChangeFeedProcessorOptions.IsAutoCheckpointEnabled som har markerats som föråldrade.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Stabilitetsförbättringar:
  * Bättre hantering av lånet store initieras. Om lånet store är tom, bara en instans av processor kan initiera den väntar andra.
  * Mer stabil/effektiv lånet förnyelse och utgåva. Förnya och lansera ett lån en partition är oberoende av förnyar andra. I v1 som gjordes sekventiellt för alla partitioner.
* Nya v2 API: et:
  * Builder mönster för flexibla konstruktion av processorn: klassen ChangeFeedProcessorBuilder.
    * Ta valfri kombination av parametrar.
    * Det kan ta en instans av DocumentClient för övervakning och/eller lånet samling (inte tillgängligt i v1).
  * IChangeFeedObserver.ProcessChangesAsync tar nu bara CancellationToken.
  * IRemainingWorkEstimator - återstående arbete kostnadsuppskattning kan användas separat från processorn.
  * Nya punkter:
    * IPartitionLoadBalancingStrategy - för anpassade belastningsutjämning av partitioner mellan instanser av processorn.
    * ILease ILeaseManager - för hantering av anpassade lånet.
    * IPartitionProcessor - för anpassad bearbetning ändringar på en partition.
* Loggning - använder [LibLog](https://github.com/damianh/LibLog) biblioteket.
* 100% bakåtkompatibla med v1-API.
* Ny kodbas.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.21.1 och senare.

### <a name="v1-builds"></a>V1-versioner

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Lägga till mer loggning.
* Fast en DocumentClient läcka när du anropar den pågående arbetet uppskattningen flera gånger.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Korrigeringar i den pågående arbetet uppskattningen.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Stabilitetsförbättringar.
  * Korrigering för hantering av avbrutna aktiviteter problem som kan leda till stoppade observatörer på vissa partitioner.
* Stöd för manuell kontrollpunkter.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.21 och senare.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Lägger till stöd för .NET Standard 2.0. Paketet stöder nu `netstandard2.0` och `net451` framework monikers.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.17.0 och senare.
* Kompatibel med [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) versioner 1.5.1 och senare.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Löser ett problem med beräkningen av uppskattning av återstående när Change Feed var tom eller inget arbete väntade.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.13.2 och senare.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Lägga till en metod för att få en uppskattning av återstående arbete som ska bearbetas i Change Feed.
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.13.2 och senare.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK
* Kompatibel med [SQL .NET SDK](sql-api-sdk-dotnet.md) versioner 1.14.1 och nedan.


## <a name="release--retirement-dates"></a>Fri & Släpp dras tillbaka datum
Microsoft meddelar meddelande minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Varje begäran till Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.2.6](#2.2.6) |Den 29 januari 2019 |--- |
| [2.2.5](#2.2.5) |13 december 2018 |--- |
| [2.2.4](#2.2.4) |Den 29 november 2018 |--- |
| [2.2.3](#2.2.3) |19 november 2018 |--- |
| [2.2.2](#2.2.2) |Den 31 oktober 2018 |--- |
| [2.2.1](#2.2.1) |24 oktober 2018 |--- |
| [1.3.3](#1.3.3) |08 maj 2018 |--- |
| [1.3.2](#1.3.2) |18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |Den 29 augusti 2017 |--- |
| [1.1.0](#1.1.0) |Den 13 augusti 2017 |--- |
| [1.0.0](#1.0.0) |07 juli 2017 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 

