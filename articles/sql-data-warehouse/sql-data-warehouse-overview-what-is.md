---
title: Vad är Azure SQL Data Warehouse? | Microsoft Docs
description: Företagsklass, distribuerad databas som kan bearbeta petabytevolymer med relationella och icke-relationella data. Det är branschens första informationslager i molnet med möjlighet att växa, krympa och pausa inom några sekunder.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: a9126e9023091dd8c3df71f2aa2558a01227a8be
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428029"
---
# <a name="what-is-azure-sql-data-warehouse"></a>Vad är Azure SQL Data Warehouse?

SQL Data Warehouse är en molnbaserad Enterprise Data mpp-modell som använder massiv parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte med data. Använd SQL Data Warehouse som en viktig komponent i en lösning för stordata. Importera stordata till SQL Data Warehouse med enkel [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL-frågor, och sedan använda kraften i MPP för att köra högpresterande analyser. Använd informationslagret en tid för att integrera och analysera och upptäck vilket fantastiskt hjälpmedel det är för att fördjupa insikterna.  

## <a name="key-component-of-big-data-solution"></a>Viktig komponent i lösning för stordata

SQL Data Warehouse är en viktig komponent i en lösning mellan slutpunkter för stordata i molnet.

![Lösning för informationslager](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

I en molndatalösning inhämtas data till lagringsplatser för stordata från olika källor. Hadoop, Spark och maskininlärningsalgoritmer förbereder och tränar data när de finns på lagringsplatser för stordata. När dessa data är färdiga för komplexa analyser använder SQL Data Warehouse PolyBase för att fråga lagringsplatserna för stordata. PolyBase använder de T-SQL-frågor som är standard för att flytta data till SQL Data Warehouse.
 
SQL Data Warehouse lagrar data i relationstabeller med kolumnbaserad lagring. Det här formatet minskar lagringskostnaderna för data avsevärt och förbättrar frågeprestanda. Du kan köra analys på massiv skala när data lagras i SQL Data Warehouse. Jämfört med traditionella databassystem slutförs analysfrågor på några sekunder i stället för flera minuter eller på några timmar i stället för flera dagar. 

Analysresultaten kan skickas till globala rapporteringsdatabaser eller program. Företagsanalytiker får sedan den information de behöver för att kunna fatta välgrundade affärsbeslut.

## <a name="next-steps"></a>Nästa steg

- Utforska [Azure SQL Data Warehouse-arkitektur](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]
- [Läsa in exempeldata][load sample data].
- Utforska [videor](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  
* Sök [bloggar]
* Skicka en [Funktionsbegäranden]
* Sök [Customer Advisory Team-bloggar]
* [Skapa ett supportärende]
* Sök [MSDN-forum]
* Sök [Stack Overflow-forum]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
