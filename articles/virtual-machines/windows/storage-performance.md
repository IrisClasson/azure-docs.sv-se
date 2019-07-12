---
title: Optimera prestanda på virtuella datorer i Azure Lsv2-serien – lagring | Microsoft Docs
description: Lär dig mer om att optimera prestanda för din lösning på de virtuella datorerna Lsv2-serien.
services: virtual-machines-windows
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 7e96fb6fc7b3e581d058ef0f96124959189d0f4e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709810"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimera prestanda på de virtuella datorerna Lsv2-serien

Lsv2-serien virtuella datorer stöder en mängd olika arbetsbelastningar som behöver hög i/o och dataflöde på lokal lagring över en mängd olika program och branscher.  Lsv2-serien är perfekt för Stordata, SQL, NoSQL-databaser, informationslagerhantering och stora transaktionsdatabaser, inklusive Cassandra, MongoDB, Cloudera och Redis.

Utformningen av Lsv2-serien virtuella datorer (VM) maximerar AMD EPYC™ 7551 processorn för att ge bästa möjliga prestanda mellan processorn, minne, NVMe-enheter och de virtuella datorerna. Förutom att maximera maskinvaruprestanda, är virtuella datorer Lsv2-serien utformade att fungera med behoven hos Windows och Linux-operativsystem för bättre prestanda med maskinvaran och programvaran.

Justering av program- och maskinvarukraven resulterade i den optimerade versionen av [Windows Server 2019 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), som släpptes i tidig December 2018 på Azure Marketplace som har stöd för maximal prestanda på NVMe-enheter i Lsv2-serien Virtuella datorer.

Den här artikeln innehåller tips och förslag för att se till att dina arbetsbelastningar och program få ut maximala prestanda som utformats i de virtuella datorerna. Information om den här sidan kommer att uppdateras kontinuerligt när fler avbildningar av Lsv2 optimerade läggs till på Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ kretsuppsättningsarkitektur

Lsv2-serien virtuella datorer använder processorer för AMD EYPC™ server baserat på Zen mikroarkitektur. AMD utvecklats Infinity Fabric (om) för EYPC™ som skalbara sammankoppling för dess NUMA-modell som kan användas för kommunikation på chips, på paketet och flera paket. Jämfört med QPI (Snabbstart-Path sammankoppling) och UPI (Ultra-Path sammankoppling) används på Intel moderna monolitisk chips processorer, AMD: s många NUMA små chips arkitektur se båda dra nytta av prestanda samt utmaningar. Faktisk påverkan av nätverksbandbredd och svarstid minnesbegränsningar kan variera beroende på vilken typ av arbetsbelastningar som körs.

## <a name="tips-for-maximizing-performance"></a>Tips för att maximera prestanda

* Maskinvaran som används av de virtuella datorerna Lsv2-serien använder NVMe-enheter med åtta par för i/o-kö (QP) s. Varje kö för NVMe-i/o-enhet är egentligen ett par: en bidrag kö och en slutförande-kö. NVMe-drivrutinen har ställts in för att optimera användningen av dessa åtta i/o-frågor per sekund genom att distribuera jag / systemcachens i en resursallokering schemalägga. Köra åtta jobb per enhet för att matcha för att få maximala prestanda.

* Undvik att blanda NVMe admin-kommandon (till exempel NVMe SMART info-fråga, osv.) med NVMe-i/o-kommandon under aktiva arbetsbelastningar. Lsv2 NVMe-enheterna backas upp av Hyper-V NVMe Direct-teknik, växla till ”långsam läget” när kommandon som administratör NVMe är väntande. Lsv2-användare kan få se en dramatisk prestanda släppa NVMe-i/o-prestanda i så fall.

* Lsv2-användare bör inte förlita dig på NUMA enhetsinformation (alla 0) rapporterades från den virtuella datorn för dataenheter bestämma NUMA-tillhörighet för sina appar. Det rekommenderade sättet för bättre prestanda är att sprida arbetsbelastningar över processorer om möjligt. 

* Stöds ködjup per i/o-kö-par för Lsv2 VM NVMe-enhet är 1024 (jämfört med Amazon i3 Ködjup 32 gräns). Lsv2-användare bör begränsa sina (syntetiska) benchmarking arbetsbelastningar till ködjup 1024 eller lägre för att undvika att utlösa kö fullständiga villkor som kan minska prestandan.

## <a name="utilizing-local-nvme-storage"></a>Använda lokal NVMe-lagring

Lokal lagring på disk på NVMe 1.92 TB på alla virtuella datorer Lsv2 är tillfälliga. Data på den lokala NVMe-disken finns kvar under en lyckad standard omstart av den virtuella datorn. Data behålls inte på NVMe om den virtuella datorn distribueras om, avallokeras eller tas bort. Data behålls inte om ett annat problem gör att den virtuella datorn eller den maskinvara som den körs på, för att blivit defekt. Då kan raderas på ett säkert sätt alla data på den gamla värden.

Det kommer också att fall när den virtuella datorn behöver flyttas till en annan värddator, exempelvis under en planerat underhåll. Planerat underhåll och en del maskinvarufel kan ske med [Scheduled Events](scheduled-events.md). Schemalagda händelser bör användas för att hålla dig uppdaterad på alla förväntade underhåll och återställningsåtgärder.

I det fallet som en planerad underhållshändelse kräver att den virtuella datorn skapas på nytt på en ny värd med tomma lokala diskar kan måste data synkroniseras om (igen, med alla data på den gamla värden på ett säkert sätt raderas). Det beror på att virtuella datorer Lsv2-serien inte stöder för närvarande Direktmigrering på den lokala NVMe-disken.

Det finns två lägen för planerat underhåll.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standard VM kund-kontrollerade Underhåll

- Den virtuella datorn flyttas till en uppdaterad värd under en 30-dagars-fönstret.
- Lsv2 lokal lagringsdata kan gå förlorade, så att säkerhetskopiering av data för händelsen rekommenderas.

### <a name="automatic-maintenance"></a>Automatiskt Underhåll

- Inträffar om kunden inte körs kund-kontrollerade underhåll, eller i händelse av akuta åtgärder, till exempel en säkerhetshändelse nolldagsattacker.
- Avsedd att bevara kunddata, men det finns en liten risk för en VM-låsning eller starta om datorn.
- Lsv2 lokal lagringsdata kan gå förlorade, så att säkerhetskopiering av data för händelsen rekommenderas.

För alla kommande händelser, använder du kontrollerad underhållsprocessen för att välja en tid som är enklast att du för uppdateringen. Innan händelsen, kan du säkerhetskopiera dina data i premium-lagring. När händelsen Underhåll är klar kan du returnera data uppdateras Lsv2 virtuella datorer lokala NVMe lagring.

Scenarier som underhåller data på den lokala NVMe diskar är:

- Virtuellt datorn körs och är felfria.
- Den virtuella datorn startas om på plats (av dig eller Azure).
- Den virtuella datorn har pausats (stoppad utan inaktivering allokering).
- Merparten av planerat underhåll underhållsåtgärder.

Scenarier som säkert att Raderar data för att skydda kunden är:

- Den virtuella datorn är på nytt, stoppas (frigörs), eller tas bort (av dig).
- Den virtuella datorn blir ohälsosamt och har tjänsten reparation av nodtjänst till en annan nod bero på maskinvaruproblem.
- Ett litet antal det planerade underhållet underhållsåtgärder som kräver att den virtuella datorn ska allokeras till en annan värd för behandling.

Läs mer om alternativ för säkerhetskopiering av data i lokal lagring i [återställning för säkerhetskopiering och haveriberedskap för Azure IaaS-diskar](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

* **Hur börjar jag distribuera Lsv2-serien virtuella datorer?**  
   Mycket precis som med andra virtuella datorer, använda den [Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md), eller [PowerShell](quick-create-powershell.md) skapa en virtuell dator.

* **Kommer ett enda NVMe-diskfel gör att alla virtuella datorer på värden för att misslyckas?**  
   Om en hårddiskkrasch har identifierats på noden för maskinvara, är maskinvaran i ett felaktigt tillstånd. När detta inträffar är automatiskt alla virtuella datorer på noden avallokeras och flyttas till en felfri nod. För virtuella datorer Lsv2-serien innebär det att kundens data på noden misslyckas raderas även på ett säkert sätt och måste återskapas av kunden på den nya noden. Enligt vad som anges innan Direktmigrering blir tillgängligt på Lsv2, flytta data på noden misslyckas proaktivt med de virtuella datorerna som de överförs till en annan nod.

* **Behöver jag göra justeringar för avsökning i Windows i Windows Server 2012 eller Windows Server 2016?**  
   NVMe avsökningen är bara tillgängligt på Windows Server 2019 på Azure.  

* **Kan jag gå tillbaka till en traditionell interrupt service routine (ISR)-modell?**  
   Virtuella datorer Lsv2-serien är optimerade för NVMe avsökning. Uppdateringar som kontinuerligt förbättra prestanda för avsökning.

* **Kan jag justera avsökningsinställningar i Windows Server 2019?**  
   Sökningsutlösare inställningar är inte användaren justerbara.
   
## <a name="next-steps"></a>Nästa steg

* Se specifikationer för alla [virtuella datorer som är optimerade för lagringsprestanda](sizes-storage.md) på Azure
