---
title: SQL Server migrering av databasen till Azure SQL Database
description: Lär dig mer om migrering av SQL Server Database till Azure SQL Database.
keywords: databasmigrering, sql server-databasmigrering, databasmigreringsverktyg, migrera databas, migrera sql-databas
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 106337fb4756052ee682624290620093bf4a70b3
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86081952"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server migrering av databasen till Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln får du lära dig om de primära metoderna för att migrera en SQL Server 2005-databas eller senare till Azure SQL Database. Information om hur du migrerar till en Azure SQL-hanterad instans finns i [Migrera en SQL Server instans till en Azure SQL-hanterad instans](../managed-instance/migrate-to-instance-from-sql-server.md). Information om migrering om migrering från andra plattformar finns i [guiden Migrera Azure Database](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrera till en enskild databas eller en databas i poolen

Det finns två primära metoder för att migrera en SQL Server 2005-databas eller senare till Azure SQL Database. Den första metoden är enklare, men kräver viss till omfattande stilleståndstid under migreringen. Den andra metoden är mer komplicerad, men kräver mycket kortare stilleståndstid under migreringen.

I båda fallen måste du se till att käll databasen är kompatibel med Azure SQL Database med hjälp av [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database närmar sig [funktions paritet](features-comparison.md) med SQL Server, förutom problem som rör åtgärder på server nivå och mellan databaser. Databaser och program som förlitar sig på [funktioner som delvis eller inte stöds](transact-sql-tsql-differences-sql-server.md) behöver viss [omkonstruktion för att åtgärda dessa inkompatibiliteter](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) innan SQL Server-databasen kan migreras.

> [!NOTE]
> För att migrera en icke-SQL Server-databas, inklusive Microsoft Access, Sybase, MySQL Oracle och DB2 till Azure SQL Database, se [SQL Server-migreringsassistent](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Metod 1: Migrering med stilleståndstid under migreringen

 Använd den här metoden om du vill migrera till en enskild databas eller en databas i poolen om du har ett visst avbrott eller om du utför en testmigrering av en produktions databas för senare migrering. En själv studie kurs finns i [Migrera en SQL Server databas](../../dms/tutorial-sql-server-to-azure-sql.md).

Följande lista innehåller det allmänna arbets flödet för en SQL Server Database-migrering av en eller flera databaser i en pool med den här metoden. För migrering till SQL-hanterad instans, se [migrering till SQL-hanterad instans](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![VSSSDT-migreringsdiagram](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Utvärdera](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) databasen för kompatibilitet med hjälp av den senaste versionen av [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Förbered nödvändiga korrigeringar som Transact-SQL-skript.
3. Gör en transaktions konsekvent kopia av käll databasen som migreras eller stoppa nya transaktioner från det som inträffar i käll databasen medan migreringen sker. Metoder för att göra detta senare alternativ är att inaktivera klient anslutning eller att skapa en [databas ögonblicks bild](https://msdn.microsoft.com/library/ms175876.aspx). Efter migreringen kan du använda Transaktionsreplikering för att uppdatera de migrerade databaserna med ändringar som inträffar efter den sista punkten för migreringen. Se [migrera med hjälp av transaktionell migrering](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Distribuera Transact-SQL-skripten för att tillämpa korrigeringar på databaskopian.
5. [Migrera](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) databas kopian till en ny databas i Azure SQL Database med hjälp av data migration assistant.

> [!NOTE]
> I stället för att använda DMA kan du också använda en BACPAC-fil. Mer information finns [i Importera en BACPAC-fil till en ny databas i Azure SQL Database](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimera prestanda för dataöverföring under migreringen

Följande lista innehåller rekommendationer för bästa prestanda under importen.

- Välj den högsta tjänst nivån och den beräknings storlek som din budget tillåter för att maximera överförings prestandan. Du kan spara pengar genom att skala ned när migreringen är klar.
- Minimera avståndet mellan BACPAC-filen och mål data centret.
- Inaktivera autostatistik under migrering
- Partitionstabeller och index
- Ta bort indexerade vyer och återskapa dem när de är klara
- Ta bort sällan efterfrågade historiska data till en annan databas och migrera dessa historiska data till en separat databas i Azure SQL Database. Du kan sedan söka i historiska data med [elastiska frågor](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimera prestanda när migreringen är klar

[Uppdatera statistik](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) med fullständig sökning när migreringen har slutförts.

## <a name="method-2-use-transactional-replication"></a>Metod 2: Använd transaktionsreplikering

När du inte har råd att ta bort din SQL Server databas från produktionen medan migreringen sker, kan du använda Transaktionsreplikering för SQL Server som migrerings lösning. För att kunna använda den här metoden måste källdatabasen uppfylla [kraven för transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx) och vara kompatibel med Azure SQL Database. Information om SQL-replikering med Always on finns i [Konfigurera replikering för Always on Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Om du vill använda den här lösningen konfigurerar du databasen i Azure SQL Database som prenumerant på den SQL Server-instans som du vill migrera. Distributören av transaktionsreplikeringen synkroniserar de data som ska synkroniseras från databasen (utgivaren) medan nya transaktioner fortsätter att göras.

Med transaktionell replikering visas alla ändringar i dina data eller schema i databasen i Azure SQL Database. När synkroniseringen är klar och du är redo att migrera, ändrar du anslutnings strängen för dina program så att de pekar på databasen. När transaktionsreplikeringen tömt alla ändringar som finns kvar i källdatabasen och alla program pekar på Azure DB kan du avinstallera transaktionsreplikeringen. Din databas i Azure SQL Database är nu ditt produktions system.

 ![SeedCloudTR-diagram](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Du kan också använda transaktionsreplikering till att migrera en del av din källdatabas. Den publikation som du replikerar till Azure SQL Database kan begränsas till en del av tabellerna i databasen som replikeras. Du kan begränsa data till en del av raderna och/eller en del av kolumnerna för varje tabell som replikeras.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrera till SQL Database med arbetsflödet för transaktionsreplikering

> [!IMPORTANT]
> Använd den senaste versionen av SQL Server Management Studio om du fortfarande vill synkronisera med uppdateringar till Azure och SQL Database. Äldre versioner av SQL Server Management Studio kan inte konfigurera SQL Database som en prenumerant. [Uppdatera SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Konfigurera distribution
   - [Med SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Med Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Skapa publikation
   - [Med SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Med Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Skapa en prenumeration
   - [Med SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Med Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Tips och skillnader vid migrering till SQL Database

- Använda en lokal distributör
  - Detta leder till en prestanda påverkan på servern.
  - Om denna påverkan inte är acceptabel kan du använda en annan server men det innebär en mer komplicerad hantering och administration.
- När du väljer en mapp för ögonblicksbilder måste du se till att mappen är tillräckligt stor för att innehålla en BCP för varje tabell som du vill replikera.
- Skapandet av ögonblicks bilder låser de associerade tabellerna tills den har slutförts, så Schemalägg din ögonblicks bild på lämpligt sätt.
- Endast push-prenumerationer stöds i Azure SQL Database. Du kan bara lägga till prenumeranter från källdatabasen.

## <a name="resolving-database-migration-compatibility-issues"></a>Åtgärda kompatibilitetsproblem vid databasmigrering

Det finns många olika kompatibilitetsproblem som du kan stöta på, beroende på vilken version av SQL Server i käll databasen och hur komplex databasen som du migrerar. Äldre versioner av SQL Server har fler kompatibilitetsproblem. Använd följande resurser, utöver en riktad Internetsökning med hjälp av sökmotor:

- [SQL Server-databasfunktioner som inte stöds i Azure SQL Database](transact-sql-tsql-differences-sql-server.md)
- [Utgångna databasmotorfunktioner i SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Utgångna databasmotorfunktioner i SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Utgångna databasmotorfunktioner i SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Utgångna databasmotorfunktioner i SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Utgångna databasmotorfunktioner i SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Förutom att söka på Internet och använda dessa resurser använder du [sidan Microsoft Q&en fråga för Azure SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html) eller [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Med Azure SQL Managed instance kan du migrera en befintlig SQL Server-instans och dess databaser med minimalt antal kompatibilitetsproblem. Se [Vad är en hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Nästa steg

- Använd skriptet i Azure SQL EMEA Engineers-bloggen för att [övervaka tempdb-användningen vid migrering](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/) (på engelska).
- Använd skriptet i Azure SQL EMEA Engineers-bloggen för att [övervaka transaktionsloggutrymmet i databasen medan migreringen pågår](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database) (på engelska).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- Information om hur du arbetar med UTC-tid efter migreringen finns i [Ändra standardtidszon för den lokala tidszonen](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (på engelska).
- Information om hur du ändrar standardspråk för en databas efter migreringen finns [Ändra standardspråk för Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (på engelska).
 