---
title: Azure Cosmos DB referens för övervaknings data | Microsoft Docs
description: Logg och statistik referens för övervakning av data från Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 21e1d93e206751b5a55b0b3549e8bd566612ddbe
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080461"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referens till Azure Cosmos DB-övervakningsdata

Den här artikeln är en referens till de logg- och måttdata som samlas in för att analysera prestanda och tillgänglighet för Azure Cosmos DB. Se artikeln [övervaka Azure Cosmos DB](monitor-cosmos-db.md) för hur du samlar in och analyserar övervaknings data för Azure Cosmos dB.

## <a name="resource-logs"></a>Resursloggar

I följande tabell visas egenskaperna för resurs loggar i Azure Cosmos DB. Resurs loggarna samlas in i Azure Monitor loggar eller Azure Storage. I Azure Monitor samlas loggar in i tabellen **AzureDiagnostics** under resurs leverantören * * namnet på `MICROSOFT.DOCUMENTDB` .

| Azure Storage fält eller egenskap | Azure Monitor loggar egenskap | Beskrivning |
| --- | --- | --- |
| **tid** | **TimeGenerated** | Datum och tid (UTC) när åtgärden utfördes. |
| **resourceId** | **Resurs** | Azure Cosmos DB konto för vilka loggar är aktiverade.|
| **kategori** | **Kategori** | För Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** är de tillgängliga logg typerna. |
| **operationName** | **OperationName** | Åtgärdens namn. Åtgärds namnet kan vara,,,,,,,,, `Create` `Update` `Read` `ReadFeed` `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` , eller `Upsert` .   |
| **egenskaperna** | saknas | Innehållet i det här fältet beskrivs i raderna som följer. |
| **activityId** | **activityId_g** | Unikt GUID för den loggade åtgärden. |
| **userAgent** | **userAgent_s** | En sträng som anger den klient användar agent från vilken begäran skickades. Användar agentens format är `{user agent name}/{version}` .|
| **requestResourceType** | **requestResourceType_s** | Typ av resurs som används. Det här värdet kan vara databas, behållare, dokument, bilaga, användare, behörighet, lagrad procedur, utlösare, användardefinierad funktion eller ett erbjudande. |
| **statusCode** | **statusCode_s** | Åtgärdens svars status. |
| **requestResourceId** | **ResourceId** | Det resourceId som hör till begäran. Beroende på vilken åtgärd som utförs kan det här värdet peka på `databaseRid` , `collectionRid` eller `documentRid` .|
| **clientIpAddress** | **clientIpAddress_s** | Klientens IP-adress. |
| **requestCharge** | **requestCharge_s** | Antalet RU/s som används av åtgärden |
| **collectionRid** | **collectionId_s** | Samlingens unika ID.|
| **giltighet** | **duration_d** | Åtgärdens varaktighet i millisekunder. |
| **requestLength** | **requestLength_s** | Längden på begäran, i byte. |
| **responseLength** | **responseLength_s** | Svarets längd i byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Det här värdet är icke-tomt när [resurs-token](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering. Värdet pekar på användarens resurs-ID. |
| **responseLength** | **responseLength_s** | Svarets längd i byte.|

För en lista över alla Azure Monitor logg kategorier och länkar till associerade scheman, se [Azure Monitor loggar kategorier och scheman](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Mått
I följande tabeller visas de plattforms mått som samlats in för Azure CosmOS DB. Alla mått lagras i namn området **Cosmos DB standard mått**.

En lista över alla Azure Monitor support mått (inklusive Azure Cosmos DB) finns i [Azure Monitor mått som stöds](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Begär mått
            
|Mått (måttets visnings namn)|Enhet (agg regerings typ) |Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totalt antal förfrågningar) | Antal (antal) | Antal begär Anden som gjorts| DatabaseName, samlings namn, region, StatusCode| Alla | TotalRequests, http-2xx, http-3xx, http 400, HTTP 401, internt Server fel, tjänsten är inte tillgänglig, begränsade begär Anden, genomsnittliga begär Anden per sekund | Används för att övervaka begär Anden per status kod, behållare på en minuts kornig het. Om du vill få genomsnittlig begär Anden per sekund använder du Count-aggregering på minut och dividerar med 60. |
| MetadataRequests (begär Anden om metadata) |Antal (antal) | Antal metadata-begäranden. Azure Cosmos DB underhåller systemets metadata-behållare för varje konto, så att du kan räkna upp samlingar, databaser osv. och deras konfigurationer kostnads fritt. | DatabaseName, samlings namn, region, StatusCode| Alla| |Används för att övervaka begränsningar på grund av metadata-begäranden.|
| MongoRequests (Mongo-begäranden) | Antal (antal) | Antal Mongo-begäranden som gjorts | DatabaseName, samlings namn, region, CommandName, ErrorCode| Alla |Mongo för begäran, Mongo uppdaterings frekvens, Mongo ta bort begär ande frekvens, Mongo infoga begär ande frekvens, antal Mongo för begäran| Används för att övervaka Mongo begär fel, användningar per kommando typ. |

#### <a name="request-unit-metrics"></a>Enhets mått för begäran

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Antal (totalt) |Mongo enheter för förbrukad begäran| DatabaseName, samlings namn, region, CommandName, ErrorCode| Alla |Mongo begär ande avgift, Mongo uppdaterings avgift, Mongo ta bort begär ande avgift, Mongo infoga begär ande avgift, antal Mongo avgift för begäran| Används för att övervaka Mongo Resource ru: er på en minut.|
| TotalRequestUnits (totalt antal enheter för programbegäran)| Antal (totalt) | Förbrukade enheter för begär Ande| DatabaseName, samlings namn, region, StatusCode |Alla| TotalRequestUnits| Används för att övervaka total användning av RU med en minuts kornig het. Använd total agg regering vid minut och dividera med 60 för att få Genomsnittligt antal RU-förbrukade per sekund.|
| ProvisionedThroughput (tillhandahållet data flöde)| Antal (max) |Tillhandahållet data flöde vid behållar kornig het| DatabaseName, ContainerName| 5M| | Används för att övervaka etablerade data flöden per behållare.|

#### <a name="storage-metrics"></a>Lagrings mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (tillgängligt lagrings utrymme) |Byte (totalt) | Totalt tillgängligt lagrings utrymme som har rapporter ATS enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5M| Tillgängligt lagringsutrymme| Används för att övervaka tillgänglig lagrings kapacitet (gäller endast för fasta lagrings samlingar) minimal kornig het är 5 minuter.| 
| DataUsage (data användning) |Byte (totalt) |Total data användning rapporterad enligt 5-minuters kornig het per region| DatabaseName, samlings region| 5M |Data storlek | Används för att övervaka total data användning i behållare och region, minsta kornig het är 5 minuter.|
| IndexUsage (index användning) | Byte (totalt) |Total användning av index rapporterad enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5M| Index storlek| Används för att övervaka total data användning i behållare och region, minsta kornig het är 5 minuter. |
| DocumentQuota (dokument kvot) | Byte (totalt) | Total lagrings kvot som rapporter ATS med 5 minuters kornig het per region.| DatabaseName, samlings region| 5M |Lagringskapacitet| Används för att övervaka den totala kvoten vid container och region, minimal kornig het är 5 minuter.|
| DocumentCount (antal dokument) | Antal (totalt) |Totalt antal dokument som rapporter ATS enligt 5 minuters kornig het per region| DatabaseName, samlings region| 5M |Antal dokument|Används för att övervaka antalet dokument i container och region, minimal kornig het är 5 minuter.|

#### <a name="latency-metrics"></a>Svars tids mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Användning |
|---|---|---|---| ---| ---|
| ReplicationLatency (replikeringsfördröjning)| Millisekunder (lägsta, högsta, genomsnitt) | P99 för replikering i käll-och mål regioner för geo-aktiverat konto| SourceRegion, TargetRegion| Alla | Används för att övervaka P99 för replikering mellan två regioner för ett geo-replikerat konto. |
| Svars tid på Server Sidan| Millisekunder (genomsnitt) | Tiden det tar för servern att bearbeta begäran. | Samlings namn, ConnectionMode, DatabaseName, OperationType, PublicAPIType, region | Alla | Används för att övervaka svars tiden för begäran på Azure Cosmos DB-servern. |



#### <a name="availability-metrics"></a>Tillgänglighets mått

|Mått (måttets visnings namn) |Enhet (agg regerings typ)|Beskrivning| Tids kornig het| Legacy mått mappning | Användning |
|---|---|---|---| ---| ---|
| ServiceAvailability (tjänst tillgänglighet)| Procent (minimum, max) | Konto begär tillgänglighet med en timkostnad| 1H | Tjänst tillgänglighet | Visar procent andelen slutförda begär Anden som skickats. En begäran anses vara misslyckad på grund av systemfel om status koden är 410, 500 eller 503 som används för att övervaka tillgängligheten för kontot med tids kornig het. |


#### <a name="cassandra-api-metrics"></a>API för Cassandra mått

|Mått (måttets visnings namn)|Enhet (agg regerings typ)|Beskrivning|Dimensioner| Tids kornig het| Användning |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-begäranden) | Antal (antal) | Antal API för Cassandra begär Anden som gjorts| DatabaseName, samlings namn, ErrorCode, region, OperationType, ResourceType| Alla| Används för att övervaka Cassandra-begäranden på en minuts kornig het. Om du vill få genomsnittlig begär Anden per sekund använder du Count-aggregering på minut och dividerar med 60.|
| CassandraRequestCharges (Cassandra Request avgifter) | Count (sum, min, Max, AVG) | Enheter för programbegäran som konsumeras av API för Cassandra | DatabaseName, samlings namn, region, OperationType, ResourceType| Alla| Används för att övervaka ru: er som används per minut av ett API för Cassandra-konto.|
| CassandraConnectionClosures (Cassandra Connections-stängningar) |Antal (antal) |Antal stängda Cassandra-anslutningar| ClosureReason, region| Alla | Används för att övervaka anslutningen mellan klienter och Azure Cosmos DB API för Cassandra.|

## <a name="see-also"></a>Se även

- En beskrivning av övervaknings Azure Cosmos DB finns i [övervaknings Azure Cosmos DB](monitor-cosmos-db.md) .
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) .
