---
title: Det tar lång tid att importera och exportera en databas
description: Azure SQL Database-och Azure SQL Managed instance import/export-tjänsten tar lång tid att importera eller exportera en databas
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: f98cfcd49806061a969a9227f9ade05f70ce79ff
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982328"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Det tar lång tid att importera eller exportera en databas med Azure SQL Database och hanterad instans import/export-tjänst

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

När du använder import/export-tjänsten kan processen ta längre tid än förväntat. I den här artikeln beskrivs möjliga orsaker till den här fördröjningen och alternativa lösnings metoder.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database import/export-tjänsten

Tjänsten Azure SQL Database import/export är en REST-baserad webb tjänst som körs i varje Azure-datacenter. Den här tjänsten anropas när du använder antingen [import databasen](database-import.md#using-azure-portal) eller [export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) alternativet för att flytta databasen i Azure Portal. Tjänsten tillhandahåller kostnads fria begär ande köer och beräknings tjänster för att utföra import och export mellan Azure SQL Database och Azure Blob Storage.

Import-och export åtgärderna representerar inte en traditionell fysisk databas säkerhets kopia utan i stället en logisk säkerhets kopia av databasen som använder ett särskilt BACPAC-format. I BACPAC-formatet kan du undvika att behöva använda ett fysiskt format som kan variera mellan versioner av Microsoft SQL Server, Azure SQL Database och Azure SQL-hanterad instans.

## <a name="what-causes-delays-in-the-process"></a>Vad orsakar fördröjningar i processen?

Tjänsten Azure SQL Database import/export innehåller ett begränsat antal virtuella datorer per region för att bearbeta import-och export åtgärder. De virtuella datorerna för beräkning av virtuella datorer är värdar för att se till att import eller export förhindrar fördröjningar och kostnader för bandbredd i flera regioner. Om för många begär Anden görs samtidigt i samma region kan det uppstå betydande fördröjningar vid bearbetning av åtgärderna. Tiden som krävs för att slutföra förfrågningar kan variera från några sekunder till flera timmar.

> [!NOTE]
> Om en begäran inte bearbetas inom fyra dagar avbryts begäran automatiskt av tjänsten.

## <a name="recommended-solutions"></a>Rekommenderade lösningar

Om databas exporten bara används för återställning från oavsiktlig data borttagning, tillhandahåller alla Azure SQL Database-versioner självbetjänings återställning från systemgenererade säkerhets kopior. Men om du behöver dessa exporter av andra orsaker, och om du behöver snabbare eller mer förutsägbara import/export-prestanda, bör du överväga följande alternativ:

* [Exportera till en BACPAC-fil med hjälp av SQLPackage-verktyget](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Kör BACPAC-import eller exportera direkt i din kod med hjälp av API: et Microsoft SQL Server Data-Tier Application Framework (DacFx). Mer information finns i:
  * [Exportera ett data skikts program](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft. SqlServer. DAC-namnrymd](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Ladda ned DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>Saker att tänka på när du exporterar eller importerar en databas

* Alla metoder som beskrivs i den här artikeln använder sig av kvoten för databas transaktions enheten (DTU), vilket orsakar begränsning av Azure SQL Databases tjänsten. Du kan [Visa DTU-statistik för databasen på Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Om databasen har nått resurs gränserna [uppgraderar du tjänst nivån](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) för att lägga till fler resurser.
* Vi rekommenderar att du kör klient program (t. ex. verktyget sqlpackage eller ditt anpassade DAC-program) från en virtuell dator i samma region som din databas. Annars kan det uppstå prestanda problem som rör nätverks fördröjningen.
* Att exportera stora tabeller utan grupperade index kan vara mycket långsamt eller till och med orsaka fel. Det här problemet beror på att tabellen inte kan delas upp och exporteras parallellt. I stället måste det exporteras i en enda transaktion och det orsakar långsamma prestanda och potentiella fel under exporten, särskilt för stora tabeller.


## <a name="related-documents"></a>Relaterade dokument

[Att tänka på när du exporterar en databas](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
