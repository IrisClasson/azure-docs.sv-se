---
title: Självstudie om global distribution i Azure Cosmos DB för tabell-API
description: Lär mer om hur du konfigurerar global distribution i Azure Cosmos DB med tabell-API.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2017
ms.reviewer: sngun
ms.openlocfilehash: d7d68c2dbdf5ca32fb2936e92daafac838c97a06
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042538"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Konfigurera global distribution i Azure Cosmos DB med tabell-API

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [tabell-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Ansluta till en önskad region med hjälp av tabell-API

I syfte att dra nytta av den [globala distributionen](distribute-data-globally.md) kan klientprogrammen ange den beställda listan med inställningar för regioner som ska användas för att utföra dokumentåtgärder. Detta kan göras genom att ange egenskapen [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations). Tabell-API:ns SDK i Azure Cosmos DB väljer den bästa slutpunkten för kommunikationen baserat på kontokonfiguration, aktuell regional tillgänglighet och listan med inställningar.

PreferredLocations ska innehålla en kommaavgränsad lista med önskade (flera värdar) platser för läsningar. Varje klientinstans kan ange en delmängd av dessa regioner i önskad ordning för läsningar med låg latens. Regionerna måste namnges med sina [visningsnamn](https://msdn.microsoft.com/library/azure/gg441293.aspx), till exempel `West US`.

Alla läsningar skickas till den första tillgängliga regionen i PreferredLocations-listan. Om begäran misslyckas går klienten nedåt i listan till nästa region osv.

SDK:erna försöker läsa från de regioner som anges i PreferredLocations. Det innebär att om databaskontot t.ex. är tillgängligt i tre regioner men klienten enbart anger två av de skrivskyddade regionerna för PreferredLocations, så hanteras inga läsningar från skrivregionen även om en redundans sker.

SDK:n skickar automatiskt alla skrivningar till den aktuella skrivregionen.

Om egenskapen PreferredLocations inte har angetts hanteras alla förfrågningar från den aktuella skrivregionen.

Och med detta är den här självstudiekursen klar. Mer information om hur du kan hantera ditt globalt replikerade kontos konsekvens finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Mer information om hur global databasreplikering fungerar i Azure Cosmos DB finns i [Distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med tabell-API:n i Azure Cosmos DB

