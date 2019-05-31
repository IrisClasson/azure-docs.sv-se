---
title: Jämför med Azure SQL Database-funktioner | Microsoft Docs
description: Den här artikeln jämförs funktionerna i SQL Server som är tillgängliga i olika varianter av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
manager: craigg
ms.date: 05/10/2019
ms.openlocfilehash: 79cf4c713d60fa600bbb80b9c16728502ffc88ff
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236836"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Jämförelse av funktioner: Azure SQL Database jämfört med SQLServer

Azure SQL-databas delar en gemensam kodbas med SQL Server. Funktioner i SQL Server som stöds av Azure SQL Database beror på vilken typ av Azure SQL-databas som du skapar. Med Azure SQL Database kan du skapa en databas som en del av en [hanterad instans](sql-database-managed-instance.md), som en enkel databas eller som en del av en elastisk pool.

Microsoft fortsätter att lägga till funktioner till Azure SQL Database. Gå till webbsida för tjänstuppdateringar för Azure för de senaste uppdateringarna som använder filtren:

- Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrerade till allmän tillgänglighet [meddelanden (GA)](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Stöd för SQL Server-funktionen i Azure SQL Database

I följande tabell visas de viktigaste funktionerna i SQL Server och innehåller information om huruvida funktionen helt eller delvis stöds och en länk till mer information om funktionen.

| **SQL-funktionen** | **Stöds av enskilda databaser och elastiska pooler** | **Stöds av hanterade instanser** |
| --- | --- | --- |
| [Aktiv geo-replikering](sql-database-active-geo-replication.md) | Ja – alla tjänstnivåer än hyperskala | Nej, se [automatisk redundans groups(preview)](sql-database-auto-failover-group.md) som ett alternativ |
| [Automatiska redundansgrupper](sql-database-auto-failover-group.md) | Ja – alla tjänstnivåer än hyperskala | Ja, i [offentlig förhandsversion](sql-database-auto-failover-group.md)|
| [Alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ja – Se [certifikatarkiv](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md) | Ja – Se [certifikatarkiv](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On-Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas och [kan inte hanteras av användaren](sql-database-managed-instance-transact-sql-information.md#always-on-availability). Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Ansluta en databas](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nej | Nej |
| [Programroller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ja | Ja |
| [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ja](sql-database-auditing.md)| [Ja](sql-database-managed-instance-auditing.md), med några [skillnader](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Automatisk säkerhetskopiering](sql-database-automated-backups.md) | Ja. Fullständiga säkerhetskopior tas var 7 dagar, differentiella 12 timmar och loggsäkerhetskopior var 5-10 minuter. | Ja. Fullständiga säkerhetskopior tas var 7 dagar, differentiella 12 timmar och loggsäkerhetskopior var 5-10 minuter. |
| [Automatisk justering (markörplan)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| [Ja](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatisk justering (index)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| Nej |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Ja | Ja |
| [BACPAC-fil (exportera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja – Se [SQL Database-export](sql-database-export.md) | Ja – Se [SQL Database-export](sql-database-export.md) |
| [BACPAC-fil (import)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja – Se [SQL Database-import](sql-database-import.md) | Ja – Se [SQL Database-import](sql-database-import.md) |
| [BACKUP-kommandot](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nej, endast systeminitierade automatiska säkerhetskopior – Se [automatiska säkerhetskopior](sql-database-automated-backups.md) | Ja, användarinitierad endast kopiering säkerhetskopieringar till Azure Blob Storage (inte går att initiera automatiska säkerhetskopior av användare) – Se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Inbyggda funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se enskilda funktioner | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) | 
| [BULK INSERT-instruktion](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | Ja, men bara från Azure Blob storage som en källa. | Ja, men bara från Azure Blob Storage som en källa – Se [skillnader](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset). |
| [Certifikat och asymmetriska nycklar](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | Ja, utan åtkomst till filsystemet för `BACKUP` och `CREATE` åtgärder. | Ja, utan åtkomst till filsystemet för `BACKUP` och `CREATE` åtgärder - finns i [certifikat skillnader](sql-database-managed-instance-transact-sql-information.md#certificates). | 
| [Sammanställning av ändringsdata](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nej | Ja |
| [Spårning av ändringar](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ja |Ja |
| [Sortering - databas](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Ja | Ja |
| [Sortering - server/instans](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Nej, Standardsortering logisk server `SQL_Latin1_General_CP1_CI_AS` används alltid. | Ja, kan anges när den [instans skapas](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) och kan inte uppdateras senare. |
| [Columnstore-index](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ja – [Premium-nivån, Standard-nivån – S3 och senare, nivån generell användning och affärskritisk nivåer](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Ja |
| [CLR (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nej | Ja, men inte har tillgång till filsystemet i `CREATE ASSEMBLY` statement - Se [CLR-skillnader](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ja | För närvarande ingen [på grund av defekt återställning inklusive point-in-time-återställning](sql-database-managed-instance-transact-sql-information.md#cant-restore-contained-database). Det här är ett fel som kommer att åtgärdas snart. |
| [Inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ja | Ja |
| [Kontroll av flödesspråk](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ja | Ja |
| [Autentiseringsuppgifter](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | Ja, men endast [databasbegränsade autentiseringsuppgifter](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). | Ja, men endast **Azure Key Vault** och `SHARED ACCESS SIGNATURE` som stöds finns i [information](sql-database-managed-instance-transact-sql-information.md#credential) |
| [Frågor mellan databaser](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej, se [elastiska frågor](sql-database-elastic-query-overview.md) | Ja, plus [elastiska frågor](sql-database-elastic-query-overview.md) |
| [Transaktioner över flera databaser](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej | Ja, i instansen. Se [länkad server skillnader](sql-database-managed-instance-transact-sql-information.md#linked-servers) för frågor över flera instanser. |
| [Markörer](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ja |Ja |
| [Datakomprimering](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ja |Ja |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nej | Ja |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Ja | Ja |
| [Databasspegling](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nej | [Nej](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [Databaskonfigurationsinställningar](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ja | Ja |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nej | Nej |
| [Databasögonblicksbilder](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nej | Nej |
| [Datatyper](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ja |Ja |
| [DBCC-uttryck](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | De flesta – se enskilda uttryck | Ja – Se [DBCC skillnader](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL-instruktionerna](https://docs.microsoft.com/sql/t-sql/statements/statements) | De flesta – se enskilda uttryck | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [DDL-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Endast databas |  Ja |
| [Distribuerade partition vyer](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nej | Ja |
| [Distribuerade transaktioner - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nej, se [elastiska transaktioner](sql-database-elastic-transactions-overview.md) |  Nej, se [länkad server skillnader](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML-instruktioner](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ja | Ja |
| [DML-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | De flesta – se enskilda uttryck |  Ja |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | De flesta – se enskilda DMV: er |  Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Ja](sql-database-dynamic-data-masking-get-started.md)| [Ja](sql-database-dynamic-data-masking-get-started.md) |
| [Elastiska pooler](sql-database-elastic-pool.md) | Ja | Inbyggda – en enda hanterad instans kan ha flera databaser som delar samma pool av resurser |
| [Händelseaviseringar](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nej, se [aviseringar](sql-database-insights-alerts-portal.md) | Nej |
| [Uttryck](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ja | Ja |
| [Utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Vissa – Se [utökade händelser i SQL-databas](sql-database-xevent-db-diff-from-svr.md) | Ja – Se [utökade händelser skillnader](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nej | Nej |
| [Filer och filgrupper](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Endast primär filgrupp | Ja. Sökvägar tilldelas automatiskt och Filplatsen kan inte anges i `ALTER DATABASE ADD FILE` [instruktionen](sql-database-managed-instance-transact-sql-information.md#alter-database-statement).  |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nej | [Nej](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [Fulltextsökning](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Ja, men orddelare från tredje part stöds inte | Ja, men [orddelare från tredje part stöds inte](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) |
| [Funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se enskilda funktioner | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [GEO-återställning](sql-database-recovery-using-backups.md#geo-restore) | Ja – alla tjänstnivåer än hyperskala | Ja – med hjälp av [Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa). |
| [Bearbeta diagram](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Ja | Ja |
| [Minnesintern optimering](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ja – [Premium och affärskritisk nivåer](sql-database-in-memory.md) | Ja – [företag affärskritisk nivå](sql-database-managed-instance.md) |
| [Stöd för JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Ja](sql-database-json-features.md) | [Ja](sql-database-json-features.md) |
| [Språkelement](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | De flesta – se enskilda element |  Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Länkade servrar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej, se [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) | Endast [SQLServer och SQL-databas](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [Loggöverföring](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |[Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Inloggningar och användare](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | Ja, men `CREATE` och `ALTER` inloggning instruktioner erbjuder inte alla alternativ (inga Windows och på servernivå Azure Active Directory-inloggningar). `EXECUTE AS LOGIN` har inte stöd för – använda `EXECUTE AS USER` i stället.  | Ja, med några [skillnader](sql-database-managed-instance-transact-sql-information.md#logins-and-users). Windows-inloggningar stöds inte och de ska ersättas med Azure Active Directory-inloggningar. |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nej | Nej |
| [Minimal loggning i massimport](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nej | Nej |
| [Ändra systemdata](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nej | Ja |
| [OLE-Automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | Nej | Nej |
| [Indexåtgärder online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ja | Ja |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nej|Ja, endast till andra Azure SQL-databaser och SQL-servrar. Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Ja|Ja|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nej|Ja, endast till andra Azure SQL-databaser och SQL-servrar. Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Ja, bara om du vill importera från Azure Blob storage. |Ja, endast till andra Azure SQL-databaser och SQL-servrar och för att importera från Azure Blob storage. Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Ja|Ja|
| [Operatörer](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | De flesta – se enskilda operatorer |Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Partitionering](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Ja | Ja |
| Offentlig IP-adress | Ja. Åtkomsten kan begränsas med brandväggen eller tjänstens slutpunkter.  | Ja. Måste vara explicit aktiverad och port 3342 måste vara aktiverat i NSG-regler. Offentlig IP-adress kan inaktiveras om det behövs. Se [offentlig slutpunkt](sql-database-managed-instance-public-endpoint-securely.md) för mer information. | 
| [Tidpunkt för återställning av databasen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ja – alla tjänstnivåer än hyperskala – Se [återställning av SQL-databas](sql-database-recovery-using-backups.md#point-in-time-restore) | Ja – Se [återställning av SQL-databas](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nej | Nej |
| [Principbaserad hantering](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nej | Nej |
| [Predikat](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ja | Ja |
| [Frågemeddelanden](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Nej | Ja |
| [Query Performance Insights](sql-database-query-performance.md) | Ja | Nej |
| [R-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Ja, i [offentlig förhandsversion](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nej |
| [Resursstyrning](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nej | Ja |
| [RESTORE-uttryck](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nej | Ja, med obligatorisk `FROM URL` alternativ för säkerhetskopior av filer placeras i Azure Blob Storage. Se [återställa skillnader](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Återställa databasen från en säkerhetskopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Data från automatiska säkerhetskopieringar endast – se [återställning av SQL-databas](sql-database-recovery-using-backups.md) | Data från automatiska säkerhetskopieringar – Se [SQL Database recovery](sql-database-recovery-using-backups.md) och fullständiga säkerhetskopieringar för Azure Blob Storage - Se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ja | Ja |
| [Semantisk sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nej | Nej |
| [Sekvensnummer](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ja | Ja |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nej | Ja, men endast inom instansen. Se [Service Broker-skillnader](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Inställningar för serverkonfiguration](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nej | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Ange uttryck](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | De flesta – se enskilda uttryck | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [Ja](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | Ja [version 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [Rumslig](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ja | Ja |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Ja | Ja |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Ja | Nej |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nej, se [elastiska jobb](elastic-jobs-overview.md) | Ja – Se [SQL Server Agent-skillnader](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nej, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) är en separat Azure-molntjänst. | Nej, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) är en separat Azure-molntjänst. |
| [SQL Server-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nej, se [SQL Database-granskning](sql-database-auditing.md) | Ja – Se [granskning skillnader](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Ja | Ja |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ja, med en hanterade SSIS i Azure Data Factory (ADF)-miljö, var paket lagras i SSISDB med Azure SQL Database och körs på Azure SSIS Integration Runtime (IR), se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Jämförelse mellan SSIS-funktioner i SQL Database-server och Managed Instance finns [jämför Azure SQL Database enkel databaser/elastiska pooler och Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Ja, med en hanterade SSIS i Azure Data Factory (ADF)-miljö, var paket lagras i SSISDB med Managed Instance och körs på Azure SSIS Integration Runtime (IR), se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Jämförelse mellan SSIS-funktioner i SQL Database och Managed Instance finns [jämför Azure SQL Database enkel databaser/elastiska pooler och Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Ja | Ja [version 18,0 och högre](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ja | Ja |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nej, se [utökade händelser](sql-database-xevent-db-diff-from-svr.md) | Ja |
| [SQL Server-replikering](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Prenumerant för transaktions-och ögonblicksbildsreplikering endast](sql-database-single-database-migrate.md) | Ja, i [offentlig förhandsversion](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nej, [Se Power BI](https://docs.microsoft.com/power-bi/) | Nej, [Se Power BI](https://docs.microsoft.com/power-bi/) |
| [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ja | Ja |
| [Systemlagrade funktioner](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | De flesta – se enskilda funktioner | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [Systemlagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Vissa – se enskilda lagrade procedurer | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [Systemtabeller](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Vissa – se enskilda tabeller | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Systemkatalogvyer](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Vissa – se enskilda vyer | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Temporary tables](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) (Temporära tabeller) | Lokal och databasbegränsade globala tillfälliga tabeller | Lokal och instans-omfattande globala tillfälliga tabeller |
| [Temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Ja](sql-database-temporal-tables.md) | [Ja](sql-database-temporal-tables.md) |
| Val av tidszon | Nej | [Yes(Preview)](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone) |
| Identifiering av hot|  [Ja](sql-database-threat-detection.md)|[Ja](sql-database-managed-instance-threat-detection.md)|
| [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nej | Nej |
| [Variabler](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ja | Ja |
| [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ja – generell användning och affärskritisk tjänstnivåer endast| [Ja](transparent-data-encryption-azure-sql.md) |
| [VNet](../virtual-network/virtual-networks-overview.md) | Delvis – Se [slutpunkter för virtuellt nätverk](sql-database-vnet-service-endpoint-rule-overview.md) | Ja, Resource Manager-modellen |
| [Windows Server-Redundansklustring](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Information om en hanterad instans finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
