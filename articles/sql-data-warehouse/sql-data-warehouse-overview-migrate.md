---
title: Migrera lösningen till SQL Data Warehouse | Microsoft Docs
description: Riktlinjer för att leverera din lösning till Azure SQL Data Warehouse-plattformen.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776228"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrera lösningen till Azure SQL Data Warehouse
Se vad ingår i att migrera en befintlig databaslösning till Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profilera din arbetsbelastning
Innan du migrerar, som du vill att vissa SQL Data Warehouse är den rätta lösningen för din arbetsbelastning. SQL Data Warehouse är ett distribuerat system som utformats för att utföra analyser på stora mängder data.  Migrera till SQL Data Warehouse kräver vissa ändringar av design som inte är för hårt för att förstå men kan ta lite tid att implementera. Om ditt företag kräver ett informationslager i företagsklass, är fördelarna värt att arbetet. Om du inte behöver kraften i SQL Data Warehouse, är det dock mer kostnadseffektivt att använda SQL Server eller Azure SQL Database.

Överväg att använda SQL Data Warehouse när du:
- Har en eller flera terabyte data
- Planera att köra analyser på stora mängder data
- Måste ha möjlighet att skala beräkning och lagring 
- Om du vill spara pengar genom att pausa beräkningsresurser när de inte behövs.

Använd inte SQL Data Warehouse för operativa (OLTP)-arbetsbelastningar som ibland:
- Hög frekvens läsningar och skrivningar
- Stora mängder singleton väljer
- Stora volymer av enskild rad infogningar
- Rad för rad bearbetning behov
- Inkompatibla format (JSON, XML)

## <a name="plan-the-migration"></a>Planera migreringen

När du har valt att migrera en befintlig lösning till SQL Data Warehouse, är det viktigt att planera migreringen innan du börjar. 

Ett mål i planeringen är att säkerställa att dina data och dina tabellscheman koden är kompatibla med SQL Data Warehouse. Det finns vissa kompatibilitet skillnader som du kan undvika mellan dina aktuella system och SQL Data Warehouse. Dessutom kan du migrera stora mängder data till Azure tar tid. Noggrann planering påskyndar hämtar dina data till Azure. 

Ett annat mål med planering är att justera design att se till att din lösning drar nytta av den höga frågeprestanda SQL Data Warehouse är utformad att ge. Designa informationslager för att skala introducerar olika design mönster och så traditionella metoder är inte alltid bäst. Även om du kan göra vissa design justeringar efter migreringen, sparar att göra ändringar i processen snabbare tid senare.

Om du vill utföra en lyckad migrering, måste du migrera dina tabellscheman, din kod och dina data. Information om dessa migreringsavsnitt finns:

-  [Migrera dina scheman](sql-data-warehouse-migrate-schema.md)
-  [Migrera din kod](sql-data-warehouse-migrate-code.md)
-  [Migrera dina data](sql-data-warehouse-migrate-data.md). 

## <a name="next-steps"></a>Nästa steg
CAT (Customer Advisory Team) har också bra SQL Data Warehouse vägledning, där de publicerar via bloggar.  Ta en titt på deras artikeln [migrera data till Azure SQL Data Warehouse i praktiken] [ Migrating data to Azure SQL Data Warehouse in practice] för mer information om migrering.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

<!--Update_Description: update meta properties, wording update-->