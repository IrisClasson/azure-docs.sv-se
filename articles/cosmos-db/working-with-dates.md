---
title: Arbeta med datum i Azure Cosmos DB
description: Läs mer om hur du arbetar med datum i Azure Cosmos DB.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 5b482d00990add365a90f36bf6e8d047dc50efc0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467822"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Arbeta med datum i Azure Cosmos DB
Azure Cosmos DB ger schemaflexibilitet och omfattande indexering via ett ursprungligt [JSON](https://www.json.org) datamodellen. Alla Azure Cosmos DB-resurser, inklusive databaser, behållare, dokument och lagrade procedurer modelleras och lagras som JSON-dokument. Som ett krav för portabelt stöder JSON (och Azure Cosmos DB) bara en liten uppsättning grundläggande typer: Sträng, tal, boolesk, matris, objekt och null. JSON är flexibel och tillåta utvecklare och ramverk för att representera mer komplexa typer med hjälp av dessa primitiver och skriva dem som objekt eller matriser. 

Förutom grundläggande typer, många program måste den [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) typ som representerar datum och tidsstämplar. Den här artikeln beskriver hur utvecklare kan lagra, hämta och fråga efter datum i Azure Cosmos DB med .NET SDK.

## <a name="storing-datetimes"></a>Lagra datum och tid
Som standard den [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) Serialiserar DateTime-värden som [ISO 8601](https://www.iso.org/iso/catalogue_detail?csnumber=40874) strängar. De flesta program kan använda standard-strängrepresentation för DateTime av följande skäl:

* Strängar kan jämföras och relativa ordning av datum/tid-värden bevaras när de omvandlas till strängar. 
* Den här metoden kräver inte någon anpassad kod eller ett attribut för JSON-konvertering.
* Datum som lagras i JSON är mänskliga läsbar.
* Den här metoden kan dra nytta av Azure Cosmos DB-index för snabb frågeprestanda.

Till exempel följande kodavsnitt lagrar en `Order` objekt som innehåller två datum/tid-egenskaper – `ShipDate` och `OrderDate` som ett dokument med hjälp av .NET-SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Det här dokumentet lagras i Azure Cosmos DB på följande sätt:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Du kan också lagra datum och tid som Unix tidsstämplar, det vill säga som ett tal som representerar antalet förflutna sekunder sedan den 1 januari 1970. Azure Cosmos DB interna tidsstämpel (`_ts`) egenskapen följer den här metoden. Du kan använda den [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) klassen för att serialisera datum och tid som tal. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexering datum och tid för intervallfrågor
Omfångsfrågor är vanliga med DateTime-värden. Om du behöver hitta alla beställningar som skapats sedan igår eller hitta alla beställningar som levereras under de senaste fem minuterna, måste du utföra intervallfrågor. Om du vill köra dessa frågor effektivt, måste du konfigurera din samling för intervallet indexering på strängar.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Du kan lära dig mer om hur du konfigurerar indexering principer på [indexera Azure Cosmos DB-principer](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Förfrågningar till datum och tid i LINQ
SQL .NET SDK har automatiskt stöd för frågor till data som lagras i Azure Cosmos DB via LINQ. Följande kodavsnitt visar exempelvis en LINQ-frågan som filter order som levererades under de senaste tre dagarna.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Du kan lära dig mer om Azure Cosmos DB SQL-frågespråket och LINQ-providern på [frågor till Cosmos DB](how-to-sql-query.md).

I den här artikeln har tittat vi på hur du lagrar, index- och fråga efter datum och tid i Azure Cosmos DB.

## <a name="next-steps"></a>Nästa steg
* Ladda ned och kör den [kodexempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Läs mer om [SQL-frågor](how-to-sql-query.md)
* Läs mer om [indexera Azure Cosmos DB-principer](index-policy.md)
