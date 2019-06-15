---
title: 'Företag affärskontinuitet och haveriberedskap recovery (BCDR): Parade Azure-regioner | Microsoft Docs'
description: Läs mer om Azure regional länkning, för att säkerställa att programmen är elastisk vid data center haverier.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 5ed9dc595c537d8a923d3eb056dcb002cf225f7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427108"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Företag affärskontinuitet och haveriberedskap recovery (BCDR): Parade Azure-regioner

## <a name="what-are-paired-regions"></a>Vad är länkade regioner?

Azure körs på flera geografiska områden runtom i världen. En Azure geografiskt område är en definierad del av världen som innehåller minst en Azure-Region. En Azure-region är ett område inom ett geografiskt område som innehåller ett eller flera datacenter.

Varje Azure-region är kopplad till en annan region inom samma geografiska område, tillsammans att göra en regionala par. Undantaget är södra Brasilien, som är kopplad till en region utanför dess geografisk plats. I regionpar Serialiserar Azure platform uppdateringar (planerat underhåll), så att endast en länkad region uppdateras samtidigt. Vid ett eventuellt strömavbrott påverkar flera områden, prioriteras minst en region i varje par för återställning.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Bild 1 – Azure regionala par

| Geografi | Länkade regioner |  |
|:--- |:--- |:--- |
| Asien |Östasien |Sydostasien |
| Australien |Östra Australien |Sydöstra Australien |
| Australien |Australien, centrala |Australien, centrala 2 |
| Brasilien |Södra Brasilien |Södra centrala USA |
| Kanada |Centrala Kanada |Östra Kanada |
| Kina |Norra Kina |Östra Kina|
| Kina |Kina, norra 2 |Kina, östra 2|
| Europa |Nordeuropa (Irland) |Västeuropa (Nederländerna) |
| Frankrike |Centrala Frankrike|Frankrike, södra|
| Tyskland |Centrala Tyskland |Nordöstra Tyskland |
| Indien |Indien, centrala |Södra Indien |
| Indien |Indien, västra |Södra Indien |
| Japan |Östra Japan |Västra Japan |
| Korea |Sydkorea, centrala |Sydkorea, södra |
| Nordamerika |Östra USA |Västra USA |
| Nordamerika |USA, östra 2 |Centrala USA |
| Nordamerika |Norra centrala USA |Södra centrala USA |
| Nordamerika |Västra USA 2 |Västra centrala USA 
| Sydafrika | Sydafrika, norra | Sydafrika, västra
| Storbritannien |Storbritannien, västra |Storbritannien, södra |
| Förenade Arabemiraten | Förenade Arabemiraten, norra | Förenade Arabemiraten, centrala
| USA:s försvarsdepartement |US DoD, östra |US DoD, centrala |
| Amerikanska myndigheter |Arizona (USA-förvaltad region) |Texas (USA-förvaltad region) |
| Amerikanska myndigheter |US Gov, Iowa |Virginia (USA-förvaltad region) |
| Amerikanska myndigheter |Virginia (USA-förvaltad region) |Texas (USA-förvaltad region) |

Tabell 1 - mappning av Azure regionala par

- Västra Indien är länkad i en riktning. Västra Indien sekundär region är södra Indien, men södra Indien sekundär region är centrala Indien.
- Södra Brasilien är unikt eftersom den är kopplad till en region utanför sin egen geografi. Södra Brasilien sekundär region är södra centrala USA. Södra centrala USA sekundära regionen är inte södra Brasilien.
- USA Gov Iowa sekundär region är Virginia (USA-förvaltad region).
- USA-förvaltad region Virginia sekundär region är US Gov Texas.
- USA Gov Texas sekundär region är Arizona (USA-förvaltad region).


Vi rekommenderar att du konfigurerar kontinuitet för företag-haveriberedskap (BCDR) över regionala par kan dra nytta av Azures principer för isolering och tillgänglighet. För program som stöder flera aktiva regioner, bör du använda båda regionerna i regionparet där det är möjligt. Det säkerställer optimala tillgänglighet för program och minimerade återställningstid vid ett haveri. 

## <a name="an-example-of-paired-regions"></a>Ett exempel på länkade regioner
Bild 2 nedan visar ett hypotetiskt program som använder den regionala par för katastrofåterställning. Grön talen Markera interregionala aktiviteter tre Azure-tjänster (Azure databearbetning, lagring och databas) och hur de konfigureras för att replikera mellan regioner. De unika fördelarna med att distribuera över länkade regioner är markerade med orange siffror.

![Översikt över parad Region fördelar](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Bild 2 – hypotetiska Azure regionala par

## <a name="cross-region-activities"></a>Interregionala aktiviteter
Enligt figur 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** – måste du etablera ytterligare beräkningsresurser i förväg för att säkerställa att resurser är tillgängliga i en annan region vid ett haveri. Mer information finns i [Azure återhämtning, tekniska riktlinjer](resiliency/resiliency-technical-guidance.md).

![Storage](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** -Geo-Redundant lagring (GRS) är konfigurerat som standard när ett Azure Storage-konto har skapats. Med GRS, är dina data automatiskt replikeras tre gånger inom den primära regionen och tre gånger i den parade regionen. Mer information finns i [redundansalternativ för Azure Storage](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – med Azure SQL Database Geo-replikering, kan du konfigurera asynkron replikering av transaktioner till alla regioner i världen; men vi rekommenderar att du distribuerar dessa resurser i en länkad region för de flesta scenarier med haveriberedskap. Mer information finns i [Geo-replikering i Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** -Resource Manager tillhandahåller sin natur logisk isolering av komponenter i olika regioner. Det innebär att logiska fel i en region är mindre troligt att påverka en annan.

## <a name="benefits-of-paired-regions"></a>Fördelarna med länkade regioner
Enligt figur 2.  

![Isolering](./media/best-practices-availability-paired-regions/5Orange.png)
**fysisk isolering** – när möjligt, Azure föredrar minst 300 miles uppdelning mellan datacenter i ett regionalt par, även om detta inte är praktiskt eller möjligt i alla geografiska områden. Uppdelning av fysiska datacenter minskar sannolikheten för naturkatastrofer, civilrättsliga oroligheter, strömavbrott eller avbrott i fysiska nätverk som påverkar båda regionerna samtidigt. Isolering är begränsningar i geografiska område (geografi storlek, power/nätverkets infrastruktur tillgänglighet, bestämmelser, osv.).  

![Replikering](./media/best-practices-availability-paired-regions/6Orange.png)
**plattformen erbjuder replikering** -vissa tjänster, till exempel Geo-Redundant lagring tillhandahåller automatisk replikering till den parade regionen.

![Recovery](./media/best-practices-availability-paired-regions/7Orange.png)
**Region betalningskrav** – i händelse av ett avbrott i återställning av en region prioriteras varje par. Program som distribueras på länkade regioner garanterat har ett av de regioner som har återställts med prioritet. Om ett program distribueras över regioner som inte har parats ihop kan återställning fördröjas – i värsta fall de valda regionerna kanske de sista två som ska återställas.

![Uppdateringar](./media/best-practices-availability-paired-regions/8Orange.png)
**sekventiella uppdateringar** – planerat Azure systemuppdateringarna distribueras till ihopparade regioner sekventiellt (inte på samma gång) att minimera nedtid, effekten av buggar och logiska fel om av en felaktig Uppdatera.

![Data](./media/best-practices-availability-paired-regions/9Orange.png)
**dataplacering** – en region befinner sig inom samma geografiska område som paren (med undantag för södra Brasilien) för att uppfylla krav på dataplacering av skatte- och jurisdiktionsmässiga skäl.
