---
title: Ange ny värd för en Contoso-app genom att migrera den virtuella Azure-datorer och SQL Server AlwaysOn-tillgänglighetsgruppen | Microsoft Docs
description: Lär dig hur Contoso rehost en lokal app genom att migrera den till virtuella datorer i Azure och SQL Server AlwaysOn-tillgänglighetsgrupp
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 535ba0049e91e09de3d1dcf05fc8ede80ef403ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60672215"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso-migrering: Ange ny värd för en lokal app på Azure virtuella datorer och SQL Server AlwaysOn-tillgänglighetsgrupp

Den här artikeln visar hur Contoso namnkonflikt SmartHotel360-app i Azure. Contoso migrerar app klientdelens VM till en Azure-dator och app-databas till en Azure SQL Server-dator, som körs i ett redundanskluster för Windows Server med SQL Server AlwaysOn-Tillgänglighetsgruppen gGroups.

Det här dokumentet är i en serie av artiklar som visar hur det fiktiva företaget Contoso migrerar lokala resurser till Microsoft Azure-molnet. Serien innehåller grundläggande information och scenarier som illustrerar hur du konfigurerar en infrastruktur för migrering, utvärdera lokala resurser för migrering och som kör olika typer av migreringar. Scenarier växer i komplexitet. Vi lägger till ytterligare artiklar med tiden.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: Översikt över](contoso-migration-overview.md) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt   
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp | Contoso migrerar SmartHotel360-app. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Den här artikeln
[Artikel 7: Ange ny värd för en app för Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso har slutförts en lift and shift-migrering av Linux osTicket app på virtuella Azure-datorer med Azure Site Recovery | Tillgängligt
[Artikel 8: Ange ny värd för en app för Linux på Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Azure Site Recovery och migrerar app-databasen till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app på Azure Web Apps och Azure SQL-databas](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar SmartHotel360-app till ett Azure Web Apps och app-databasen har migrerats till en Azure SQL Server-instans med Database Migration Assistant | Tillgängligt
[Artikel 10: Omstrukturera en app för Linux på Azure Web Apps och Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt 
[Artikel 11: Omstrukturera TFS på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Contoso migrerar dess lokal Team Foundation Server-distribution till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app på Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt
[Artikel 14: Skala en migrering till Azure](contoso-migration-scale.md) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Tillgängligt


I den här artikeln migrerar Contoso tvålagers-Windows .NET SmartHotel360-app som körs på virtuella VMware-datorer till Azure. Om du vill använda den här appen tillhandahålls som öppen källkod och du kan ladda ned det från [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har haft ett nära samarbete med affärspartners att förstå vad de vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer och därför har Press på lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och förenkla processer för utvecklare och användare.  Affärsbehov IT ska vara snabbt och inte spill tid eller pengar och därför leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**:  Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi.  IT får inte vara i vägen eller bli en blockerare för företag.
- **Skala**: När verksamheten växer har, ange Contoso IT system som kommer växa i samma takt.

## <a name="migration-goals"></a>Migreringsmål

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål som användes för att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestandafunktioner eftersom det gör idag i VMWare.  Appen finns kvar som kritiskt i molnet eftersom det är på plats.
- Contoso vill inte investera i den här appen.  Det är viktigt att verksamheten, men i sin nuvarande form Contoso bara vill flytta på ett säkert sätt till molnet.
- Den lokala databasen för appen har haft problem med tillgängligheten. Contoso vill distribuera det i Azure som ett kluster med hög tillgänglighet, med funktioner för redundans.
- Contoso vill uppgradera från deras aktuella SQL Server 2008 R2-plattform till SQL Server 2017.
- Contoso vill inte använda en Azure SQL-databas för den här appen och är ute efter alternativ.


## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned sina mål och krav, Contoso utformar går igenom en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som ska användas för migreringen.

### <a name="current-architecture"></a>Aktuella arkitektur

- Appen är nivåindelad över två virtuella datorer (WEBVM och SQLVM).
- De virtuella datorerna finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).


### <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Contoso migrerar app klientdelen WEBVM till en Azure IaaS-dator.
    - Klientdelen virtuell dator i Azure kommer att distribueras i resursgruppen ContosoRG (används för produktionsresurser).
    -  Det ska finnas i det Azure-produktionsnätverket (VNET-PROD-EUS2) i den primära regionen för östra usa2.
- App-databasen kommer att migreras till en Azure SQL Server-dator.
    - Det ska finnas i Contosos Azure database nätverk (PROD-DB-EUS2) i den primära regionen för östra usa2.
    - Det kommer att placeras i ett Windows Server failover-kluster med två noder som använder SQL Server Always On-Tillgänglighetsgrupper.
    - De två SQL Server-VM-noderna i klustret kommer att distribueras i resursgruppen ContosoRG i Azure.
    - VM-noder finns i det Azure-produktionsnätverket (VNET-PROD-EUS2) i den primära regionen för östra usa2.
    - Virtuella datorer körs Windows Server 2016 med SQL Server 2017 Enterprise Edition. Contoso har licenser för operativsystemet, så den använder en avbildning i Azure Marketplace som innehåller licensen som ett tillägg till åtagandet Azure EA.
    - Förutom de unika namn använder båda virtuella datorer samma inställningar.
- Contoso distribuerar en intern belastningsutjämnare som lyssnar efter trafik på klustret och dirigerar den till lämplig klusternod.
    - Den interna belastningsutjämnaren ska distribueras i ContosoNetworkingRG (används för nätverksresurser).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="database-considerations"></a>Överväganden för databasen

Som en del av designprocessen gjorde Contoso en funktionsjämförelse mellan Azure SQL Database och SQL Server. Följande överväganden hjälpte dem att bestämma att en virtuell Azure IaaS-dator som kör SQL Server:

 - Med hjälp av en Azure-dator som kör SQL Server verkar vara en optimal lösning om Contoso behöver anpassa operativsystemet eller databasservern, eller om det kanske vill samordna och köra appar från tredje part på samma virtuella dator.
 - Med Data Migration Assistant, kan Contoso enkelt utvärdera och migrera till en Azure SQL Database.
 

### <a name="solution-review"></a>Granska lösningen

Contoso utvärderar sina föreslagna design genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** | WEBVM kommer att flyttas till Azure utan ändringar, kan du förenkla migreringen.<br/><br/> SQL Server-nivå ska köras på SQL Server 2017 och Windows Server 2016. Detta drar tillbaka deras aktuella Windows Server 2008 R2-operativsystem och som kör SQL Server 2017 stöder Contosos tekniska krav och mål. IT ger 100% kompatibilitet samtidigt som rör sig från SQL Server 2008 R2.<br/><br/> Contoso kan utnyttja deras investering i Software Assurance, med hjälp av Azure Hybrid-förmånen.<br/><br/> En SQL Server-distribution i Azure med hög tillgänglighet ger feltolerans så att datanivån för appen är inte längre en enda åtkomstpunkt för redundans.
**Nackdelar** | WEBVM kör Windows Server 2008 R2. Operativsystemet stöds av Azure för specifika roller (juli 2018). [Läs mer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).<br/><br/> Webbnivå appens förblir en enda åtkomstpunkt för redundans.</br><br/> Contoso behöver att fortsätta stödja webbnivån som en Azure virtuell dator i stället för att flytta till en hanterad tjänst, till exempel Azure App Service.<br/><br/> Med den valda lösningen måste Contoso fortsätta att hantera två SQL Server-datorer i stället för att flytta till en hanterad plattform, till exempel Azure SQL Database Managed Instance. Dessutom kan Contoso med Software Assurance exchange sina befintliga licenser för rabatterade priser på Azure SQL Database Managed Instance.


### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA körs lokalt från en lokal SQL Server-datorn och migrerar databasen på en plats-till-plats VPN-anslutning till Azure. | DMA är ett kostnadsfritt, nedladdningsbart verktyg.
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betala, vid redundans. [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

 

## <a name="migration-process"></a>Migreringsprocessen

Contoso-administratörer kommer att migrera de virtuella datorerna för appen till Azure.

- De ska migrera klientdelens VM till virtuell dator i Azure med Site Recovery:
    - Som ett första steg de Förbered och konfigurera Azure-komponenter och förbereda lokala VMware-infrastruktur.
    - Med allt förberett börjar de replikera den virtuella datorn.
    - När replikering har aktiverats och arbetar måste de migrera den virtuella datorn genom att växla över till Azure.
- De ska migrera databasen till SQL Server-kluster i Azure med den Data Migration Assistant (DMA).
    - Som ett första steg behöver för de att etablera SQL Server-datorer i Azure, konfigurera klustret och en intern belastningsutjämnare och konfigurera AlwaysOn-Tillgänglighetsgrupper.
    - Med den här på plats kan de migrera databasen
- Efter migreringen, kommer de att aktivera AlwaysOn skydd för databasen.

![Migreringsprocessen](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>Nödvändiga komponenter

Här är vad Contoso behöver för att utföra det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Azure-prenumeration** | Contoso har redan skapat en prenumeration i en tidig artikel i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Azure-infrastrukturen** | [Lär dig hur](contoso-migration-infrastructure.md) Contoso ställa in en Azure-infrastruktur.<br/><br/> Mer information om specifika [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) kraven för Site Recovery.
**Webbplatsåterställning (lokalt)** | Den lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värden.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Stöds [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguration.<br/><br/> Virtuella datorer du vill replikera måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).



## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso kommer att köras migreringen:

> [!div class="checklist"]
> * **Steg 1: Förbereda ett kluster**: Skapa ett kluster för att distribuera två noder för SQL Server-dator i Azure.
> * **Steg 2: Distribuera och konfigurera klustret**: Förbered en Azure SQL Server-kluster.  Databaser migreras i det här klustret som skapats i förväg.
> * **Steg 3: Distribuera belastningsutjämnaren**: Distribuera en belastningsutjämnare för att belastningsutjämna trafik till SQL Server-noder.
> * **Steg 4: Förbereda Azure Site Recovery**: Skapa ett Azure storage-konto för att lagra replikerade data och Recovery Services-valvet. 
> * **Steg 5: Förbereda lokala VMware för Site Recovery**: Förbereda konton för installation av VM-identifiering och agentinstallation. Förbereda lokala virtuella datorer så att användare kan ansluta till virtuella Azure-datorer efter m; migrering.
> * **Steg 6: Replikera virtuella datorer**: Aktivera replikering av virtuella datorer till Azure.
> * **Steg 7: Installera DMA**: Hämta och installera Database Migration Assistant.
> * **Steg 7: Migrera databas med DMA**: Migrera databasen till Azure.
> * **Steg 9: Skydda databasen**: Skapa en ständigt aktiverad tillgänglighetsgrupp för klustret.
> * **Steg 10: Migrera webbappen VM**: Kör ett redundanstest för att kontrollera att allt fungerar som förväntat. Kör en fullständig redundans till Azure. 


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>Steg 1: Förbereda en SQL Server AlwaysOn availability group klustret

Contoso-administratörer konfigurera klustret enligt följande:

1. De kan skapa två SQL Server-datorer genom att välja SQL Server 2017 Enterprise Windows Server 2016-avbildning i Azure Marketplace. 

    ![SQL VM-SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. I den **Skapa virtuell dator guiden** > **grunderna**, konfigureras:

    - Namn för de virtuella datorerna: **SQLAOG1** och **SQLAOG2**.
    - Eftersom datorer är verksamhetskritiska, aktivera de SSD för VM-disktyp.
    - De ange autentiseringsuppgifter för dator.
    - De distribuera de virtuella datorerna i den primära ÖSTRA USA 2 region i resursgruppen ContosoRG.

3. I **storlek**, de börjar med D2s_V3 SKU för båda virtuella datorerna. De kan skalas i senare som de behöver.
4. I **inställningar**, de gör du följande:

    - Eftersom dessa virtuella datorer är kritiska databaser för appen kan använda de hanterade diskar.
    - De placerar på datorer i produktionsnätverket för USA, ÖSTRA 2 primär region (**VNET-PROD-EUS2**), i databas-undernät (**PROD-DB-EUS2**).
    - De skapar en ny tillgänglighetsuppsättning: **SQLAOGAVSET**, med två feldomäner och fem uppdateringsdomäner.

      ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. I **SQL Server-inställningar**, SQL-anslutning till det virtuella nätverket (privat) att begränsa på standardporten 1433. För autentisering de använder samma autentiseringsuppgifter som de använder på plats (**contosoadmin**).

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**Behöver du mer hjälp?**

- [Få hjälp](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) etablera en SQL Server VM.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms) Konfigurera virtuella datorer för olika SQL Server SKU: er.

## <a name="step-2-deploy-and-set-up-the-cluster"></a>Steg 2: Distribuera och konfigurera klustret

Här är hur Contoso administratörer konfigurera klustret:

1. De har konfigurerat ett Azure storage-konto som fungerar som molnvittnet.
2. De lägger till SQL Server-datorer till Active Directory-domän i Contoso-datacenter på plats.
3. Klustret skapas i Azure.
4. De kan konfigurera molnvittnet.
5. Till sist gör SQL Always On-Tillgänglighetsgrupper.

### <a name="set-up-a-storage-account-as-cloud-witness"></a>Konfigurera ett lagringskonto som molnvittne

Om du vill konfigurera ett molnvittne behöver Contoso ett Azure Storage-konto som ska innehålla den blobfil som används för klustret skiljedomar. Samma lagringskonto kan användas för att ställa in molnvittne för flera kluster. 

Contoso-administratörer skapa ett lagringskonto på följande sätt:

1. De ange ett beskrivande namn för kontot (**contosocloudwitness**).
2. De distribuerar en allmän alla syften konto med LRS.
3. De placera kontot i en tredje region – södra centrala USA. De placeras utanför den primära och sekundära regionen så att det finns kvar vid regionala fel.
4. De placera den i deras resursgruppen som innehåller infrastrukturresurser - **ContosoInfraRG**.

    ![Molnvittne](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. När de skapar storage-konto, primära och sekundära åtkomstnycklar har genererats för den. De måste den primära åtkomstnyckeln för att skapa molnvittnet. Nyckeln visas under namnet på lagringskontot > **åtkomstnycklar**.

    ![Åtkomstnyckel](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>Lägg till SQL Server-datorer i Contoso-domänen

1. Contoso lägger till SQLAOG1 och SQLAOG2 till domänen contoso.com.
2. På varje virtuell dator installera de sedan Windows funktionen för redundanskluster och verktyg.

### <a name="set-up-the-cluster"></a>Konfigurera klustret

Innan du skapar klustret, ta en ögonblicksbild av OS-disken på varje dator i Contoso-administratörer.

![ögonblicksbild](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

1. Sedan kan de köra ett skript som de har lägger ihop för att skapa Windows-redundanskluster.

    ![Skapa kluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

2. När de har skapat klustret kan kontrollera de att de virtuella datorerna visas som klusternoder.

     ![Skapa kluster](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>Konfigurera molnvittnet

1. Contoso-administratörer konfigurera molnvittnet med hjälp av den **kvorum konfigurationsguiden** i hanteraren för redundanskluster.
2. I guiden väljer de för att skapa ett molnvittne med storage-konto.
3. När molnvittnet har konfigurerats visas i i snapin-modulen Klusterhanterare för växling vid fel.

    ![Molnvittne](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

### <a name="enable-sql-server-always-on-availability-groups"></a>Aktivera SQL Server Always On-Tillgänglighetsgrupper

Contoso-administratörer kan nu aktivera Always On:

1. Konfigurationshanteraren för SQL Server aktiveras **AlwaysOn Availability Groups** för den **SQL Server (MSSQLSERVER)** service.

    ![Aktivera AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. De starta om tjänsten för att ändringarna ska börja gälla.

Contoso kan med AlwaysOn-aktivera konfigurera AlwaysOn-tillgänglighetsgruppen som ska skyddas av SmartHotel360-databasen.

**Behöver du mer hjälp?**

- [Läs mer om](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) vittne och ställa in ett lagringskonto för den.
- [Instruktioner](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial) för att konfigurera ett kluster och skapa en tillgänglighetsgrupp.

## <a name="step-3-deploy-the-azure-load-balancer"></a>Steg 3: Distribuera Azure-belastningsutjämnaren

Contoso Administratörer vill nu distribuera en intern belastningsutjämnare som är placerad framför klusternoderna. Belastningsutjämnaren lyssnar efter trafik och leder till lämplig noden.

![Belastningsutjämning](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

De skapa belastningsutjämnaren på följande sätt:

1. I Azure portal > **nätverk** > **Load Balancer**, de har konfigurerat en ny intern belastningsutjämnare: **ILB-PROD-DB-EUS2-SQLAOG**.
2. De placera belastningsutjämnaren i produktionsnätverket **VNET-PROD-EUS2**, i databasen undernät **PROD-DB-EUS2**.
3. De tilldela den en statisk IP-adress: 10.245.40.100.
4. Som ett nätverk element de distribuerar belastningsutjämnaren i Nätverksresursgruppen **ContosoNetworkingRG**.

    ![Belastningsutjämning](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

När den interna belastningsutjämnaren har distribuerats, måste de du konfigurera den. De skapa en backend-adresspool, ställa in en hälsoavsökning och konfigurera en regel för belastningsutjämning.

### <a name="add-a-backend-pool"></a>Lägg till en serverdelspool

Contoso administratörer ställa in en backend-adresspool som innehåller IP-adresser för nätverkskort för virtuella datorer som ska ta emot nätverkstrafik från belastningsutjämnaren för att distribuera trafik till de virtuella datorerna i klustret.

1. Lägg till en serverdelspool i belastningsutjämningsinställningarna i portalen, Contoso: **ILB-PROD-DB-EUS-SQLAOG-BEPOOL**.
2. De associera poolen med tillgänglighetsuppsättning SQLAOGAVSET. De virtuella datorerna i uppsättningen (**SQLAOG1** och **SQLAOG2**) läggs till i poolen.

    ![Serverdelspool](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Contoso-administratörer skapa en hälsoavsökning så att belastningsutjämnaren kan övervaka appens hälsa. Avsökningen dynamiskt lägger till eller tar bort virtuella datorer från belastningsutjämnarens rotation, baserat på hur de svarar på hälsokontroller.

De skapar du avsökningen på följande sätt: 

1. I belastningsutjämningsinställningarna i portalen skapas Contoso en hälsoavsökning: **SQLAlwaysOnEndPointProbe**.
2. De anges om avsökningen för att övervaka virtuella datorer på TCP-port 59999.
3. De ange ett intervall på fem sekunder mellan avsökningar och ett tröskelvärde för 2. Om två avsökningar misslyckas, betraktas den virtuella datorn feltillstånd.

    ![Avsökningen](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>Konfigurera en belastningsutjämnare för att ta emot trafik


Contoso-administratörer kan nu ställa in en regel för belastningsutjämnaren att definiera hur trafiken ska distribueras till de virtuella datorerna.

- Frontend IP-adressen hanterar inkommande trafik.
- Backend-IP-pool som tar emot trafiken.

De skapa regeln enligt följande:

1. Lägg till en ny regel för belastningsutjämning i belastningsutjämningsinställningarna i portalen, de: **SQLAlwaysOnEndPointListener**.
2. De har en frontend lyssnare för att ta emot inkommande SQL-klienttrafik på TCP 1433.
3. De ange serverdelspoolen till vilken trafik som kommer att dirigeras och den port där virtuella datorer som lyssnar efter trafik.
4. De gör flytande IP (direkt serverreturnering). Detta krävs alltid för SQL AlwaysOn.

    ![Avsökningen](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**Behöver du mer hjälp?**

- [Få en översikt](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) för Azure Load Balancer.
- [Lär dig mer om](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal) skapa en belastningsutjämnare.



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>Steg 4: Förbered Azure för Site Recovery-tjänsten

Här följer Azure Contoso behöver du distribuerar Site Recovery-komponenterna:

- Ett virtuellt nätverk som virtuella datorer kommer att finnas när de skapar under redundansväxlingen.
- Ett Azure storage-konto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso administratörer konfigurera dessa enligt följande:

1.  Contoso som redan har skapat en nätverk/undernät som de kan använda för Site Recovery när de [distribuerade Azure-infrastrukturen](contoso-migration-rehost-vm-sql-ag.md).

    - SmartHotel360-app är en produktionsapp och WEBVM kommer att migreras till Azure-produktionsnätverket (VNET-PROD-EUS2) i den primära regionen för östra usa2.
    - WEBVM placeras i resursgruppen ContosoRG som används för produktionsresurser, och i produktion undernät (PROD-FE-EUS2).

2. Contoso-administratörer skapa ett Azure storage-konto (contosovmsacc20180528) i den primära regionen.

    - De använder ett allmänt konto med standardlagring och LRS-replikering.
    - Kontot måste vara i samma region som valvet.

      ![Site Recovery-lagring](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. Med nätverks- och storage-kontot på plats, de nu skapa ett Recovery Services-valv (**ContosoMigrationVault**), och placera den i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2 .

    ![Recovery Services-valv](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-5-prepare-on-premises-vmware-for-site-recovery"></a>Steg 5: Förbereda lokala VMware för Site Recovery

Här är vad Contoso-administratörer Förbered lokala:

- Ett konto på vCenter-servern eller vSphere ESXi-värden, att automatisera VM-identifiering.
- Ett konto som tillåter automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Lokala inställningarna för virtuella datorer så att Contoso kan ansluta till den replikerade virtuella Azure-datorn efter redundans.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. 
- Samordna replikering, redundans och återställning efter fel.
- Minst ett skrivskyddat konto krävs. Du behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso-administratörer som konfigurerar kontot enligt följande:

1. De kan skapa en roll på vCenter-nivå.
2. De tilldela sedan rollen behörigheterna som krävs.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på varje virtuell dator.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när replikering har aktiverats för den virtuella datorn.
- Du behöver ett konto som Site Recovery kan använda för att få åtkomst till den virtuella datorn för push-installation. Du anger det här kontot när du konfigurerar replikering i Azure-konsolen.
- Kontot kan vara domän eller lokal, med behörigheter för att installera på den virtuella datorn.




### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till virtuella Azure-datorer efter redundansväxling. Gör detta genom göra Contoso administratörer följande innan migreringen:

1. För åtkomst via internet de:

   - Aktiverar du RDP på den lokala virtuella datorn före redundans
   - Se till att TCP och UDP-regler lagts till för den **offentliga** profil.
   - Kontrollera att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
 
2. För åtkomst via plats-till-plats-VPN, de:

   - Aktiverar du RDP på den lokala datorn.
   - Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner**, för **domän och privat** nätverk.
   - Ange operativsystemets SAN-princip på den lokala virtuella datorn till **OnlineAll**.

Dessutom när de kör en redundans måste de du kontrollera följande:

- Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Om det finns inte användare att logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter en redundansväxling, kan de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs, och dessa [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="step-6-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Steg 6: Replikera lokala virtuella datorer till Azure med Site Recovery

Innan de kan köra en migrering till Azure, måste Contoso-administratörer att konfigurera och aktivera replikering.

### <a name="set-a-replication-goal"></a>Ange ett replikeringsmål

1. I valvet under valvnamnet (ContosoVMVault) de väljer ett replikeringsmål (**komma igång** > **Site Recovery** > **Förbered infrastruktur** .
2. De ange som finns lokalt, som körs på VMware och replikeras till Azure på sina datorer.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, de behöver för att bekräfta att de har slutfört distributionsplanering genom att välja **Ja, jag har gjort det**. I det här scenariot Contoso är bara migrera en virtuell dator och behöver inte distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Contoso-administratörer måste du konfigurera deras källkod-miljö. Detta gör de ladda ned en OVF-mall och använda den för att distribuera Site Recovery configuration server med hög tillgänglighet, en lokal VMware VM. När configuration server är igång, registrera den i valvet.

Konfigurationsservern körs ett antal komponenter:

- Konfigurationsserverkomponenten som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.

Contoso-administratörer utför dessa steg på följande sätt:


1. I valvet, de ladda ner OVF-mall från **förbereda infrastrukturen** > **källa** > **konfigurationsservern**.
    
    ![Ladda ner OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. De importerar mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar, de loggar in på den virtuella datorn som administratör. Vid första inloggning körs Azure Site Recovery-konfigurationsverktyget som standard.
5. I verktyget Ange de ett namn som ska användas för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats kan de logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. De loggar in på datorn igen och i guiden Konfigurera serverhantering startar automatiskt.
9. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
10. De Välj prenumeration, resursgrupp och valv där du vill registrera konfigurationsservern.
        ![Valvet](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. De och sedan hämta och installera MySQL-Server och VMWare PowerCLI. 
11. Efter valideringen kan ange de FQDN eller IP-adress för vCenter-servern eller vSphere-värden. De lämnar standardporten och anger ett eget namn för vCenter-servern.
12. De ange det konto som de har skapat för automatisk identifiering och de autentiseringsuppgifter som används för att automatiskt installera Mobilitetstjänsten. Kontot måste ha lokal administratörsbehörighet på de virtuella datorerna för Windows-datorer.

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. När registreringen är klar i Azure portal, de dubbla kontrollerar du att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso-administratörer kan nu ange Målinställningar för replikering.

1. I **Förbered infrastruktur** > **Target**, de väljer målinställningarna.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

Contoso-administratörer kan nu skapa en replikeringsprincip.

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: Standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**. Som standard på en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincip](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>Aktivera replikering

Contoso-administratörer kan nu börja replikera WebVM.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

    ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. Nu kan ange de Målinställningar, inklusive resursgruppen och virtuellt nätverk och lagringskontot som replikerade data ska lagras.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. De väljer WebVM för replikering, kontrollerar replikeringsprincipen och aktiverar replikering. Site Recovery installerar Mobilitetstjänsten på den virtuella datorn när replikering har aktiverats.
 
    ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. De spåra Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
5. I **Essentials** i Azure-portalen kan de se strukturen för de virtuella datorerna replikeras till Azure.

    ![Infrastruktur-vy](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**Behöver du mer hjälp?**

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Du kan läsa mer om [att aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-7-install-the-database-migration-assistant-dma"></a>Steg 7: Installera Database Migration Assistant (DMA)

Contoso-administratörer kommer att migrera SmartHotel360-databas till Azure VM **SQLAOG1** med hjälp av DMA. De har konfigurerat DMA på följande sätt:

1. De ladda ned verktyget från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) till en lokal SQL Server-VM (**SQLVM**).
2. De kör installationsprogrammet (DownloadMigrationAssistant.msi) på den virtuella datorn.
3. På den **Slutför** kan de välja **starta Microsoft Data Migration Assistant** innan du avslutar guiden.

## <a name="step-8-migrate-the-database-with-dma"></a>Steg 8: Migrera databas med DMA

1. I DMA de kör en ny migrering - **SmartHotel**.
2. De väljer den **Målservertyp** som **SQL Server på Azure Virtual Machines**. 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. Information om migrering de Lägg till i **SQLVM** som källservern och **SQLAOG1** som mål. De ange autentiseringsuppgifter för varje dator.

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. De skapar en lokal resurs för databas- och konfiguration. Den måste vara tillgänglig med skrivåtkomst av SQL-tjänstkontot på SQLVM och SQLAOG1.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso väljer inloggningar som ska migreras och startar migreringen. När den är klar visar DMA migreringen som lyckas.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. De kontrollerar att databasen körs på **SQLAOG1**.

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS ansluter till en lokal SQL Server-dator via en plats-till-plats VPN-anslutning mellan Contoso-datacenter och Azure och migrerar databasen.

## <a name="step-7-protect-the-database-with-alwayson"></a>Steg 7: Skydda databasen med AlwaysOn

Med app-databas som körs på **SQLAOG1**, Contoso-administratörer kan nu skydda den med hjälp av AlwaysOn-Tillgänglighetsgrupper. De konfigurerar AlwaysOn med hjälp av SQL Management Studio och sedan tilldela en lyssnare med hjälp av Windows-kluster. 

### <a name="create-an-alwayson-availability-group"></a>Skapa en AlwaysOn-tillgänglighetsgrupp

1. I SQL Management Studio och de högerklickar du på **alltid på hög tillgänglighet** att starta den **guiden Ny tillgänglighetsgrupp**.
2. I **ange alternativ för**, de namnge tillgänglighetsgruppen **SHAOG**. I **Välj databaser**, de väljer du SmartHotel360-databasen.

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. I **ange repliker**, de lägga till de två SQL-noderna som tillgänglighetsrepliker och konfigurera dem för att erbjuda automatisk redundans med synkront genomförande.

     ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. De konfigurerar en lyssnare för gruppen (**SHAOG**) och port. IP-adressen för den interna belastningsutjämnaren har lagts till som en statisk IP-adress (10.245.40.100).

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. I **Välj datasynkronisering**, aktiveras automatisk seeding. Med det här alternativet skapar SQL Server automatiskt de sekundära replikerna för varje databas i gruppen så Contoso inte behöver säkerhetskopiera och återställa dem manuellt. Efter valideringen kan skapas i tillgänglighetsgruppen.

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso kördes får problem när du skapar gruppen. De använder inte Active Directory Windows integrerad säkerhet och därför måste du bevilja behörighet till SQL-inloggningen för att skapa redundanskluster för Windows-roller.

    ![AlwaysOn-tillgänglighetsgrupp](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. När gruppen har skapats kan kan Contoso se dem i SQL Management Studio.

### <a name="configure-a-listener-on-the-cluster"></a>Konfigurera en lyssnare på klustret

Som ett sista steg i hur du konfigurerar SQL-distribuering Contoso administratörer konfigurera den interna belastningsutjämnaren som lyssnare på klustret och ger lyssnaren online. De kan använda ett skript för att göra detta.

![Kluster-lyssnare](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>Kontrollera konfigurationen

Med allt konfigurera har Contoso nu en fungerande tillgänglighetsgrupp i Azure som använder den migrerade databasen. Administratörer kontrollera detta genom att ansluta till den interna belastningsutjämnaren i SQL Management Studio.

![Ansluta ILB](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**Behöver du mer hjälp?**
- Läs om hur du skapar en [tillgänglighetsgruppen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group) och [lyssnare](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener).
- Manuellt [konfigurera klustret att använda IP-adress för load balancer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address).
- [Läs mer](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) om att skapa och med hjälp av SAS.


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>Steg 8: Migrera den virtuella datorn med Site Recovery

Contoso-administratörer som kör ett snabbt redundanstest och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Kör ett redundanstest hjälper dig att se till att allt fungerar som förväntat innan migreringen. 

1. Köra ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs för att kontrollera att alla de villkor som krävs för migrering är på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

3. När redundansen är klar visas repliken virtuell Azure-dator i Azure-portalen. De kontrollera att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs. 
4. När du har verifierat, rensa växling vid fel, de och registrera och sparar eventuella observationer. 

### <a name="run-a-failover"></a>Köra en redundansväxling

1. Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, Contoso administratörer skapa en återställningsplan för migrering och WEBVM läggas till i planen.

     ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. De kör en redundansväxling med planen. De väljer du den senaste återställningspunkten och ange att Site Recovery bör försöka att stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. Efter redundansen kan kontrollera de att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

    ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. När du har verifierat den virtuella datorn i Azure, slutföra de migreringen du slutför migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Som det sista steget i migreringsprocessen uppdatera Contoso administratörer anslutningssträngen för programmet som ska peka på den migrerade databasen som körs på SHAOG lyssnaren. Den här konfigurationen kommer att ändras på WEBVM nu körs i Azure.  Den här konfigurationen finns i filen web.config för ASP-program. 

1. Hitta filen vid C:\inetpub\SmartHotelWeb\web.config.  Ändra namnet på servern att återspegla det fullständiga Domännamnet för AOG: shaog.contoso.com.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. Starta om IIS på WEBVM när du uppdaterar filen och spara den, de. De gör detta med hjälp av IISRESET /RESTART från en kommandotolk.
2. När IIS har startats använder programmet den databas som körs på SQL-MI.


**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) som kör ett redundanstest. 
- [Lär dig](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxla till Azure.

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Efter migreringen SmartHotel360-appen körs på en virtuell Azure-dator och den SmartHotel360-databasen finns i Azure SQL-klustret.

Nu kan måste Contoso slutföra stegen rensning:  

- Ta bort de lokala virtuella datorerna från vCenter-lagret.
- Ta bort de virtuella datorerna från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation för att visa nya platser och IP-adresser för virtuella datorer.
- Granska eventuella resurser som interagerar med de inaktiverade virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.
- Lägg till de två nya virtuella datorerna (SQLAOG1 och SQLAOG2) ska läggas till produktion övervakningssystem.

## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

Contoso security-teamet granskar Azure virtuella datorer WEBVM, SQLAOG1 och SQLAOG2 för att fastställa eventuella säkerhetsproblem. 

- Teamet granskar Nätverkssäkerhetsgrupper (NSG) för den virtuella datorn för åtkomstkontroll. NSG: er för att säkerställa att endast trafik som tillåts att programmet kan skicka.
- Teamet överväger att skydda data på disken med hjälp av Azure Disk Encryption och KeyVault.
- Teamet ska utvärdera transparent datakryptering (TDE) och sedan aktivera det i SmartHotel360-databas som körs på den nya SQL AOG. [Läs mer](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017).

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) om säkerhetsrutiner för virtuella datorer.


## <a name="bcdr"></a>BCDR

 Contoso utförs följande åtgärder för affärskontinuitet och haveriberedskap (BCDR):
- Skydda data: Contoso säkerhetskopierar data på WEBVM, SQLAOG1 och SQLAOG2 virtuella datorer med Azure Backup-tjänsten. [Läs mer].
  (https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  - Contoso också lära dig om hur du använder Azure Storage för säkerhetskopiering av SQL Server direkt till blob storage. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-use-storage-sql-server-backup-restore).
  - Håll appar igång: Contoso replikerar appen virtuella datorer i Azure till en sekundär region med hjälp av Site Recovery. [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).


### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

1. Contoso har befintliga licenser för sina WEBVM och att utnyttja Azure Hybrid-förmånen.  Contoso konverterar befintliga virtuella Azure-datorer för att dra nytta av den här prissättning.
2. Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management. 

## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel360-app i Azure genom att migrera app-klientdelens VM till Azure med Site Recovery-tjänsten. Contoso migrerade app-databasen till en SQL Server-kluster som etablerats i Azure och skyddas i en SQL Server AlwaysOn-tillgänglighetsgrupp.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien som, vi visar hur Contoso appvärd sina service desk osTicket som körs på Linux och distribueras med en MySQL-databas.


