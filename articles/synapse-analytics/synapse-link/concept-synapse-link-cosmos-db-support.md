---
title: Azure Synapse-länk (för hands version) för Azure Cosmos DB funktioner som stöds
description: Förstå den aktuella listan med åtgärder som stöds av Azure Synapse-länken för Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7fbc7b1cb8119a6ee9403bf0139380aa5dcd0613
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089132"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Azure Synapse-länk (för hands version) för Azure Cosmos DB funktioner som stöds

I den här artikeln beskrivs funktionerna som för närvarande stöds i Azure Synapse Link för Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Stöd för Azure Synapse

Det finns två typer av behållare i Azure Cosmos DB:
* HTAP container – en behållare med Synapse-länk aktive rad. Den här behållaren har både transaktions lager och analys lager. 
* OLTP-behållare – en behållare med endast transaktions lager; Synapse-länken är inte aktive rad. 

> [!IMPORTANT]
> Azure Synapse-länken för Azure Cosmos DB stöds för närvarande för arbets ytor som inte har hanterat virtuellt nätverk aktiverat. 

Du kan ansluta till en Azure Cosmos DB-behållare utan att aktivera Synapse-länken, i så fall kan du bara läsa/skriva till transaktions arkivet. Nedan visas en lista över de funktioner som stöds för närvarande i Synapse-länken för Azure Cosmos DB. 

| Kategori              | Beskrivning |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [SQL utan Server](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Stöd för körning** |Stöd för läsning eller skrivning av Azure Synapse körnings tid| ✓ | [Kontakta oss](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Stöd för Azure Cosmos DB-API** |API-stöd som en Synapse-länk| SQL/MongoDB | SQL/MongoDB |
| **Jobbobjektet**  |Objekt som en tabell som kan skapas, peka direkt på Azure Cosmos DB behållare| Visa, tabell | Vy |
| **Läsa**    |Läsa data från en Azure Cosmos DB-behållare| OLTP/HTAP | HTAP  |
| **Skriva**   |Skriva data från körnings tid till en Azure Cosmos DB-behållare| OLTP | saknas |

* Om du skriver data till en Azure Cosmos DB behållare från Spark sker den här processen genom transaktions arkivet för Azure Cosmos DB och påverkar transaktions prestandan för Azure Cosmos DB genom att använda enheter för programbegäran.
* SQL-pool-integrering via externa tabeller stöds inte för närvarande.

## <a name="supported-code-generated-actions-for-spark"></a>Kod genererade åtgärder som stöds för Spark

| Gest              | Beskrivning |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Läs in till DataFrame** |Läsa in och läsa data i en spark-DataFrame |X| ✓ |
| **Skapa Spark-tabell** |Skapa en tabell som pekar på en Azure Cosmos DB behållare|X| ✓ |
| **Skriv DataFrame till container** |Skriva data till en behållare|✓| ✓ |
| **Läs in strömmande DataFrame från behållare** |Strömma data med Azure Cosmos DB ändra feed|✓| ✓ |
| **Skriv strömmande DataFrame till behållare** |Strömma data med Azure Cosmos DB ändra feed|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Kod genererade åtgärder för SQL Server utan stöd

| Gest              | Beskrivning |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Välj översta 100** |Förhandsgranska de 100 objekten från en behållare|X| ✓ |
| **Skapa vy** |Skapa en vy för att direkt ha BI-åtkomst i en behållare via Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Nästa steg

* Se hur du [ansluter till Synapse-länken för Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Lär dig hur du frågar analys lagret med Spark](how-to-query-analytical-store-spark.md)
