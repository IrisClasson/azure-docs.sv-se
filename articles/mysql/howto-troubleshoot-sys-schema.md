---
title: Använd sys_schema-Azure Database for MySQL
description: Lär dig hur du använder sys_schema för att hitta prestanda problem och underhålla databasen i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: d2ed06041e8ee0e2993289cdde5fe92f7664b476
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829523"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Använda sys_schema för prestanda justering och databas underhåll i Azure Database for MySQL

MySQL-performance_schema, som först är tillgängligt i MySQL 5,5, innehåller instrumentering för många viktiga server resurser, till exempel minnesallokering, lagrade program, metadata lås osv. Performance_schema innehåller dock fler än 80 tabeller och den information som krävs kräver ofta att du kopplar tabellerna i performance_schema, samt tabeller från information_schema. När du bygger på både performance_schema och information_schema ger sys_schema en kraftfull samling [användarvänliga vyer](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) i en skrivskyddad databas och är helt aktive rad i Azure Database for MySQL version 5,7.

![vyer av sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Det finns 52 vyer i sys_schema och varje vy har en av följande prefix:

- Host_summary eller i/o: I/O-relaterad fördröjning.
- InnoDB: InnoDB och lås.
- Minne: minnes användning av värden och användare.
- Schema: schema relaterad information, till exempel automatisk ökning, index osv.
- Statement: information om SQL-uttryck; Det kan vara en instruktion som ledde till fullständig tabell genomsökning eller lång tid för frågor.
- Användare: resurser förbrukade och grupperade efter användare. Exempel är fil-I/o, anslutningar och minne.
- Vänta: vänta händelser grupperade efter värd eller användare.

Nu ska vi titta på några vanliga användnings mönster för sys_schema. För att börja med ska vi gruppera användnings mönstren i två kategorier: **prestanda justering** och **databas underhåll**.

## <a name="performance-tuning"></a>Prestandajustering

### <a name="sysuser_summary_by_file_io"></a>*sys. user_summary_by_file_io*

I/o är den dyraste åtgärden i-databasen. Vi kan ta reda på den genomsnittliga IO-svars tiden genom att fråga *sys. user_summary_by_file_io* -vyn. Med standard 125 GB allokerad lagring, är min IO-latens cirka 15 sekunder.

![i/o-latens: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Eftersom Azure Database for MySQL skalar IO med avseende på lagring, minskar min IO-fördröjning till 571 MS efter att ha ökat mitt allokerat lagrings utrymme till 1 TB.

![i/o-latens: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys. schema_tables_with_full_table_scans*

Trots noggrann planering kan många frågor fortfarande resultera i fullständiga tabells ökningar. Mer information om typer av index och hur du optimerar dem finns i den här artikeln: [fel sökning av frågans prestanda](./howto-troubleshoot-query-performance.md). Fullständiga tabells ökningar är resurs krävande och försämrar databasens prestanda. Det snabbaste sättet att hitta tabeller med fullständig tabells ökning är att fråga *sys. schema_tables_with_full_table_scans* -vyn.

![fullständiga tabells ökningar](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys. user_summary_by_statement_type*

För att felsöka problem med databas prestanda kan det vara bra att identifiera de händelser som händer i databasen, och med hjälp av *sys. user_summary_by_statement_type* -vyn kan det bara göra sticket.

![Sammanfattning per instruktion](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

I det här exemplet Azure Database for MySQL ägnade 53 minuter att tömma slog-händelseloggen 44579 gånger. Det är en lång tid och många IOs. Du kan minska den här aktiviteten genom att antingen inaktivera din långsamma fråga-logg eller minska frekvensen för långsam inloggning Azure Portal.

## <a name="database-maintenance"></a>Databas underhåll

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys. innodb_buffer_stats_by_table*

[!IMPORTANT]
> Att fråga den här vyn kan påverka prestandan. Vi rekommenderar att du utför det här fel söknings arbetet under tider med låg belastning.

InnoDB buffer finns i minnet och är den viktigaste cache-mekanismen mellan DBMS och lagring. Storleken på InnoDB-bufferten är kopplad till prestanda nivån och kan inte ändras om inte en annan produkt-SKU väljs. Precis som med minne i operativ systemet, växlas gamla sidor ut för att göra plats för mer information. Om du vill ta reda på vilka tabeller som använder merparten av InnoDB buffer buffer, kan du fråga *sys. innodb_buffer_stats_by_table* -vyn.

![Status för InnoDB-buffert](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

I bilden ovan är det uppenbart att andra än system tabeller och vyer, varje tabell i mysqldatabase033-databasen, som är värd för en av mina WordPress-webbplatser, upptar 16 KB eller 1 sida av data i minnet.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys. schema_unused_indexes* & *sys. schema_redundant_indexes*

Index är fantastiska verktyg för att förbättra Läs prestanda, men de medför ytterligare kostnader för infogningar och lagring. *Sys. schema_unused_indexes* och *sys. schema_redundant_indexes* ger insikter om oanvända eller dubbla index.

![oanvända index](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundanta index](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Slutsats

I sammanfattning är sys_schema ett utmärkt verktyg för både prestanda justering och databas underhåll. Se till att dra nytta av den här funktionen i din Azure Database for MySQL. 

## <a name="next-steps"></a>Nästa steg
- Om du vill hitta peer-svar på dina mest aktuella frågor eller publicera en ny fråga/svar kan du besöka [Microsoft Q&en fråge sida](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
