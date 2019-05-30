---
title: Azure SQL Database viktig information | Microsoft Docs
description: Läs mer om nya funktioner och förbättringar i Azure SQL Database-tjänsten och i dokumentationen för Azure SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: d527c4fed9c43e62d815078c049d4d8e6f8a46b7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787719"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL-databas

Den här artikeln innehåller nya funktioner och förbättringar i tjänsten SQL Database och SQL Database-dokumentationen. Förbättringar för SQL Database-tjänsten, finns även [uppdateringar av tjänsten SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funktioner i offentlig förhandsversion

| Funktion | Information |
| ---| --- |
| Accelererad databasåterställning med enskilda databaser och elastiska pooler | Mer information finns i [accelererad databasåterställning](sql-database-accelerated-database-recovery.md).|
|Ungefärligt antal distinkta|Mer information finns i [ungefärliga Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batchläge på Rowstore (under kompatibilitetsnivå 150)|Mer information finns i [batchläge på Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Dataidentifiering och -klassificering  |Mer information finns i [Azure SQL Database och SQL Data Warehouse dataidentifiering och klassificering](sql-database-data-discovery-and-classification.md).|
| Elastic Database-jobb | Mer information finns i [skapa, konfigurera och hantera elastiska jobb](elastic-jobs-overview.md). |
| Elastiska frågor | Mer information finns i [elastisk fråga översikt](sql-database-elastic-query-overview.md). |
| Elastiska transaktioner | [Distribuerade transaktioner över molndatabaser](sql-database-elastic-transactions-overview.md). |
| Med hanterade instanser-instansens sortering |Mer information finns i [använda PowerShell med Azure Resource Manager-mall för att skapa en hanterad instans i Azure SQL Database](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md).|
|Minne ge Feedback (rad läge) (under kompatibilitetsnivå 150)|Mer information finns i [minne ge Feedback (rad läge)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Frågeredigeraren i Azure portal |Mer information finns i [använder Azure portal SQL-frågeredigeraren för att ansluta och fråga efter data](sql-database-connect-query-portal.md).|
| R services / machine learning med enskilda databaser och elastiska pooler |Mer information finns i [Machine Learning-tjänster i Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Återskapa utelämnade databaser med hanterade instanser |Mer information finns i [återskapa bort databaser i Azure SQL Managed Instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replikering med hanterade instanser |Mer information finns i [Konfigurera replikering i en Azure SQL Database-hanterad databasinstans](replication-with-sql-database-managed-instance.md).|
| Serverlös beräkningsnivå | Mer information finns i [SQL-databas utan server (förhandsversion)](sql-database-serverless.md).|
|SQL Analytics|Mer information finns i [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Tabellen varierande fördröjas kompilering (under kompatibilitetsnivå 150)|Mer information finns i [tabell varierande fördröjas kompilering](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Hotidentifiering med hanterade instanser |Mer information finns i [konfigurera hotidentifiering i Azure SQL Database-hanterad instans](sql-database-managed-instance-threat-detection.md).|
| Tidszon stöd för hanterade instanser|Mer information finns i [tidszonen i Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md).|
| Transparent datakryptering (TDE) med ta med din egen nyckel (BYOK) med hanterade instanser |Mer information finns i [Azure SQL Transparent datakryptering med Kundhanterade nycklar i Azure Key Vault: Stöd för Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="may-2019"></a>Maj 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
|Hyperskala tjänstnivå som släpps för allmän tillgänglighet| Mer information finns i [hyperskala tjänstnivå för upp till 100 TB](sql-database-service-tier-hyperscale.md) och [hämta högpresterande skalning för arbetsbelastningen i din Azure-databas med hyperskala](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/).|
|Serverlös Beräkningsnivån är för en förhandsversion | Mer information finns i [SQL-databas utan server (förhandsversion)](sql-database-serverless.md).|
| Ökade log transaktionspriser och mål-IOPS för databaser som använder den vCore-baserade inköpsmodellen| Mer information finns i [Resource gränser för enskilda databaser med hjälp av den vCore-baserade inköpsmodellen](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) och [Resource gränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases).
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Hyperskala service nivån docs uppdaterat GA-version| Mer information finns i [hyperskala tjänstnivå för upp till 100 TB](sql-database-service-tier-hyperscale.md).|
|Beräkning utan Server nivån docs lanseras med offentlig förhandsversion| Mer information finns i [SQL-databas utan server (förhandsversion)](sql-database-serverless.md).|
| &nbsp; |

## <a name="april-2019"></a>April 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| Offentliga slutpunkter för den hanterade instansen i offentlig förhandsversion| Mer information finns i [med hjälp av Azure SQL Database-hanterad instans på ett säkert sätt med offentliga slutpunkten](sql-database-managed-instance-public-endpoint-securely.md).|
| Tidszon stöd för hanterad instans i offentlig förhandsversion| Mer information finns i [tidszonen i Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md).|
| Versionen för att säkra Azure SQL-databaser med hanterade identiteter i andra offentlig förhandsversion| Se [skydda Azure SQL-databaser med hanterade identiteter blev just ännu enklare](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Offentliga slutpunkter för den hanterade instansen i offentlig förhandsversion| Mer information finns i [med hjälp av Azure SQL Database-hanterad instans på ett säkert sätt med offentliga slutpunkten](sql-database-managed-instance-public-endpoint-securely.md).|
| Tidszon stöd för hanterad instans i offentlig förhandsversion| Mer information finns i [tidszonen i Azure SQL Database Managed Instance](sql-database-managed-instance-timezone.md). |
| Resurs-styrning i Azure SQL Database | Mer information finns i [resurs-styrning i Azure SQL Database](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). || &nbsp; |

## <a name="march-2019"></a>Mars 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| Läs stöd för Azure SQL Database som släpps för allmän tillgänglighet | Mer information finns i [lässkalning](sql-database-read-scale-out.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Har lagts till loggen gränser för enskilda databaser|Mer information finns i [enkel database vCore-resursgränser](sql-database-vcore-resource-limits-single-databases.md).|
| Har lagts till loggen gränser för elastiska pooler och databaser i en pool|Mer information finns i [vCore resource begränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).|
| Har lagts till Transaction log rate styrning| Lagt till nytt innehåll för [Transaction log rate styrning](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Uppdaterade PowerShell-exempel för enskilda databaser och elastiska pooler använda az.sql modul | Mer information finns i [PowerShell-exempel för enskilda databaser och elastiska pooler](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Februari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
|Skapa en återställningsbart online-index är nu allmänt tillgänglig| Mer information finns i [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Hanterad instans-stöd för routningstabeller förbättrad| Mer information finns i [krav på](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Databasen namnbyte stöds i hanterade instansen | Mer information finns i den [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) och [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) syntax.|
|SQL-databas som en källa för referensdata för Stream Analytics. | Mer information finns i [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) och [Azure Stream Analytics nu stöd för Azure SQL Database som referensindata](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/).|
|Data Migration Assistant lägger till stöd för den hanterade instansen. |Mer information finns i [vad är nytt i DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Migration Assistant lägger till stöd för target diagnostisk utvärdering av för den hanterade instansen. | Mer information finns i [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Datamigreringstjänst stöder migrering från Amazon RDS till managed instance | Mer information finns i [Självstudie: Migrera Fjärrskrivbordstjänster SQL Server till Azure SQL Database eller en Azure SQL Database hanterad instans online med DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|Lägger till den hanterade instansen distribution alternativet förtydliganden|Uppdatera många artiklar för att förtydliga se om det gäller enkel databas och elastisk pool distributionsalternativ för hanterad instans. |
|Uppdaterade tempdb-storlekar för DTU-baserade inköpsmodellen | Mer information finns i [Tempdb-databasen i SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Uppdaterade import och export med bacpac-fil för hanterad instans-support| Mer information finns i [Import från BACPAC](sql-database-import.md) och [exportera till BACPAC](sql-database-export.md). |
| &nbsp; |

## <a name="january-2019"></a>Januari 2019

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| Ytterligare kornighet alternativ för att beräkna resurser | Generell användning och affärskritisk tjänstnivåer för [enkla databaser](sql-database-vcore-resource-limits-single-databases.md) och [elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) har nu mer detaljerade beräkningsalternativ.|
| Visning av granskningsposter för den hanterade instansen i Azure portal | Visa [granskningsposter för hanterade instanser](sql-database-managed-instance-auditing.md) i Azure portal stöds nu.|
| ADVANCE threat funktion för dubblettidentifiering bytt namn till avancerad säkerhet för Data | Funktionen för identifiering av avancerade hot som bytt namn till [avancerade datasäkerhet](sql-advanced-threat-protection.md) för enskilda databaser, elastiska pooler och hanterade instanser. |
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
| Hanterade instanser och Transaktionsreplikering | Har lagts till artikeln om hur du använder [Transaktionsreplikering med hanterade instanser](replication-with-sql-database-managed-instance.md). |
| Har lagts till Azure AD med självstudiekursen för hanterad instans | Detta [Azure AD med hanterad instans](sql-database-managed-instance-aad-security-tutorial.md) självstudiekurs visar du behöva konfigurera och testa hanterade instans security med Azure AD-inloggningar. |
| Uppdaterat innehåll för jobbet automation med hjälp av Transact-SQL-skript | Uppdaterade och tydliggjorde innehåll för att använda [jobbet automation med hjälp av Transact-SQL-skript](sql-database-job-automation-overview.md) för enskilda databaser, elastiska pooler och hanterade instanser. |
| Säkerhetsinnehåll för hanterade instanser uppdateras | Uppdaterade och klargör innehållet för den [säkerhetsmodell för hanterade instanser](sql-database-security-overview.md), och skillnader i med säkerhetsmodell för enskilda databaser och elastiska pooler. |
| Uppdatera alla snabbstarter och självstudier | Alla snabbstarter och självstudier i den [dokumentation](https://docs.microsoft.com/azure/sql-database) har uppdaterats och så att den matchar ändringar i Azure-portalen. |
| Översikt för har lagts till snabbstartguider | Lagt till en översikt över Snabbstartsguide till [enkla databaser](sql-database-quickstart-guide.md) och för [hanterade instanser](sql-database-managed-instance-quickstart-guide.md). |
| Har lagts till SQL-databas i ordlista | Detta [villkoren ordlista](sql-database-glossary-terms.md) artikeln innehåller en fullständig förteckning över SQL Database-termer och länkar till den primära konceptuella sidan som förklarar termen i kontexten. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Bidra till innehållet förbättring

Azure SQL-dokumentationsuppsättningen är öppen källkod. Att arbeta offentligt ger flera fördelar:

- Öppen källkod lagringsplatser med att få feedback på vilka dokument som behövs mest.
- Granskning av lagringsplatser med öppen källkod med att publicera det mest hjälpfulla innehållet på din första utgåva.
- Uppdatering av lagringsplatser med öppen källkod med att göra det enklare att kontinuerligt förbättra innehållet.

Om du vill bidra till Azure SQL Database-dokumentationsinnehåll, se den [Microsoft Docs översikt över deltagarguiden](https://docs.microsoft.com/contribute/). Användarupplevelsen på [docs.microsoft.com](https://docs.microsoft.com/) integrerar [GitHub](https://github.com/) arbetsflödet direkt för att göra det ännu enklare. Börja med att [redigera det dokument du visar](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Hjälp till genom [granska nya ämnen](https://docs.microsoft.com/contribute/#review-open-prs), eller [skapa kvalitetsärenden](https://docs.microsoft.com/contribute/#create-quality-issues).
