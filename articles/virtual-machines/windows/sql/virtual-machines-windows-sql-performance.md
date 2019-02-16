---
title: Prestandariktlinjer för för SQL Server i Azure | Microsoft Docs
description: Innehåller riktlinjer för att optimera prestanda för SQL Server i virtuella Microsoft Azure-datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6493da0cfc86560fac8e69f4329804c628942806
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328727"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Prestandavägledning för SQL Server i Azure Virtual Machines

## <a name="overview"></a>Översikt

Den här artikeln innehåller vägledning för att optimera prestanda för SQL Server på Microsoft Azure-dator. När du kör SQL Server i Azure Virtual Machines, rekommenderar vi att du fortsätter med den samma databas alternativen för prestandajustering som gäller för SQL Server i en lokal server-miljö. Prestanda för en relationsdatabas i ett offentligt moln beror dock på många faktorer, till exempel storleken på en virtuell dator och konfigurationen för datadiskar.

[SQL Server-avbildningar som etableras i Azure portal](quickstart-sql-vm-create-portal.md) följa bästa praxis för allmänna konfiguration (Mer information om hur konfigureras lagringsutrymme finns i [lagringskonfiguration för SQL Server-datorer](virtual-machines-windows-sql-server-storage-configuration.md)). Överväg att använda andra optimeringar som beskrivs i den här artikeln när du har etablerat. Basera dina val på din arbetsbelastning och verifiera genom testning.

> [!TIP]
> Det är vanligtvis en kompromiss mellan optimering för kostnader och optimera prestanda. Den här artikeln fokuserar på att få den *bästa* prestanda för SQL Server på virtuella Azure-datorer. Om din arbetsbelastning är mindre systemresurser, kanske du inte kräver varje optimering som anges nedan. Överväg att dina prestandabehov, kostnader och arbetsbelastningmönster medan du utvärderar de här rekommendationerna.

## <a name="quick-check-list"></a>Snabb Checklista

Följande är en snabb kontroll lista för optimala prestanda för SQL Server på Azure Virtual Machines:

| Område | Optimeringar |
| --- | --- |
| [Storlek på virtuell dator](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) eller högre för SQL Enterprise edition.<br/><br/> - [DS2_v2](../sizes-general.md) eller högre för SQL Standard- och webb-utgåvor. |
| [Storage](#storage-guidance) | – Använd [premium SSD](../disks-types.md). Standard-lagring rekommenderas endast för utveckling och testning.<br/><br/> -Håll den [lagringskonto](../../../storage/common/storage-create-storage-account.md) och SQL Server-dator i samma region.<br/><br/> * Inaktivera Azure [geo-redundant lagring](../../../storage/common/storage-redundancy.md) (geo-replikering) för lagringskontot. |
| [Diskar](#disks-guidance) | – Använd minst 2 [P30 diskar](../disks-types.md#premium-ssd) (1 för loggfiler och 1 för datafiler inklusive TempDB). Överväg att använda ett Ultra SSD för arbetsbelastningar som kräver ~ 50 000 IOPS. <br/><br/> – Använd inte operativsystemet eller temporära diskar för databaslagring eller loggning.<br/><br/> -Aktivera Läs cachelagring på diskarna som är värd för filer och datafiler för TempDB.<br/><br/> -Aktivera inte cachelagring på diskar som är värd för loggfilen.  **Viktiga**: Stoppa SQL Server-tjänsten när du ändrar cacheinställningarna för en virtuell dator i Azure-disk.<br/><br/> -Stripe-flera Azure-datadiskar för att få ökad i/o-genomströmning.<br/><br/> -Format med dokumenterat allokering storlekar. <br/><br/> -TempDB plats på lokal SSD för verksamhetskritiska SQLServer arbetsbelastningar (när du har valt rätt VM-storlek). |
| [I/O](#io-guidance) |-Aktivera komprimering för databas-sidan.<br/><br/> -Aktivera initieringen av omedelbar fil för datafiler.<br/><br/> -Begränsa automatisk utökning av databasen.<br/><br/> -Inaktivera automatiska storleksminskningen av databasen.<br/><br/> – Flytta alla databaser till datadiskar, inklusive systemdatabaser.<br/><br/> – Flytta SQL Server fel logg- och spårningsfiler filkataloger till datadiskar.<br/><br/> -Konfigurera säkerhetskopiering och databasen standardsökvägar.<br/><br/> -Aktivera låsta sidor.<br/><br/> -Tillämpa korrigeringar för SQL Server-prestanda. |
| [Funktionsspecifika](#feature-specific-guidance) | -Säkerhetskopiera direkt till blob storage. |

Mer information om *hur* och *varför* för att göra dessa optimeringar, granska informationen och riktlinjerna i följande avsnitt.

## <a name="vm-size-guidance"></a>Vägledning för VM-storlek

För känsliga program för prestanda, vi rekommenderar att du använder följande [storlekar på virtuella datorer](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 eller högre
* **SQL Server Standard- och webb-utgåvor**: DS2_v2 eller högre

[DSv2-serien](../sizes-general.md#dsv2-series) VMs stöd för premium storage, vilket rekommenderas för bästa prestanda. Storlekarna som rekommenderas är här baslinjer, men den faktiska storleken du väljer beror på din arbetsbelastning. Virtuella datorer i DSv2-serien är allmänt virtuella datorer som är bra för en rad olika arbetsbelastningar, medan andra storlekar på datorer som är optimerade för typer av specifika arbetsbelastningar. Till exempel den [M-serien](../sizes-memory.md#m-series) erbjuder högst antal virtuella processorer och minne för de största SQL Server-arbetsbelastningarna. Den [GS-serien](../sizes-memory.md#gs-series) och [DSv2-serien 11-15](../sizes-memory.md#dsv2-series-11-15) är optimerade för stora minneskrav. Båda dessa serien är också tillgängliga i [begränsad core storlekar](../../windows/constrained-vcpu.md), vilket sparar pengar för att få arbetsbelastningar med lägre krav. Den [Ls-serien](../sizes-storage.md) datorer är optimerade för högt diskgenomflöde och I/O. Det är viktigt att tänka på den specifika SQL Server-arbetsbelastningen och koppla den till ditt val av en VM-serie och storlek.

## <a name="storage-guidance"></a>Riktlinjer för Storage

Stöd för virtuella datorer av DS-serien (tillsammans med DSv2-serien och GS-serien) [premium SSD](../disks-types.md). Premium SSD rekommenderas för alla produktionsarbetsbelastningar.

> [!WARNING]
> Standard hårddiskar och SSD har olika svarstider och bandbredd och rekommenderas endast för arbetsbelastningar för utveckling och testning. Produktionsarbetsbelastningar ska använda premium SSD.

Vi rekommenderar dessutom att du skapar din Azure-lagringskonto i samma datacenter som din SQL Server-datorer för att minska fördröjningar i överföringen. När du skapar ett lagringskonto kan du inaktivera geo-replikering som konsekvent skrivning ordning över flera diskar inte är säkert. Överväg istället att konfigurera en SQL Server disaster recovery-teknik mellan två Azure-datacenter. Mer information finns i [hög tillgänglighet och katastrofåterställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Vägledning för diskar

Det finns tre huvudsakliga disktyper på en Azure-dator:

* **OS-disken**: När du skapar en Azure virtuell dator plattformen ska kopplas till minst en disk (märkta som den **C** enhet) till den virtuella datorn för operativsystemets disk. Den här disken är en virtuell Hårddisk som lagras som en sidblobb i lagring.
* **Temporär disk**: Azure-datorer innehåller en annan disk kallas den temporära disken (märkta som den **D**: enhet). Det här är en disk på den nod som kan användas för tillfälliga utrymmet.
* **Datadiskar**: Du kan också koppla ytterligare diskar till din virtuella dator som datadiskar och dessa kommer att lagras i storage som sidblobar.

I följande avsnitt beskrivs rekommendationer för att använda dessa olika diskar.

### <a name="operating-system-disk"></a>Operativsystemdisk

En operativsystemdisk är en virtuell Hårddisk som du kan starta och montera som en som kör version av ett operativsystem och är märkta som **C** enhet.

Standard Cachelagringsprincip på operativsystemdisken är **Läs/Skriv**. För känsliga program för prestanda rekommenderar vi att du använder datadiskar i stället för operativsystemdisken. Se avsnittet på Datadiskar nedan.

### <a name="temporary-disk"></a>Temporär disk

Temporär lagring-enhet, märkta som den **D**: enhet, sparas inte till Azure blob storage. Spara inte din användardatabasfiler eller användaren transaktionsloggfiler på den **D**: enhet.

För D-serien, Dv2-serien och virtuella datorer i G-serien är den temporära enheten på dessa virtuella datorer SSD-baserad. Om din arbetsbelastning gör väldigt mycket för TempDB (till exempel temporära objekt eller komplexa kopplingar), lagring av TempDB på den **D** enhet kan leda till högre TempDB dataflöde och lägre latens TempDB. Ett exempelscenario finns i TempDB-diskussion i följande blogginlägg: [Riktlinjer för Storage-konfiguration för SQLServer på Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

<<<<<<< HEAD för virtuella datorer som har stöd för premium SSD (DS-serien, DSv2-serien och GS-serien), vi rekommenderar att du lagrar TempDB på en disk som har stöd för premium SSD med läscachelagring aktiverat. Det finns ett undantag till den här rekommendationen; Om din användning av TempDB är skrivningsintensiva, kan du få bättre prestanda genom att lagra TempDB på lokalt **D** enhet, som också är SSD-baserade på dessa datorstorlekar.
=== För virtuella datorer som har stöd för Premium Storage (DS-serien, DSv2-serien och GS-serien), rekommenderar vi att du lagrar TempDB på en disk som har stöd för Premium Storage med läscachelagring aktiverat. 

Det finns ett undantag till den här rekommendationen: _om TempDB-användningen är skrivningsintensiva, kan du få bättre prestanda genom att lagra TempDB på lokalt **D** enhet, som också är SSD-baserade på dessa datorstorlekar._ 
>>>>>>> 4326ed494fad7ef7be29e2f4ba3301ec496acf76

### <a name="data-disks"></a>Datadiskar

* **Använda datadiskar för data och loggfiler**: Om du inte använder disk striping, använda två premium SSD P30 diskar där en disk innehåller i loggfilerna och den andra innehåller data och TempDB-filerna. Varje premium SSD ger ett antal IOPs och bandbredd (MBIT/s) beroende på dess storlek, som du ser i artikeln [Välj en disktyp av](../disks-types.md). Om du använder en disk striping teknik, till exempel lagringsutrymmen, uppnå optimala prestanda genom att ha två pooler, en för loggfilerna och den andra för datafiler. Om du planerar att använda SQL Server Failover Cluster instanser (FCI), måste du konfigurera en pool.

   > [!TIP]
   > - Testresultaten på olika konfigurationer för disk- och arbetsbelastning, finns i följande blogginlägg: [Riktlinjer för Storage-konfiguration för SQLServer på Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - För verksamhetskritiska prestanda för SQL-servrar som behöver ~ 50 000 IOPS, Överväg att ersätta 10 - P30-diskar med ett Ultra SSD. Mer information finns i följande blogginlägg: [Verksamhetskritiska prestanda med Ultra SSD](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > När du etablerar en SQL Server-VM i portalen kan har du möjlighet att redigera din konfiguration för lagring. Beroende på din konfiguration konfigurerar en eller flera diskar i Azure. Flera diskar kombineras till en enskild lagringspool med striping. Både data och loggfiler filerna finnas tillsammans i den här konfigurationen. Mer information finns i [lagringskonfiguration för SQL Server-datorer](virtual-machines-windows-sql-server-storage-configuration.md).

* **Disk-Striping**: Du kan lägga till ytterligare datadiskar och använda Disk Striping mer dataflöde. Du behöver analysera antalet IOPS och bandbredd som krävs för din loggfilerna och för dina data och TempDB-filerna för att fastställa antalet datadiskar. Observera att olika storlekar har olika begränsningar för hur många IOPs och bandbredd som stöds, finns i tabellerna om IOPS per [VM-storlek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Använd följande riktlinjer:

  * Windows 8 och Windows Server 2012 eller senare, Använd [lagringsutrymmen](https://technet.microsoft.com/library/hh831739.aspx) med följande riktlinjer:

      1. Ange interfoliering (stripe-storlek) till 64 KB (65536 byte) för OLTP-arbetsbelastningar och 256 KB (262 144 byte) för Datalagerhantering för att undvika att prestanda påverkas på grund av partitionen misspassning. Detta måste anges med PowerShell.
      2. Ange kolumnantal = antal fysiska diskar. Använda PowerShell när du konfigurerar mer än 8 diskar (inte Server Manager UI). 

    Till exempel skapar en ny lagringspool med interfoliering storlek till 64 KB och antalet kolumner till 2 med följande PowerShell:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * För Windows 2008 R2 eller tidigare, kan du använda dynamiska diskar (OS stripe-volymer) och stripe-storlek är alltid 64 KB. Observera att det här alternativet används inte i Windows 8 och Windows Server 2012. Information finns i supportmeddelande på [Virtual Disk Service övergår till Windows Storage Management API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Om du använder [Lagringsdirigering (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med [SQL Server-Redundansklusterinstanser](virtual-machines-windows-portal-sql-create-failover-cluster.md), måste du konfigurera en enda pool. Observera att även om olika volymer kan skapas på den enda poolen, de alla delar samma egenskaper, till exempel samma principen för cachelagring.

  * Bestämma antalet diskar som är associerade med din lagringspool baserat på dina förväntningar för belastningen. Tänk på att olika storlekar på Virtuella datorer tillåter olika antal anslutna datadiskar. Mer information finns i [storlekar för virtuella datorer](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Om du inte använder premium SSD (utveckling och testning), rekommendationen är att lägga till det maximala antalet datadiskar som stöds av din [VM-storlek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och använda Disk Striping.

* **Cachelagringsprincip**: Observera följande rekommendationer för Cachelagringsprincip beroende på din konfiguration för lagring.

  * Om du använder separata diskar för data och loggfiler kan du aktivera läscachelagring på datadiskar som är värd för dina datafiler och datafilerna för TempDB. Detta kan resultera i en betydande fördel. Aktivera inte cachelagring på disken som innehåller loggfilen eftersom det orsakar en mindre minskning i prestanda.

  * Om du använder disk striping i en enskild lagringspool, kommer de flesta arbetsbelastningar dra nytta av läscachelagring. Om du har separata lagringspooler för logg- och datafiler kan du aktivera läscachelagring endast på lagringspoolen för datafiler. I vissa tunga arbetsbelastningar kan bättre prestanda uppnås med ingen cachelagring. Detta kan endast bestämmas genom testning.

  * Rekommendationerna ovan gäller till premium SSD: er. Om du inte använder premium SSD: er kan du inte aktivera alla cachelagring på eventuella datadiskar.

  * Mer information om hur du konfigurerar diskcachelagring finns i följande artiklar. Klassiskt (ASM) distributionsmodell finns: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) och [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Azure Resource Manager deployment model finns i: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-4.4.1) och [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk?view=azurermps-4.4.1).

     > [!WARNING]
     > Stoppa SQL Server-tjänsten när du ändrar cache-inställningen för Virtuella Azure-diskar för att undvika risken att databasen är skadad.

* **Storlek på NTFS-allokeringsenhet**: Formaterar datadisken och rekommenderas det att du använder en storlek på allokeringsenhet 64 KB för data och loggfiler samt TempDB.

* **Disk rekommenderade metoder**: När ta bort en datadisk eller ändra dess cachetyp stoppa SQL Server-tjänsten under tiden. När cachelagringsinställningarna har ändrats på OS-disken, Azure stoppar den virtuella datorn, ändras cachetyp och startar om den virtuella datorn. När cacheinställningarna för en datadisk ändras kan den virtuella datorn stoppas inte, men datadisken är frånkopplat från den virtuella datorn under ändringen och sedan återansluta.

  > [!WARNING]
  > Det gick inte att stoppa tjänsten SQL Server vid de här åtgärderna kan orsaka databasfel.


## <a name="io-guidance"></a>I/o-vägledning

* Bästa resultat med premium SSD uppnås när du parallellisera ditt program och förfrågningar. Premium SSD: er är utformade för scenarier där ködjupet för i/o är större än 1, så att du kommer se lite eller ingen prestandavinster för single-threaded seriell begäranden (även om de är lagring beräkningsintensiva). Detta kan till exempel påverka entrådiga testresultaten olika analysverktyg för prestanda, till exempel SQLIO.

* Överväg att använda [databasen sidan komprimering](https://msdn.microsoft.com/library/cc280449.aspx) som kan förbättra prestanda för i/o-intensiva arbetsbelastningar. Datakomprimering kan dock öka CPU-användning på databasservern.

* Överväg att aktivera initieringen av omedelbar fil att minska den tid som krävs för inledande filen allokering. Om du vill dra nytta av omedelbar filen initieringen du bevilja tjänstkontot för SQL Server (MSSQLSERVER) med SE_MANAGE_VOLUME_NAME och lägger till den i den **utföra underhållsaktiviteter** säkerhetsprincip. Om du använder en plattformsavbildning för SQL Server för Azure service-kontot av standardtyp (NT Service\MSSQLSERVER) är inte läggs till i **utföra underhållsaktiviteter** säkerhetsprincip. Initieringen av omedelbar fil är med andra ord inte aktiverad i en SQL Server Azure-plattformsavbildning. När du lägger till SQL Server-tjänstkontot till den **utföra underhållsaktiviteter** säkerhetsprinciper, starta om SQL Server-tjänsten. Det kan finnas säkerhetsöverväganden vid användning av den här funktionen. Mer information finns i [databasen filen initieringen](https://msdn.microsoft.com/library/ms175935.aspx).

* **automatisk storleksökning** anses vara bara en reservplan vid oväntad storleksökning. Hantera inte dina data och loggfiler tillväxt på basis dagliga med automatisk storleksökning. Om du använder automatisk storleksökning, före växa filen med växeln storlek.

* Se till att **storleksminskning** är inaktiverat för att undvika onödig som kan påverka prestanda negativt.

* Flytta alla databaser till datadiskar, inklusive systemdatabaser. Mer information finns i [flytta systemdatabaser](https://msdn.microsoft.com/library/ms345408.aspx).

* Flytta SQL Server fel logg- och spårningsfiler filkataloger till datadiskar. Detta kan göras i Konfigurationshanteraren för SQL Server genom att högerklicka på SQL Server-instansen och välja egenskaper. Fel logg- och spårningsfiler filinställningar kan ändras i den **Startparametrar** fliken. Dump-katalogen har angetts i den **Avancerat** fliken. Följande skärmbild visar att söka efter fel log Start-parametern.

    ![Skärmbild för SQL-felloggen](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Konfigurera säkerhetskopiering och databasen standardsökvägar. Använd rekommendationer i den här artikeln och gör ändringar i egenskapsfönstret för servern. Anvisningar finns i [visa eller ändra de platser som standard för Data och loggfiler (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Följande skärmbild visar var du vill göra dessa ändringar.

    ![SQL Data logg-och säkerhetskopiering](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Aktivera låsta sidor att minska i/o och några sidindelning aktiviteter. Mer information finns i [aktivera den låsa sidor i minnet alternativet (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Om du kör SQL Server 2012 måste du installera Service Pack 1 Cumulative Update 10. Den här uppdateringen innehåller korrigering för låga prestanda på i/o när du kör väljer till tillfällig tabell-satsen i SQL Server 2012. Information finns i den här [knowledge base-artikeln](https://support.microsoft.com/kb/2958012).

* Överväg att komprimera några filer när du överför in/ut i Azure.

## <a name="feature-specific-guidance"></a>Funktionsspecifika vägledning

Vissa distributioner kan uppnå ytterligare prestandafördelarna med hjälp av mer avancerade tekniker i konfigurationen. I följande lista beskrivs några SQL Server-funktioner som kan hjälpa dig att få bättre prestanda:

* **Säkerhetskopiering till Azure storage**: När du utför säkerhetskopiering för SQL Server som körs i Azure virtual machines, du kan använda [SQL Server-säkerhetskopiering till URL: en](https://msdn.microsoft.com/library/dn435916.aspx). Den här funktionen är tillgänglig från och med SQL Server 2012 SP1 CU2 och rekommenderas för säkerhetskopiering till de anslutna datadiskarna. När du säkerhetskopiering/återställning till och från Azure storage, Följ rekommendationerna som anges på [SQL Server-säkerhetskopiering till URL: en metodtips och felsökning och återställa från säkerhetskopior som lagras i Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Du kan även automatisera dessa säkerhetskopior med hjälp av [automatisk säkerhetskopiering för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

    Du kan använda innan SQL Server 2012, [SQL Server-säkerhetskopiering till Azure-verktyget](https://www.microsoft.com/download/details.aspx?id=40740). Det här verktyget kan bidra till att öka säkerhetskopiering dataflöde med hjälp av flera säkerhetskopiering stripe-mål.

* **SQL Server-datafiler i Azure**: Den här nya funktionen [SQL Server-datafiler i Azure](https://msdn.microsoft.com/library/dn385720.aspx), är tillgängliga från och med SQL Server 2014. Kör SQL Server med datafiler i Azure visar jämförbara prestandaegenskaper som med hjälp av Azure-datadiskar.

## <a name="next-steps"></a>Nästa steg

Läs mer om lagring och prestanda, [riktlinjer för Storage-konfiguration för SQL Server på Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Rekommenderade säkerhetsmetoder, se [säkerhetsöverväganden för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Granska andra artiklar för SQL Server-dator på [SQL Server på Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).
