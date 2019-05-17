---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2daafa6bf5f9a28ad2b61a97e7a8bd2246ae18d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538362"
---
# <a name="what-disk-types-are-available-in-azure"></a>Vilka disktyper är tillgängliga i Azure?

Azure-hanterade diskar erbjuder för närvarande fyra disktyper, tre som är allmänt tillgänglig (GA) och ett som är för närvarande i förhandsversion. Dessa fyra disktyper varje har sina egna lämpliga målscenarier för kunden.

## <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av ultra solid-state-enheter (SSD) (förhandsversion), premium SSD, standard SSD och standard-hårddiskar (HDD) för hanterade diskar för att avgöra vad du ska använda.

|   | Ultra SSD (förhandsversion)   | Premium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Disktyp   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |I/o-intensiva arbetsbelastningar som SAP HANA, databaser för övre nivå (till exempel SQL, Oracle) och andra transaktion tunga arbetsbelastningar.   |Produktion och prestandakänsliga arbetsbelastningar   |Webbservrar, används företagsprogram och utveckling/testning   |Säkerhetskopiering, icke-kritiska, lågfrekvent åtkomst   |
|Diskstorlek   |65 536 gibibyte (GiB) (förhandsversion)   |32 767 giB    |32 767 giB   |32 767 giB   |
|Högsta dataflöde   |2 000 MiB/s (förhandsversion)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Max IOPS   |160,000 (förhandsversion)   |20,000   |6,000   |2,000   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (förhandsversion)

Azure ultra SSD (förhandsversion) leverera högt dataflöde, hög IOPS och konsekvent låg latens disklagring för virtuella Azure IaaS-datorer. Vissa ytterligare fördelar med ultra SSD omfattar möjligheten att ändra dynamiskt prestanda för disken, tillsammans med dina arbetsbelastningar, utan att behöva starta om dina virtuella datorer. Ultra SSD lämpar sig för dataintensiva arbetsbelastningar som SAP HANA, översta databaser och transaktionen tunga arbetsbelastningar. Ultra SSD kan bara användas som datadiskar. Vi rekommenderar att du använder premium SSD som OS-diskar.

### <a name="performance"></a>Prestanda

När du etablerar ett ultra disk kan konfigureras oberoende kapacitet och prestanda för disken. Ultra SSD komma in flera fasta storlekar, från 4 GiB upp till 64 TiB och använder en modell för flexibla prestanda konfiguration där du kan konfigurera oberoende IOPS och dataflöden.

Några nyckelfunktioner i Ultra SSD är:

- Kapacitet för disk: Ultra SSD-kapacitet intervall från 4 GiB upp till 64 TiB.
- IOPs per disk: Ultra SSD stöder IOPS-gränserna för 300 IOPS/GiB, upp till högst 160 kB IOPS per disk. Kontrollera att den valda Disk-IOPS är mindre än VM IOPS för att uppnå IOPS som du etablerade. Minsta IOPS-disken är 100 IOPS.
- Diskdataflöde: Med ultra SSD dataflödesgräns av en enskild disk är 256 KiB/s för var och en etablerad IOPS, upp till högst 2000 Mbit/s per disk (där Mbit/s = 10 ^ 6 byte per sekund). Det minsta diskgenomflödet är 1 MiB.
- Ultra SSD stödja justera disk Prestandaattribut (IOPS och dataflöde) vid körning utan kopplar bort disken från den virtuella datorn. När storleksändringen en disk prestanda har utfärdats på en disk, kan det ta upp till en timme för att ändringen ska börja gälla faktiskt.

### <a name="disk-size"></a>Diskstorlek

|Diskstorlek (GiB)  |IOPS Caps  |Taket för dataflöde (Mbit/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1 024 – 65 536 (storlekar i det här intervallet som ökar i steg om 1 TiB)     |160,000         |2,000         |

### <a name="transactions"></a>Transaktioner

För ultra SSD: er varje i/o-åtgärden har mindre än eller lika med 256 KiB dataflödets betraktas som en enda i/o-åtgärd. I/o-åtgärder större än 256 KiB dataflödets anses flera I/o med storleken 256 KiB.

### <a name="preview-scope-and-limitations"></a>Förhandsversion av omfång och begränsningar

I förhandsversionen ultra SSD:

- Stöds i östra USA 2 i en enda tillgänglighetszon  
- Kan bara användas med tillgänglighetszoner (tillgänglighetsuppsättningar och enkel VM-distributioner utanför zoner kommer inte har möjlighet att koppla ett ultra disk)
- Stöds endast på ES/DS v3 virtuella datorer
- Finns bara tillgängliga i datadiskar och endast stöd för 4k som fysisk sektorstorlek  
- Kan bara skapas som tomma diskar  
- För närvarande kan endast distribueras med hjälp av Azure Resource Manager-mallar, CLI och python SDK.
- Stöder ännu inte diskbilder, VM-avbildningar, tillgänglighetsuppsättningar, VM-skalningsuppsättningar och Azure-diskkryptering.
- Stöder ännu inte integrering med Azure Backup eller Azure Site Recovery.
- Precis som med [de flesta förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), den här funktionen ska inte användas för produktionsarbetsbelastningar fram till allmän tillgänglighet (GA).
