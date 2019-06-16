---
title: Förbättra prestanda för Azure-program med Azure Advisor | Microsoft Docs
description: Använd Advisor för att optimera prestandan för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 8fdae1e12e56dcbcb56941726b0c089ad59b8fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254654"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Förbättra prestanda för Azure-program med Azure Advisor

Azure Advisor-rekommendationer att förbättra hastighet och tillgänglighet för dina verksamhetskritiska program. Du kan hämta prestandarekommendationer från Advisor den **prestanda** fliken Advisor-instrumentpanelen.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Minska tiden för DNS-TTL-värde på Traffic Manager-profilen att växla över till felfria slutpunkter snabbare

[Tid till Live (TTL) inställningar](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) på din Traffic Manager-profil kan du ange hur snabbt växla slutpunkter om en viss slutpunkt slutar svara på frågor. Minska TTL-värden innebär att klienter kommer att dirigeras till fungerande slutpunkter snabbare.

Azure Advisor identifierar Traffic Manager-profiler med ett längre TTL-värde som har konfigurerats och rekommenderar konfigurera TTL-värdet till 20 sekunder eller 60 sekunder beroende på om profilen som har konfigurerats för [snabb redundans](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Förbättra databasens prestanda med SQL DB Advisor

Advisor ger dig en konsekvent konsoliderad vy över rekommendationer för alla dina Azure-resurser. Det integreras med SQL Database Advisor för att ge dig rekommendationer för att förbättra prestandan för din SQL Azure-databas. SQL Database Advisor utvärderar prestanda för dina SQL Azure-databaser genom att analysera din användningshistorik. Den erbjuder sedan rekommendationer som passar bäst för att köra databasens normal belastning.

> [!NOTE]
> För att få rekommendationer, en databas måste ha om en veckas användning och inom den veckan måste det finnas viss konsekvent aktivitet. SQL Database Advisor kan optimera enklare för konsekvent frågemönster än för slumpmässiga ökningar av aktivitet.

Läs mer om SQL Database Advisor [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Förbättra App Service-prestanda och tillförlitlighet

Azure Advisor integrerar rekommendationer om bästa praxis för att förbättra din upplevelse för App Services och identifiera relevanta plattformsfunktioner. Exempel på rekommendationer för App Services är:
* Identifiering av instanser där minne eller CPU-resurser är slut genom appen körningar med alternativ för lösning.
* Identifiering av instanser där collocating resurser som webbappar och databaser kan förbättra prestanda och lägre kostnader.

Läs mer om rekommendationer för App Services, [Metodtips för Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Använd Managed Disks för att förhindra diskbegränsning i/o

Advisor identifierar virtuella datorer som tillhör ett lagringskonto som når skalbarhetsmålet. Det här tillståndet gör att de virtuella datorerna mottagliga för i/o-begränsning. Advisor rekommenderar att de använder Managed Disks för att förhindra att prestanda försämras.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Förbättra säkerheten och tillförlitligheten för virtuella diskar med Premium Storage

Advisor identifierar virtuella datorer med standarddiskar som har en stor mängd transaktioner på ditt lagringskonto och rekommenderar att du uppgraderar till premium-diskar. 

Azure Premium Storage tillhandahåller högpresterande och låg latens disksupport för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Virtuella diskar som använder premium storage-konton kan du lagra data på SSD (solid-state drive). För bästa prestanda för ditt program rekommenderar vi att du har migrerat alla virtuella diskar som kräver hög IOPS till premium storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Ta bort datasnedställning på SQL data warehouse-tabell att öka prestanda för frågor

Datasnedställning kan orsaka onödiga data movement eller resurs flaskhalsar när du kör din arbetsbelastning. Advisor identifierar distributionsdata förskjuta större än 15% och rekommenderar att du distribuera om dina data och gå tillbaka till dina viktiga val för tabell-distribution. Läs mer om identifiera och ta bort skeva i [felsökning skeva](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Skapa eller uppdatera inaktuella tabellstatistik på SQL data warehouse-tabell att öka prestanda för frågor

Advisor identifierar tabeller som inte har uppdaterat [tabellstatistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) och rekommenderar att skapa eller uppdatera tabellstatistik. SQL data warehouse-fråga optimering använder uppdaterade statiska värden för att beräkna kardinalitet eller antalet rader i frågeresultatet som möjliggör frågeoptimerare att skapa en frågeplan med hög kvalitet för bästa prestanda.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skala upp för att optimera användningen av cache för din SQL Data Warehouse-tabeller för att öka prestanda för frågor

Azure Advisor identifierar om SQL Data Warehouse har hög cache används procent och låg når procent. Det här tillståndet anger borttagning har hög cache, vilket kan påverka prestandan för SQL Data Warehouse. Advisor föreslår att du skalar upp din SQL Data Warehouse så du kan allokera tillräckligt med kapacitet för cache för din arbetsbelastning.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konvertera SQL Data Warehouse-tabeller i replikerade tabeller för att öka prestanda för frågor

Advisor identifierar tabeller som inte är replikerade tabeller men skulle ha nytta av konvertering och föreslår att du konverterar dessa tabeller. Rekommendationerna baseras på replikerad tabellstorlek, antal kolumner, tabelltyp för distribution och antalet partitioner i SQL Data Warehouse-tabell. Ytterligare heuristik kan anges i rekommendationen för kontext. Mer information om hur den här rekommendationen bestäms finns [rekommendationer för SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrera ditt Storage-konto till Azure Resource Manager för att få alla de senaste Azure-funktionerna

Migrera din modell för distribution av Storage-konto till Azure Resource Manager (Resource Manager) att dra nytta av malldistributioner, ytterligare säkerhetsalternativ och möjlighet att uppgradera till ett GPv2-konto för användning av Azure Storage senaste funktionerna. Advisor identifierar eventuella fristående lagringskonton som använder den klassiska distributionsmodellen och rekommenderar att du migrerar till Resource Manager-distributionsmodellen.

> [!NOTE]
> Klassiska aviseringar i Azure Monitor är schemalagda att dra tillbaka i juni 2019. Vi rekommenderar att du uppgraderar ditt klassiska storage-konto om du vill använda Resource Manager till behåller aviseringsdata funktioner med den nya plattformen. Mer information finns i [klassiska aviseringar tillbakadragande](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Utforma dina lagringskonton för att förhindra nått prenumerationsgränsen för högsta

En Azure-region har stöd för upp till 250 lagringskonton per prenumeration. När gränsen har nåtts, kan du inte skapa några fler storage-konton i den region eller prenumeration på kombinationen. Advisor kontrollerar dina prenumerationer och surface rekommendationer att designa för färre storage-konton för alla som ligger nära når maxgränsen.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimera prestandan för dina Azure MySQL och Azure PostgreSQL Azure MariaDB-servrar 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Åtgärda CPU-belastning servrarnas Azure MySQL och Azure PostgreSQL Azure MariaDB med processorflaskhalsar
Mycket hög användning av Processorn under en längre period kan orsaka långsam frågeprestanda för din arbetsbelastning. Öka storleken på CPU hjälpa på att optimera körningen för databasfrågorna och förbättra prestandan. Azure Advisor identifierar servrar med en hög processoranvändning som körs troligen begränsad processorarbetsbelastningar och rekommenderar att skala din databearbetning.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Minska minnesbegränsningar på din Azure MySQL och Azure PostgreSQL Azure MariaDB-servrar eller flytta till ett minne optimerade SKU
Ett låga cache träffar förhållande kan medföra långsammare prestanda för frågor och ökad IOPS. Detta kan bero på en felaktig frågeplan eller kör en minne-intensiv arbetsbelastning. Åtgärda frågeplanen eller [ökar minnet](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) i Azure Database för PostgreSQL-databasserver, Azure MySQL database-server eller Azure MariaDB server kommer att optimera körningen av databas-arbetsbelastning. Azure Advisor identifierar servrar påverkas på grund av hög bufferten pool dataomsättningen och rekommenderar åtgärda frågeplanen, flyttar till en högre SKU med mer minne eller öka lagringsstorleken för att få fler IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Använd en Azure MySQL- eller Azure PostgreSQL Läs replik för att skala ut läsningar för Läs-intensiva arbetsbelastningar
Azure Advisor utnyttjar arbetsbelastning-baserade heuristik, till exempel antalet läsåtgärder och skrivåtgärder på servern under de senaste sju dagarna att identifiera läsintensiva arbetsbelastningar. Din Azure database för PostgreSQL-resurs eller en Azure database för MySQL-resursen med mycket hög läsning/skrivning förhållandet kan resultera i processor och/eller minne contentions leda till långsamma prestanda för frågor. Att lägga till en [repliken](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) gör det lättare att skala ut läsningar till replikservern, förhindrar begränsningar för CPU och/eller minne på den primära servern. Advisor ska identifiera servrar med sådan hög läsintensiva arbetsbelastningar och rekommenderar att du lägger en [Läs replik](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) att omfördela några av de skrivskyddade arbetsbelastningarna.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skala din Azure MySQL, Azure PostgreSQL eller Azure MariaDB-server till en högre SKU för att förhindra att anslutningsbegränsningar
Varje ny anslutning till din databasserver upptar minne. Databas-serverns prestanda försämras om anslutningar till servern misslyckas grund av ett [övre gräns för](https://docs.microsoft.com/azure/postgresql/concepts-limits) i minnet. Azure Advisor ska identifiera servrar som kör med många anslutningsfel och rekommenderar att du uppgraderar din server anslutningar gränser för att ge mer minne till servern genom att skala upp beräkning eller använda minne Beräkningsoptimerade SKU: er, som har mer databearbetning per kärna.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skala ditt cacheminne till en annan storlek eller SKU: N för att förbättra Cache- och programprestanda

Cache-instanser gör bäst ifrån sig när det inte körs under hög minnesbelastning, hög serverbelastningen eller hög nätverksbandbredden som kan orsaka att sluta att svara, uppleva dataförlust eller blir otillgänglig. Advisor ska identifiera Cache-instanser i dessa villkor och rekommenderar antingen tillämpa rekommenderade metoder för att minska minnesbelastning, belastningen på servern eller nätverksbandbredd eller skalning till en annan storlek eller SKU: N med högre kapacitet.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Lägga till regioner med trafik till ditt Azure Cosmos DB-konto

Advisor identifierar Azure Cosmos DB-konton som har trafik från en region som inte ännu har konfigurerats och rekommenderar att lägga till den regionen. Detta förbättrar svarstiden för begäranden som kommer från den regionen och säkerställer tillgänglighet vid avbrott i regionen. [Mer information om globala data-distribution med Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurera Azure Cosmos DB indexeringspolicy med kunden inkluderas och uteslutas sökvägar

Azure Advisor identifierar Cosmos DB-behållare som använder standard indexeringspolicy men kan dra nytta av en anpassad indexeringsprincip baserat på mönster för arbetsbelastningen. Standard indexeringspolicy indexerar alla egenskaper, men med en anpassad indexeringsprincip med explicita inkluderade eller exkluderade sökvägar som används i frågefilter kan minska ru: er och lagringsutrymme för indexering. [Läs mer om hur du ändrar index principer](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Ställ in Azure Cosmos DB-frågesidstorleken (MaxItemCount) på -1 

Azure Advisor identifierar Azure Cosmos DB-behållare som använder sidstorlek för fråga om 100 och rekommenderar att du använder en sidstorlek-1 för snabbare sökningar. [Mer information om antal för maximalt antal objekt](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Hur du kommer åt prestandarekommendationer i Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på den **prestanda** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor kostnadsrekommendationer](advisor-performance-recommendations.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)
