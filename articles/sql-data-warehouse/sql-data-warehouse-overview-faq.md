---
title: Vanliga frågor och svar om Azure SQL Data Warehouse | Microsoft Docs
description: Den här artikeln innehåller reda på vanliga frågor och svar om Azure SQL Data Warehouse från kunder och utvecklare
services: sql-data-warehouse
author: happynicolle
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: nicw
ms.reviewer: igorstan
ms.openlocfilehash: c16d95ea15fc358cb81b17b42570cb35f2e8c52d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795553"
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse vanliga frågor och svar

## <a name="general"></a>Allmänt

F. Vad erbjuder SQL DW för datasäkerhet?

A. SQL DW erbjuder flera lösningar för att skydda data, till exempel transparent Datakryptering och granskning. Mer information finns i [Säkerhet].

F. Var hittar jag reda på vilka juridiska eller business-standarder SQL DW som är kompatibla med?

A. Gå till den [Microsoft-efterlevnad] för olika efterlevnadserbjudanden av produkten som SOC och ISO. Först väljer efter efterlevnad rubrik och sedan expandera Azure under Microsoft omfattade cloud services till höger på sidan för att se vilka tjänster som är Azure-tjänster är kompatibla.

F. Kan jag ansluta Power BI?

A. Ja! Om Power BI stöder direkt fråga med SQL DW, är den inte avsedd för många användare eller data i realtid. För användning i produktion av Power BI bör du använda Power BI ovanpå Azure Analysis Services eller Analysis Service IaaS. 

F. Vilka är Kapacitetsbegränsningarna för SQL Data Warehouse?

A. Se våra aktuella [kapacitetsbegränsningar] sidan. 

F. Varför är min skala/pausa/återuppta tar så lång tid?

A. En mängd olika faktorer kan påverka tiden för hanteringsåtgärder för beräkning. Ett vanligt användningsfall för tidskrävande åtgärder är transaktionell återställning. När en skala eller pausa åtgärd initieras, blockeras alla inkommande sessioner och frågor är tömda. För att du lämnar systemet i ett stabilt tillstånd, måste transaktioner distribueras tillbaka innan en åtgärd kan påbörjas. Det största antalet och större loggstorleken av transaktioner, desto längre tid åtgärden kommer ha stoppats återställa systemet till ett stabilt tillstånd.

## <a name="user-support"></a>Stöd för användare

F. Jag har en funktionsbegäran där skickar jag det?

A. Om du har en funktionsbegäran skickar du den på vår [UserVoice] sidan

F. Hur gör jag x?

A. Behöver hjälp med att utveckla med SQL Data Warehouse, kan du ställa frågor på våra [Stack Overflow] sidan. 

F. Hur gör jag för att skicka ett supportärende?

A. [Supportärenden] kan registreras via Azure-portalen.

## <a name="sql-languagefeature-support"></a>SQL language/funktioner som stöds 

F. Vilka datatyper stöder SQL Data Warehouse?

A. Finns i SQL Data Warehouse [datatyper].

F. Vilka table-funktioner stöder ni?

A. Medan SQL Data Warehouse stöder många funktioner, vissa stöds inte och dokumenteras i [funktioner som inte stöds tabell].

## <a name="tooling-and-administration"></a>Verktyg och administration

F. Du har stöd för databas-projekt i Visual Studio.

A. Vi stöder för närvarande inte databasprojekt i Visual Studio för SQL Data Warehouse. Om du vill konvertera en röst att hämta den här funktionen kan du gå till vår User Voice [Funktionsförfrågan för databas-projekt].

F. SQL Data Warehouse har stöd för REST API: er?

A. Ja. De flesta REST-funktioner som kan användas med SQL Database är också tillgängliga med SQL Data Warehouse. Du kan hitta API-information i REST-dokumentationssidorna eller [MSDN].


## <a name="loading"></a>Läser in

F. Vilka klientdrivrutiner som stöder ni?

A. Stödet för DW kan hittas på den [anslutningssträngar] sidan

F: Vilka filformat som stöds av PolyBase med SQL Data Warehouse?

S: Orc, RC, Parquet och fast avgränsad text

F: Vad kan jag ansluta till från SQL DW med PolyBase? 

S: [Azure Data Lake Store] och [Azure Storage Blobs]

F: Går beräkning pushdown vid anslutning till Azure Storage-Blobbar eller ADLS? 

S: SQL DW PolyBase interagerar Nej, endast lagringskomponenter. 

F: Kan jag ansluta till HDI?

S: HDI kan använda antingen ADLS eller WASB som det HDFS-lagret. Om du har antingen som HDFS-lager, kan du läsa in dessa data i SQL DW. Men kan inte du generera pushdown beräkning till HDI-instans. 

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Warehouse som helhet finns i vår [översikt] sidan.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Anslutningssträngar]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Supportärenden]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Säkerhet]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft-efterlevnad]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[kapacitetsbegränsningar]: ./sql-data-warehouse-service-capacity-limits.md
[datatyper]: ./sql-data-warehouse-tables-data-types.md
[Funktioner som inte stöds tabell]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Funktionsförfrågan för databas-projekt]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Översikt]: ./sql-data-warehouse-overview-faq.md
