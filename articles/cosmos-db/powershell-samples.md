---
title: Azure PowerShell-exempel för Azure Cosmos DB
description: Azure PowerShell-exempel – Skript som hjälper dig att skapa och hantera Azure Cosmos DB-konton.
services: cosmos-db
author: SnehaGunda
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: c2bd1ac165e6b17d1bd9604fb1203ae47214278f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871038"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure Cosmos DB. Just nu kan du bara hantera Azure Cosmos DB-kontot via PowerShell. Andra resurser, som databaser och containrar, kan inte hanteras via PowerShell.

| |  |
|---|---|
|**Skapa ett Azure Cosmos DB-konto**||
|[Skapa ett SQL API-konto](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med SQL-API:et. |
|[Skapa ett MongoDB API-konto](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med MongoDB API:et. |
|[Skapa ett Gremlin API-konto](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Gremlin API:et. |
|[Skapa ett Cassandra API-konto](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Cassandra-API:et. |
|[Skapa ett Table API-konto](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Table-API:et. |
|**Skala Azure Cosmos DB**||
|[Replikera ett Azure Cosmos DB-konto i flera regioner och konfigurera redundansprioriteringar](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replikerar kontodata globalt till flera regioner med en angiven redundansprioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta kontonycklar](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar de primära och sekundära huvudskrivnycklarna och de primära och sekundära skrivskyddade nycklarna för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar anslutningssträngen för att ansluta MongoDB-appen till Azure Cosmos DB-kontot.|
|[Återskapa kontonycklar](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Återskapar huvudnyckeln eller den skrivskyddade nyckeln för kontot.|
|[Skapa en brandvägg](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en princip för inkommande IP-åtkomstkontroll för att begränsa åtkomsten till kontot från en godkänd uppsättning datorer och/eller molntjänster.|
|**Hög tillgänglighet, haveriberedskap, säkerhetskopiering och återställning**||
|[Konfigurera redundansprincip](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ställer in redundansprioritet för varje region i vilken kontot är replikerat.|
|||
