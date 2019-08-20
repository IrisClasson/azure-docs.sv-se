---
title: Azure CLI-exempel för Azure Cosmos DB
description: Azure CLI-exempel – Skapa och hantera Azure Cosmos DB-konton, databaser, containrar, regioner och brandväggar.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d7ce5e2c12feeee770de8acfd5df81d340e0a7d0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615576"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure Cosmos DB. Det finns referenssidor för alla Azure Cosmos DB CLI-kommandon i [referensguiden för Azure CLI](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Skapa Azure Cosmos DB-konton, databaser och containrar**||
| [Skapa ett Azure Cosmos DB-konto med hjälp av SQL API](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett konto, en databas och en container i Azure Cosmos DB. |
| [Skapa ett Azure Cosmos DB-konto med hjälp av Cosmos DB:s API för MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto, en databas och en samling i Azure Cosmos DB. |
| [Skapa ett Azure Cosmos DB-konto med hjälp av Gremlin-API](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto, en databas och en graf i Azure Cosmos DB. |
| [Skapa ett Azure Cosmos DB-konto med hjälp av API för Cassandra](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto och en databas i Azure Cosmos DB. |
| [Skapa ett Azure Cosmos DB-konto med hjälp av Tabell-API](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto, en databas och en tabell i Azure Cosmos DB. |
|**Skala Azure Cosmos DB**||
| [Skala containerns dataflöde](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ändrar dataflöde i en container.|
| [Replikera Azure Cosmos Database-kontot i flera regioner och konfigurera prioriteter för redundans](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replikerar kontodata globalt till flera regioner med en angiven redundansprioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta kontonycklar](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar de primära och sekundära huvudskrivnycklarna och de primära och sekundära skrivskyddade nycklarna för kontot.|
| [Hämta en anslutningssträng för Cosmos-konto konfigurerat med Azure Cosmos DB:s API för MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar anslutningssträngen för att ansluta MongoDB-appen till Azure Cosmos DB-kontot.|
| [Återskapa kontonycklar](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Återskapa nycklar för kontot.|
| [Skapa en brandvägg](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en princip för inkommande IP-åtkomstkontroll för att begränsa åtkomsten till kontot från en godkänd uppsättning datorer och/eller molntjänster.|
|**Hög tillgänglighet, haveriberedskap, säkerhetskopiering och återställning**||
| [Konfigurera redundansprincip](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ställer in redundansprioritet för varje region i vilken kontot är replikerat.|
|**Ansluta Azure Cosmos DB till resurser**||
| [Ansluta en webbapp till Azure Cosmos DB](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Skapa och Anslut en Azure Cosmos-databas och en Azure-webbapp.|
|||
