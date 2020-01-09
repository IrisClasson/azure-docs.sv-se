---
title: Tjänst nivåer – DTU-baserad inköps modell
description: Lär dig mer om tjänst nivåer i den DTU-baserade inköps modellen för enstaka databaser och databaser i pooler för att tillhandahålla beräknings-och lagrings storlekar.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562127"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Tjänst nivåer i den DTU-baserade inköps modellen

Tjänst nivåer i den DTU-baserade inköps modellen särskiljs med en mängd beräknings storlekar med en fast mängd av lagrings utrymme, fast kvarhållningsperiod för säkerhets kopieringar och fast pris. Alla tjänst nivåer i den DTU-baserade inköps modellen ger flexibilitet i att ändra beräknings storlekar med minimal [stillestånds](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)tid. Det finns dock en växel över tid där anslutningen förloras till databasen under en kort tids period, vilket kan minskas med logiken för omprövning. Enkla databaser och elastiska pooler faktureras per timme baserat på tjänstnivå och beräkningsstorleken.

> [!IMPORTANT]
> SQL Database hanterade instansen stöder inte en DTU-baserad inköps modell. Mer information finns i [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> Läs om hur vCore-baserade tjänstnivåer [vCore-baserade tjänstnivåer](sql-database-service-tiers-vcore.md). Information om hur man skiljer DTU-baserade tjänstnivåer och vCore-baserade tjänstnivåer finns i [Azure SQL Database köpa modeller](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Jämför de DTU-baserade tjänstnivåerna

Välja tjänstnivå beror huvudsakligen på kontinuitet för företag-, lagrings- och prestandakrav.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Målarbetsbelastning|Utveckling och produktion|Utveckling och produktion|Utveckling och produktion|
|SLA för drifttid|99,99 %|99,99 %|99,99 %|
|Högsta kvarhållning av säkerhets kopior|7 dagar|35 dagar|35 dagar|
|Processor|Låg|Låg, medel, hög|Medel, hög|
|I/o-genomströmning (ungefärlig) |1-5 IOPS per DTU| 1-5 IOPS per DTU | 25 IOPS per DTU|
|I/o-svarstid (ungefärlig)|5 ms (läsa), 10 ms (skriva)|5 ms (läsa), 10 ms (skriva)|2 ms (läsa/skriva)|
|Kolumnlagringsindexering |Gäller inte|S3 och senare|Stöds|
|Minnesintern OLTP|Gäller inte|Gäller inte|Stöds|
|||||

> [!IMPORTANT]
> Tjänst nivåerna Basic, Standard S0, S1 och S2 ger mindre än en vCore (CPU).  För CPU-intensiva arbets belastningar rekommenderas tjänst nivån S3 eller högre. 
>
>När det gäller data lagring placeras standard-, Standard-S0-och S1-tjänst nivåerna på standard sid blobbar. Med standard Page blobbar används hård diskbaserade lagrings medier som är bäst lämpade för utveckling, testning och andra arbets belastningar som inte används ofta och som är mindre känsliga för prestanda variationer.
>

> [!NOTE]
> Du kan få en kostnads fri Azure SQL-databas på tjänst nivån Basic tillsammans med ett kostnads fritt Azure-konto för att utforska Azure. Mer information finns i [skapa en hanterad molndatabas med ditt kostnadsfria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Enkel databas DTU och Lagringsgränser

Compute-storlekar uttrycks i Databastransaktionsenheter (dtu: er) för enskilda databaser och elastiska Databastransaktionsenheter (edtu: er) för elastiska pooler. Mer information om DTU: er och eDTU: er finns i [DTU-baserad inköps modell](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Lagringsstorlek | 2 GB | 1 TB | 4 TB  |
| Maximala dtu: er | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastisk pool-eDTU-, lagrings- och databas i pool gränser

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Maximala lagringsstorleken per databas  | 2 GB | 1 TB | 1 TB |
| Maximala lagringsstorleken per pool | 156 GB | 4 TB | 4 TB |
| Högsta edtu: er per databas | 5 | 3000 | 4000 |
| Högsta edtu: er per pool | 1600 | 3000 | 4000 |
| Högsta antal databaser per pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala, Tyskland nordöstra, västra centrala USA, US DoD regioner och USA, centrala. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU-Benchmark

Fysiska egenskaper (processor, minne, i/o) som är kopplade till varje DTU-mått kalibreras med hjälp av en benchmark som simulerar verkliga databas-arbetsbelastning.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelera-resultaten till verkliga databasprestanda

Det är viktigt att förstå att alla prestandamått är representativt och vägledande endast. Transaktionspriser uppnås med benchmark-programmet kan inte samma som de som kan uppnås med andra program. Benchmark består av en samling med annan transaktion typer kör mot ett schema som innehåller en mängd tabeller och datatyper. Medan prestandamått utövar samma grundläggande åtgärder som är gemensamma för alla OLTP-arbetsbelastningar, representerar inte någon specifik klass av databas eller ett program. Målet med prestandamått är att tillhandahålla en rimlig guide till den relativa prestandan för en databas som kan förväntas vid skalning upp eller ned mellan storlekar. I verkligheten kan databaser är av olika storlekar och komplexitet, stöta på olika kombinationer av arbetsbelastningar och svarar på olika sätt. Till exempel ett i/o-intensiva program kan träffa tröskelvärden för i/o tidigare eller ett CPU-intensiva program kan träffa CPU-gränser snabbare. Det finns ingen garanti för att en viss databas skalas på samma sätt som prestandamått under ökande belastning.

Prestandamått och dess metoder beskrivs i detalj nedan.

### <a name="benchmark-summary"></a>Benchmark-översikt

Benchmark mäter prestanda för en blandning av grundläggande databas åtgärder som inträffar oftast i OLTP-arbetsbelastningar (Online Transaction Processing). Även om prestandamått är utformad med molnbaserad databehandling i åtanke, databasschemat, ifyllnad av data och transaktioner som har utformats för att vara brett representativ för de grundläggande delarna som används mest i OLTP-arbetsbelastningar.

### <a name="schema"></a>Schema

Schemat har utformats för att ha tillräckligt med variationen och komplexiteten för en mängd olika åtgärder. Benchmark körs mot en databas som består av sex tabeller. Tabeller är indelade i tre kategorier: fast storlek, skalning och växer. Det finns två tabeller för fast storlek. tre skalning tabeller. och en växande tabell. Tabeller med fast storlek har ett konstant antal rader. Skalning tabeller har en kardinalitet som står i proportion till databasens prestanda, men inte ändras under prestandamått. Tabellen växande storlek som en skalning tabeller på första men kardinalitet ändringarna körs prestandamått som rader infogas och raderas.

Schemat innehåller en blandning av datatyper, inklusive heltal, numeriska tecken och datum/tid. Schemat innehåller primära och sekundära nycklarna, men inte alla sekundärnycklar – det vill säga det finns inga referensintegritetsbegränsningar mellan tabeller.

Ett program för generering av data genererar data för den ursprungliga databasen. Heltal och numeriska data genereras med olika strategier. I vissa fall kan distribueras värden slumpmässigt över en rad. I andra fall kan en uppsättning värden är slumpmässigt den omkastade för att säkerställa att en specifik distribution upprätthålls. Textfält genereras från en sorterad lista över ord att producera realistisk söker data.

Databasen är storlek baserat på en ”skalfaktor”. Skalningsfaktorn (förkortas SF) avgör Kardinaliteten för att skala och växande tabeller. Enligt beskrivningen nedan i avsnittet användare och Pacing, databasens storlek, antalet användare och maximala prestanda alla skala i förhållande till varandra.

### <a name="transactions"></a>Transaktioner

Arbetsbelastningen består av nio transaktionstyper som visas i tabellen nedan. Varje transaktion är utformad för att markera en viss uppsättning egenskaper i database engine och system för maskinvara med hög kontrast från andra transaktioner. Den här metoden gör det enklare att utvärdera effekten av olika komponenter för systemets prestanda. Till exempel genererar ”läsa” intensiv transaktionen ett stort antal läsåtgärder från disken.

| Transaktionstyp | Beskrivning |
| --- | --- |
| Läsa Lite |VÄLJ; minnesinterna; skrivskyddad |
| Läs Medium |VÄLJ; huvudsakligen i minnet; skrivskyddad |
| Läs aktiverat |VÄLJ; huvudsakligen inte i minnet; skrivskyddad |
| Uppdatera Lite |UPPDATERA; minnesinterna; skrivskyddad |
| Uppdatera aktiverat |UPPDATERA; huvudsakligen inte i minnet; skrivskyddad |
| Infoga Lite |INFOGA. minnesinterna; skrivskyddad |
| Infoga aktiverat |INFOGA. huvudsakligen inte i minnet; skrivskyddad |
| Ta bort |TA BORT. blandning av i minnet och inte i minnet; skrivskyddad |
| CPU-intensiv |VÄLJ; minnesinterna; relativt tung CPU-belastning. skrivskyddad |

### <a name="workload-mix"></a>Blandning av arbetsbelastning

Transaktioner väljs slumpmässigt bland en viktad distribution med följande övergripande blandning. Den övergripande kombination har en Läs/Skriv-förhållande på cirka 2:1.

| Transaktionstyp | % av blandning |
| --- | --- |
| Läsa Lite |35 |
| Läs Medium |20 |
| Läs aktiverat |5 |
| Uppdatera Lite |20 |
| Uppdatera aktiverat |3 |
| Infoga Lite |3 |
| Infoga aktiverat |2 |
| Ta bort |2 |
| CPU-intensiv |10 |

### <a name="users-and-pacing"></a>Användare och hastighetsstyrningen

Benchmark-arbetsbelastningen drivs från ett verktyg som skickar transaktioner över en uppsättning anslutningar till simulera beteendet för ett antal samtidiga användare. Även om alla anslutningar och transaktioner är datorn som genereras för enkelhetens skull kallar vi dessa anslutningar ”användare”. Även om varje användare fungerar oberoende av alla andra användare, utför alla användare samma cykeln av stegen som visas nedan:

1. Upprätta en databasanslutning.
2. Upprepa tills signal om att avsluta:
   - Välj en transaktion slumpmässigt (från en viktad distribution).
   - Utför den valda transaktionen och mäta svarstiden.
   - Vänta tills en pacing fördröjning.
3. Stäng anslutningen till databasen.
4. Avsluta.

Pacing fördröjningen (i steg 2c) väljs slumpmässigt, men med en distribution som har ett genomsnitt av 1.0 sekund. Därmed kan varje användare i genomsnitt generera högst en transaktion per sekund.

### <a name="scaling-rules"></a>Skalningsregler

Antalet användare som bestäms av databasens storlek (i skalfaktor enheter). Det finns en användare för varje fem skalfaktor enheter. På grund av pacing fördröjningen, kan en användare skapa högst en transaktion per sekund, i genomsnitt.

Till exempel en-skalfaktor av 500 (SF = 500) databasen kommer att ha 100 användare och kan högst 100 TPS. För att driva en högre TPS kräver frekvens fler användare och en större databas.

### <a name="measurement-duration"></a>Varaktighet för mätning

En giltig benchmark-körning kräver en stabil mätning varaktighet på minst en timme.

### <a name="metrics"></a>Mått

Viktiga mått i prestandamått är dataflöde och svarstid.

- Dataflödet är det grundläggande prestandamått i prestandamått. Dataflöde rapporteras i transaktioner per i-tid, räkna alla transaktionstyper.
- Svarstiden är ett mått på förutsägbara prestanda. Tidsbegränsning för svar varierar med tjänsteklass med högre klasser av tjänsten som har strängare svar tid krav, enligt nedan.

| Tjänsteklass | Mått för dataflöde | Tidsåtgången för svar |
| --- | --- | --- |
| Premium |Transaktioner per sekund |95: e percentilen vid 0,5 sekunder |
| Standard |Transaktioner per minut |90: e percentilen vid 1.0 sekunder |
| Basic |Transaktioner per timme |80: e percentilen vid 2.0 sekunder |

## <a name="next-steps"></a>Nästa steg

- För att få information om specifika storlekar och lagring som kan användas för enskilda databaser, se [SQL Database DTU-baserade resursbegränsningar för enskilda databaser](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- För att få information om specifika storlekar och lagring som kan användas för elastiska pooler, se [SQL Database DTU-baserade resursbegränsningar](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
