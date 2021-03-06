---
title: Övervakning och prestandajustering
description: En översikt över funktionerna för övervakning och prestanda justering och metoder i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 6e17e2a6e5c9151080facc3a2dd8c1a18c0580fe
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982177"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Övervakning och prestanda justering i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Om du vill övervaka prestanda för en databas i Azure SQL Database och Azure SQL-hanterad instans, börjar du med att övervaka de processor-och IO-resurser som används av arbets belastningen i förhållande till den nivå av databas prestanda som du valde när du väljer en viss tjänst nivå och prestanda nivå. För att åstadkomma detta genererar Azure SQL Database och Azure SQL-hanterad instans resurs mått som kan visas i Azure Portal eller genom att använda något av dessa SQL Server hanterings verktyg: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database innehåller ett antal databas rådgivare för att tillhandahålla smarta rekommendationer för prestanda justering och automatiska justerings alternativ för att förbättra prestandan. Dessutom visar Query Performance Insight information om de frågor som ansvarar för den mest CPU-och i/o-användningen för enskilda databaser och databaser i pooler.

Azure SQL Database och Azure SQL-hanterad instans tillhandahåller avancerade övervaknings-och justerings funktioner som stöds av artificiell intelligens för att hjälpa dig att felsöka och maximera prestandan för dina databaser och lösningar. Du kan välja att konfigurera [strömnings exporten](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) av dessa [intelligent Insights](intelligent-insights-overview.md) och andra databas resurs loggar och mät värden till en av flera destinationer för förbrukning och analys, särskilt med [SQL Analytics](../../azure-monitor/insights/azure-sql.md)). Azure SQL-analys är en avancerad lösning för moln övervakning för att övervaka prestanda för alla dina databaser i stor skala och över flera prenumerationer i en enda vy. För en lista över loggar och mått som du kan exportera, se [diagnostisk telemetri för export](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

Slutligen har SQL Server egna funktioner för övervakning och diagnostik som SQL Database och SQL-hanterad instans använder, till exempel [query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) och [vyer för dynamisk hantering (DMV: er)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Se [övervakning med DMV: er](monitoring-with-dmvs.md) för skript för att övervaka ett antal prestanda problem.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Övervaknings-och justerings funktioner i Azure Portal

Azure SQL Database och Azure SQL-hanterad instans tillhandahåller övervakning av resurs mått i Azure Portal. Dessutom tillhandahåller Azure SQL Database databas rådgivare och Query Performance Insight rekommendationer för frågor och prestanda analys. I Azure Portal kan du slutligen aktivera automatisk för [logiska SQL-servrar](logical-servers.md) och deras enskilda databaser och databaser i pooler.

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Resurs övervakning av Azure SQL Database och Azure SQL-hanterad instans

Du kan snabbt övervaka olika resurs mått i Azure Portal i vyn **mått** . Med dessa mått kan du se om en databas når 100% av processor-, minnes-eller IO-resurser. Hög DTU-eller processor procent, samt hög IO-procent, anger att arbets belastningen kan kräva fler CPU-eller i/o-resurser. Det kan också indikera frågor som måste optimeras.

  ![Resurs mått](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Databas rådgivare i Azure SQL Database

Azure SQL Database innehåller [databas rådgivare](database-advisor-implement-performance-recommendations.md) som ger rekommendationer för prestanda justering för enskilda databaser och databaser i pooler. Dessa rekommendationer är tillgängliga i Azure Portal och med [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Du kan också aktivera [Automatisk justering](automatic-tuning-overview.md) så att Azure SQL Database automatiskt kan implementera dessa justerings rekommendationer.

### <a name="query-performance-insight-in-azure-sql-database"></a>Query Performance Insight i Azure SQL Database

[Query Performance Insight](query-performance-insight-use.md) visar prestanda i Azure Portal för de vanligaste och mest använda frågorna för enskilda databaser och databaser i pooler.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generera intelligenta utvärderingar av prestanda problem

[Intelligent Insights](intelligent-insights-overview.md) för Azure SQL Database och Azure SQL-hanterad instans använder inbyggd intelligens för att kontinuerligt övervaka databas användningen via artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda. Intelligent Insights identifierar automatiskt prestanda problem med databaser baserat på vänte tider, fel eller tids gränser för frågekörning. När den har identifierats utförs en detaljerad analys som genererar en resurs logg (kallas SQLInsights) med en [intelligent utvärdering av problemen](intelligent-insights-troubleshoot-performance.md). Den här utvärderingen består av en rotor Saks analys av databasens prestanda problem och, om möjligt, rekommendationer för prestanda förbättringar.

Intelligent Insights är en unik funktion i Azure inbyggda intelligens som ger följande värde:

- Proaktiv övervakning
- Skräddarsydda prestanda insikter
- Tidig identifiering av databas prestanda försämring
- Rotor Saks analys av problem som upptäckts
- Rekommendationer för prestanda förbättring
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Aktivera strömnings export av mått och resurs loggar

Du kan aktivera och konfigurera den [strömmande exporten av Diagnostic-telemetri](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) till en av flera destinationer, inklusive intelligent Insights resurs loggen. Använd [SQL Analytics](../../azure-monitor/insights/azure-sql.md) och andra funktioner för att använda denna ytterligare diagnostiska telemetri för att identifiera och lösa prestanda problem.

Du konfigurerar diagnostikinställningar till att strömma kategorier av mått och resurs loggar för enskilda databaser, databaser i pooler, elastiska pooler, hanterade instanser och instans databaser till någon av följande Azure-resurser.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics arbets yta i Azure Monitor

Du kan strömma mått och resurs loggar till en [Log Analytics arbets yta i Azure Monitor](../../azure-monitor/platform/resource-logs-collect-workspace.md). Data som strömmas här kan användas av [SQL Analytics](../../azure-monitor/insights/azure-sql.md), som är en övervaknings lösning för endast molnet som ger intelligent övervakning av dina databaser som innehåller prestanda rapporter, aviseringar och rekommendationer. Data som strömmas till en Log Analytics arbets yta kan analyseras med andra övervaknings data som samlas in och du kan också använda andra Azure Monitor funktioner, till exempel aviseringar och visualiseringar.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Du kan strömma mått och resurs loggar till [Azure Event Hubs](../../azure-monitor/platform/resource-logs-stream-event-hubs.md). Att strömma Diagnostic-telemetri till Event Hubs för att tillhandahålla följande funktioner:

- **Strömma loggar till loggnings-och telemetri system från tredje part**

  Strömma alla dina mått och resurs loggar till en enda händelse hubb för att skicka loggdata till en SIEM-eller Log Analytics-verktyg från tredje part.
- **Bygg en anpassad telemetri-och loggnings plattform**

  Med händelse hubbarna med hög skalbarhet för publicerings prenumerationer kan du samla in mått och resurs loggar på ett flexibelt sätt i en anpassad telemetri-plattform. Mer information finns i [utforma och ändra storlek på en plattform för global skalnings telemetri på Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Visa tjänstens hälsa genom att strömma data till Power BI**

  Använd Event Hubs, Stream Analytics och Power BI för att omvandla dina diagnostikdata till nära real tids insikter om dina Azure-tjänster. Mer information om den här lösningen finns i [Stream Analytics och Power BI: en analys instrument panel i real tid för att strömma data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Azure Storage

Strömma mått och resurs loggar till [Azure Storage](../../azure-monitor/platform/resource-logs-collect-storage.md). Använd Azure Storage för att arkivera stora mängder diagnostiska telemetri för en bråkdel av kostnaden för de föregående två strömnings alternativen.

## <a name="use-extended-events"></a>Använda utökade händelser 

Dessutom kan du använda [utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) i SQL Server för avancerad övervakning och fel sökning. Arkitekturen för utökade händelser gör det möjligt för användare att samla in så mycket eller så lite data som behövs för att felsöka eller identifiera ett prestanda problem. Information om hur du använder utökade händelser i Azure SQL Database finns i [utökade händelser i Azure SQL Database](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om intelligenta prestanda rekommendationer för enskilda databaser och grupperade databaser finns i [prestanda rekommendationer för Database Advisor](database-advisor-implement-performance-recommendations.md).
- Mer information om automatisk övervakning av databas prestanda med automatiserad diagnostik och rotor Saks analys av prestanda problem finns i [Azure SQL intelligent Insights](intelligent-insights-overview.md).
