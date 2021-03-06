---
title: Prestandajustering med cachelagring av resultatuppsättningar
description: Översikt över cachelagring av resultat uppsättningar för SQL-poolen Synapse i Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 919f3105c1ee9cc8cbdfaf413be56f6dd6291683
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212691"
---
# <a name="performance-tuning-with-result-set-caching"></a>Prestandajustering med cachelagring av resultatuppsättningar

När cachelagring av resultat uppsättningar är aktiverat cachelagrar Synapse SQL automatiskt frågeresultat i användar databasen för upprepad användning.  Detta gör att efterföljande fråge körningar kan hämta resultat direkt från det sparade cacheminnet så att omberäkning inte behövs.   Cachelagring av resultat uppsättningar förbättrar prestanda för frågor och minskar användningen av beräknings resurser.  Dessutom använder frågor som använder cachelagrade resultat uppsättningar inte några samtidiga platser och räknas därför inte över mot befintliga samtidighets gränser. Användare kan bara komma åt de cachelagrade resultaten om de har samma data åtkomst behörigheter som de användare som skapar de cachelagrade resultaten.  

## <a name="key-commands"></a>Nyckel kommandon

[Aktivera/inaktivera cachelagring av resultat uppsättningar för en användar databas](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Aktivera/inaktivera cachelagring av resultat uppsättningar för en användar databas](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Aktivera/inaktivera cachelagring av resultat uppsättningar för en session](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Kontrol lera storleken på den cachelagrade resultat uppsättningen](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Rensa cacheminnet](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Vad är inte cachelagrat  

När cachelagring av resultat uppsättningar har Aktiver ATS för en databas cachelagras resultaten för alla frågor tills cachen är full, förutom följande frågor:

- Frågor som använder icke-deterministiska funktioner som DateTime. Now ()
- Frågor med användardefinierade funktioner
- Frågor som använder tabeller med säkerhet på radnivå eller på kolumn nivå har Aktiver ATS
- Frågor som returnerar data med en rad storlek som är större än 64 KB
- Frågor som returnerar stora data i storlek (>10 GB) 

> [!IMPORTANT]
> Åtgärderna för att skapa en resultat uppsättning cache och hämta data från cachen sker på noden kontroll i en Synapse SQL-instans.
> När cachelagring av resultat uppsättningar är aktiverat kan frågor som returnerar stor resultat uppsättning (till exempel >1 GB) orsaka hög begränsning på noden och sakta ned det övergripande fråge svaret på instansen.  Frågorna används ofta vid data utforskning eller ETL-åtgärder. För att undvika att kontrol lera noden och orsaka prestanda problem bör användarna stänga av resultat uppsättningens cachelagring i databasen innan du kör dessa typer av frågor.  

Kör den här frågan för den tid som krävs för cachelagring av resultat uppsättningar för en fråga:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Här är ett exempel på utdata för en fråga som körs med mellanlagring av resultat uppsättning inaktive rad.

![Fråga – steg-med-RSC-inaktive rad](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Här är ett exempel på utdata för en fråga som körs med cachelagring av resultat uppsättningar aktiverat.

![Fråga – steg-för-RSC-aktiverat](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>När cachelagrade resultat används

Den cachelagrade resultat uppsättningen återanvänds för en fråga om alla följande krav är uppfyllda:

- Användaren som kör frågan har åtkomst till alla tabeller som refereras i frågan.
- Det finns en exakt matchning mellan den nya frågan och den föregående frågan som genererade resultat uppsättningens cacheminne.
- Det finns inga data eller schema ändringar i tabellerna där den cachelagrade resultat uppsättningen genererades.

Kör det här kommandot för att kontrol lera om en fråga kördes med en resultat-cacheträffar eller missar. I kolumnen result_cache_hit returneras 1 för cacheträff, 0 för cache-missar och negativa värden av skäl varför cachelagring av resultat uppsättningar inte användes. Mer information finns i [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Hantera cachelagrade resultat

Den maximala storleken för cache för resultat uppsättning är 1 TB per databas.  De cachelagrade resultaten blir automatiskt ogiltiga när den underliggande frågans data ändras.  

Cache-avtagningen hanteras av Synapse SQL automatiskt enligt det här schemat:

- Var 48: e timme om resultat uppsättningen inte har använts eller har ogiltig förklarats.
- När cachen för resultat uppsättningen närmar sig den maximala storleken.

Användare kan manuellt tömma hela resultat uppsättningens cacheminne genom att använda något av följande alternativ:

- Inaktivera funktionen för cachelagring av resultat uppsättningar för databasen
- Kör DBCC DROPRESULTSETCACHE medan du är ansluten till databasen

Om du pausar en databas töms inte cachelagrad resultat uppsättning.  

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).
