---
title: Azure PowerShell-exempel för Azure Cosmos DB
description: Hämta Azure PowerShell exempel för att utföra vanliga uppgifter i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506660"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till vanliga Azure PowerShell skript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB PowerShell-cmdletar finns i [referensen Azure PowerShell](/powershell/module/az.cosmosdb). Sök efter uppdateringar `Az.CosmosDB` regelbundet. Du kan också förgrena dessa PowerShell-exempel för Cosmos DB från vår GitHub-lagringsplats [Cosmos DB PowerShell-exempel på GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Vanliga exempel

|Uppgift | Beskrivning |
|---|---|
|[Uppdatera ett konto](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera standard konsekvens nivån för ett Cosmos DB-konto. |
|[Uppdatera ett kontos regioner](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera ett Cosmos DB kontos regioner. |
|[Ändra växlings prioritet eller utlös redundans](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala växlings prioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundansväxling. |
|[Konto nycklar eller anslutnings strängar](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutnings strängar eller återskapa en konto nyckel för ett Azure Cosmos DB konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto med IP-brandvägg aktive rad. |
|||

## <a name="core-sql-api-samples"></a>API-exempel för Core (SQL)

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, en databas och en behållare](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB konto, en databas och en behållare. |
|[Skapa ett konto, en databas och en behållare med autoskalning](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB konto, en databas och en behållare med autoskalning. |
|[Skapa en behållare med en stor partitionsnyckel](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa en behållare med en stor partitionsnyckel. |
|[Skapa en behållare utan index princip](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en Azure Cosmos-behållare med index princip inaktive rad.|
|[Lista eller hämta databaser eller behållare](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller behållare. |
|[Beräkna dataflöde](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta data flöde för en databas eller behållare. |
|[Uppdatera data flöde](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera data flödet för en databas eller behållare. |
|[Lås resurser från borttagning](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

## <a name="cassandra-api-samples"></a>API för Cassandra exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, ett tomt utrymme och en tabell](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, ett blank steg och en tabell. |
|[Skapa ett konto, ett nyckel utrymme och en tabell med autoskalning](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, nyckel utrymme och tabell med autoskalning. |
|[Lista eller hämta blank steg eller tabeller](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta blank steg eller tabeller. |
|[Beräkna dataflöde](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta data flöde för ett tecken utrymme eller en tabell. |
|[Uppdatera data flöde](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera data flödet för ett tecken utrymme eller en tabell. |
|[Lås resurser från borttagning](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API-exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, en databas och en samling](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, en databas och en samling. |
|[Skapa ett konto, en databas och en samling med autoskalning](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, en databas och en samling med autoskalning. |
|[Lista eller hämta databaser eller samlingar](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller samling. |
|[Beräkna dataflöde](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta data flöde för en databas eller samling. |
|[Uppdatera data flöde](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera data flödet för en databas eller samling. |
|[Lås resurser från borttagning](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

## <a name="gremlin-api-samples"></a>Gremlin API-exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, en databas och en graf](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, en databas och en graf. |
|[Skapa ett konto, en databas och en graf med autoskalning](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, en databas och en graf med autoskalning. |
|[Visa eller hämta databaser eller diagram](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Visa eller hämta databas eller diagram. |
|[Beräkna dataflöde](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta data flöde för en databas eller graf. |
|[Uppdatera data flöde](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera data flödet för en databas eller graf. |
|[Lås resurser från borttagning](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

## <a name="table-api-samples"></a>Tabell-API exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto och en tabell](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto och en tabell. |
|[Skapa ett konto och en tabell med autoskalning](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto och en tabell autoskalning. |
|[Lista eller hämta tabeller](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Visa eller hämta tabeller. |
|[Beräkna dataflöde](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta data flöde för en tabell. |
|[Uppdatera data flöde](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera data flödet för en tabell. |
|[Lås resurser från borttagning](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||
