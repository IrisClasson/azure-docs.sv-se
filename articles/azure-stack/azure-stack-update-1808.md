---
title: Azure Stack 1808 Update | Microsoft Docs
description: Läs mer om nyheter i uppdateringen 1808 för integrerade Azure Stack-system, inklusive kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 37fb4c330004ce87afd900d9cafebb337261ec06
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568241"
---
# <a name="azure-stack-1808-update"></a>Uppdatering av Azure Stack 1808

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskriver innehållet i 1808 uppdateringspaketet. Uppdateringspaketet innehåller förbättringar, korrigeringar och kända problem för den här versionen av Azure Stack. Den här artikeln innehåller också en länk så att du kan ladda ned uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1808 update build-nummer är **1.1808.0.97**.  

### <a name="new-features"></a>Nya funktioner

Den här uppdateringen innehåller följande förbättringar för Azure Stack.

<!--  2682594   | IS  --> 
- **Alla miljöer i Azure Stack kan nu använda formatet tidszon Coordinated Universal Time (UTC).**  Alla loggdata och relaterad information nu visas i UTC-format. Om du uppdaterar från en tidigare version som inte har installerats med hjälp av UTC, uppdateras din miljö för att använda UTC. 

<!-- 2437250  | IS  ASDK --> 
- **Hanterade diskar stöds.** Du kan nu använda Managed Disks i Azure Stack virtuella datorer och VM-skalningsuppsättningar. Mer information finns i [Azure Stack Managed Disks: skillnader och överväganden](/azure/azure-stack/user/azure-stack-managed-disk-considerations).

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor**. Som Azure Monitor på Azure ger Azure Monitor på Azure Stack beroende på infrastruktur-mått och loggar för de flesta tjänster. Mer information finns i [Azure Monitor på Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

<!-- 2487932| IS --> 
- **Förbereda för tillägget värden**. Du kan använda tillägget värden för att säkra Azure Stack genom att minska antalet TCP/IP-portar som krävs. Med 1808-uppdateringen kan du förbereda, förbereda Azure Stack för tillägget värden. Mer information finns i [förbereda för tillägget för värd för Azure Stack](/azure/azure-stack/azure-stack-extension-host-prepare).

<!-- IS --> 
- **Galleriobjekt för Virtual Machine Scale Sets är nu inbyggda i**. Galleriobjektet Virtual Machine Scale Sets är nu tillgänglig i användar- och analytikerportaler utan att behöva ladda ned den.  Om du uppgraderar till 1808 är den tillgänglig vid slutförandet av uppgraderingen.  

<!-- IS, ASDK --> 
- **Virtual Machine Scale Sets skalning**. Du kan använda portalen för att [skala en VM-Skalningsuppsättning](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **Stöd för anpassade konfigurationer för IPSec/IKE-princip** för [VPN-gatewayer i Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

<!-- | IS ASDK--> 
- **Marketplace-objekt för Kubernetes**. Du kan nu distribuera Kubernetes-kluster med hjälp av den [Kubernetes Marketplace-objekt](azure-stack-solution-template-kubernetes-cluster-add.md). Användare kan det Kubernetes-objekt och fylla i några parametrar för att distribuera ett Kubernetes-kluster i Azure Stack. Syftet med mallarna är att göra det enkelt för användarna att ställa in Kubernetes-distributioner för utveckling/testning med några få steg.

<!-- | IS ASDK--> 
- **Blockchain mallar**. Du kan nu köra [Ethereum consortium distributioner](user/azure-stack-ethereum.md) på Azure Stack. Du kan hitta tre nya mallar i den [Azure Stack-snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates). De används att distribuera och konfigurera ett flera medlem Ethereum konsortienätverk med minimal kunskap om Azure och Ethereum. Syftet med mallarna är att göra det enkelt för användarna att ställa in utveckling/testning Blockchain distributioner med några få steg.

<!-- | IS ASDK--> 
- **API-version profilen 2017-03-09-profilen har uppdaterats till 2018-03-01-hybrid**. API-profiler ange Azure-resursprovidern och API-version för Azure REST-slutpunkter. Mer information om profiler finns i [hantera API-versionsprofiler i Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles).

### <a name="fixed-issues"></a>Åtgärdade problem

<!-- IS ASDK--> 
- Vi har åtgärdat problemet för att skapa en tillgänglighetsuppsättning i portalen som resulterade i gruppen med en feldomän och uppdateringsdomän 1. 

<!-- IS ASDK --> 
- Inställningar för att skala VM-skalningsuppsättningar är nu tillgängliga i portalen.  

<!-- 2494144- IS, ASDK --> 
- Problem som gjorde att vissa F-serien-storlekar för virtuella datorer från att visas när du väljer en VM-storlek för distribution är löst. 

<!-- IS, ASDK --> 
- Förbättringar av prestanda när du skapar virtuella datorer med mera optimerad användning av underliggande lagring.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack.


### <a name="changes"></a>Ändringar
<!-- 1697698  | IS, ASDK --> 
- *Snabbstartsguider* i användaren portalens instrumentpanel nu länken till artiklarna i onlinedokumentationen för Azure Stack.

<!-- 2515955   | IS ,ASDK--> 
- *Alla tjänster* ersätter *fler tjänster* i Azure Stack administratörs- och portaler. Du kan nu använda *alla tjänster* som ett alternativ till att navigera i Azure Stack-portaler på samma sätt som du gör i Azure-portaler.

<!-- TBD | IS, ASDK --> 
- *+ Skapa en resurs* ersätter *+ ny* i Azure Stack administratörs- och portaler.  Du kan nu använda *+ skapa en resurs* som ett alternativ till att navigera i Azure Stack-portaler på samma sätt som du gör i Azure-portaler.  

<!--  TBD – IS, ASDK --> 
- *Basic A* storlekar för virtuella datorer är inte längre tillgängligt för [skapar VM-skalningsuppsättningar](azure-stack-compute-add-scalesets.md) (VMSS) via portalen. Använd PowerShell eller en mall för att skapa en VMSS med den här storleken.  

### <a name="common-vulnerabilities-and-exposures"></a>Common Vulnerabilities and Exposures

Den här uppdateringen installeras följande uppdateringar:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Mer information om problemen klickar du på föregående länkarna eller finns i Microsoft Knowledge Base-artikeln [4343887](https://support.microsoft.com/help/4343887).

Den här uppdateringen innehåller även minskningen för spekulativ körning sida kanal säkerhetsproblem kallas L1 Terminal-fel (L1TF), som beskrivs i den [Microsoft Security Advisory ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Förutsättningar

- Installera Azure Stack [1807 uppdatera](azure-stack-update-1807.md) innan du installerar Azure Stack 1808 uppdateringen. 

- Installera den senaste tillgängliga [uppdatering eller snabbkorrigering för version 1807](azure-stack-update-1807.md#post-update-steps).  
  > [!TIP]  
  > Prenumerera på följande *RRS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- När du kör [Test AzureStack](azure-stack-diagnostic-test.md) efter 1808 uppdateringen visas ett varningsmeddelande från den Hanteringsstyrenheten för baskort (BMC). Du kan ignorera den här varningen.

<!-- 2468613 - IS --> 
- Under installationen av den här uppdateringen kan du se aviseringar med rubriken *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.

<!-- 2489559 - IS --> 
- Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).

<!-- 2830461 - IS --> 
- I vissa fall när en uppdatering kräver uppmärksamhet, kan motsvarande avisering inte skapas. Felaktig status visas fortfarande i portalen och påverkas inte.

### <a name="post-update-steps"></a>Steg efter uppdateringen

> [!Important]  
> Förbereda distributionen av Azure Stack för tillägget värden. Förbered datorn med hjälp av nedanstående procedur, [förbereda för tillägget för värd för Azure Stack](azure-stack-extension-host-prepare.md).

Installera alla tillämpliga snabbkorrigeringar efter installationen av uppdateringen. Visa mer information i följande artiklar i kunskapsbasen, samt våra [Servicing princip](azure-stack-servicing-policy.md). 
- [KB 4468920 – Azure Stack snabbkorrigering Azure Stack snabbkorrigering 1.1808.7.113](https://support.microsoft.com/help/4471992/)


## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Här följer efter installation kända problem för den här build-versionen.

### <a name="portal"></a>Portalen

- Den tekniska dokumentationen för Azure Stack fokuserar på den senaste versionen av Azure Stack. På grund av portalen ändringar mellan versioner vad som visas när du använder Azure Stack-portalerna kan skilja sig från vad som visas i dokumentationen. 

<!-- TBD - IS ASDK --> 
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, klickar du på **redigera instrumentpanelen**, högerklicka och välj **återställa till standardtillståndet**.

<!-- 2930718 - IS ASDK --> 
- I administratörsportalen kan få åtkomst till information om alla användarprenumeration efter stänga bladet och klicka på **senaste**, användarnamn för prenumeration visas inte.

<!-- 3060156 - IS ASDK --> 
- I både den administratörs- och portaler, klickar på portalinställningar och välja **ta bort alla inställningar och privata instrumentpaneler** fungerar inte som förväntat. En felmeddelandet visas. 

<!-- 2930799 - IS ASDK --> 
- I både den administratörs- och analytikerportaler under **alla tjänster**, tillgången **DDoS-skyddsplaner** felaktigt visas. Det finns faktiskt inte i Azure Stack. Om du försöker skapa den visas ett felmeddelande om att portalen inte kunde skapa marketplace-objekt. 

<!-- 2930820 - IS ASDK --> 
- Om du söker efter ”Docker”, i både den administratörs- och analytikerportaler är returnerade felaktigt objektet. Det finns faktiskt inte i Azure Stack. Om du försöker skapa den, visas ett blad med fel uppgift. 

<!-- 2967387 – IS, ASDK --> 
- Det konto som används för att logga in på Azure Stack-administratör eller användare portalen visas som **Oidentifierad användare**. Detta inträffar när kontot inte har antingen en *första* eller *senaste* namnen. Undvik problemet genom att redigera användarkontot om du vill använda den första eller sista. Du måste sedan logga ut och logga sedan in igen på portalen. 

<!--  2873083 - IS ASDK --> 
-  När du använder portalen för att skapa en virtuell datorskalning ange (VMSS), den *instansstorlek* listrutan inte in korrekt när du använder Internet Explorer. Undvik problemet genom att använda en annan webbläsare när du använder portalen för att skapa en VMSS.  

<!-- 2931230 – IS  ASDK --> 
- Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

<!--2760466 – IS  ASDK --> 
- När du installerar en ny Azure Stack-miljö med den här versionen, aviseringen-värde som anger *aktivering krävs* kanske inte visas. [Aktivering](azure-stack-registration.md) krävs innan du kan använda marketplace syndikering.  

<!-- TBD - IS ASDK --> 
- Två administrativa prenumerationstyper som introducerades med version 1804 ska inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **Standard Provider** prenumerationstyp.

<!-- TBD - IS ASDK --> 
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

<!-- TBD - IS ASDK --> 
- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.


### <a name="health-and-monitoring"></a>Hälsa och övervakning

<!-- TBD - IS -->
- Du kan se följande aviseringar visas flera gånger och sedan försvinner på Azure Stack-system:
   - *Infrastruktur-rollinstans som är inte tillgänglig*
   - *Skala enhet noden är offline*
   
  Kör den [Test AzureStack](azure-stack-diagnostic-test.md) cmdlet för att kontrollera hälsotillståndet för rollinstanser för infrastruktur och skala enhet noder. Om inga problem har identifierats av [Test-AzureStack](azure-stack-diagnostic-test.md), du kan ignorera dessa aviseringar. Om ett problem har identifierats, kan du försöker starta rollinstansen infrastruktur eller nod med hjälp av administrationsportalen eller PowerShell.

  Det här problemet löses i senast [1808 snabbkorrigering versionen](https://support.microsoft.com/help/4471992/), så var noga med att installera snabbkorrigeringen om du upplever problemet.

<!-- 1264761 - IS ASDK --> 
- Du kan se aviseringar för den **hälsotillstånd controller** komponent som har följande information:  

   Avisera #1:
   - NAMN: Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: varning
   - KOMPONENT: Health controller
   - Beskrivning: Kontrollanten hälsotillstånd pulsslag skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.  

  Avisera #2:
   - NAMN: Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: varning
   - KOMPONENT: Health controller
   - Beskrivning: Kontrollanten hälsotillstånd fel skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.

  Båda aviseringarna kan ignoreras och de stängs automatiskt över tid.  


<!-- 2812138 | IS --> 
- Du kan se en avisering för **Storage** komponent som innehåller följande information:

   - NAMN: Internt kommunikationsfel vid lagring  
   - ALLVARLIGHETSGRAD: kritisk  
   - KOMPONENT: Storage  
   - Beskrivning: Storage internt kommunikationsfel inträffade när begäranden skickas till följande noder.  

    Aviseringen kan ignoreras, men du måste stänga aviseringen manuellt.

<!-- 2368581 - IS. ASDK --> 
- Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en **fel vid skapande av Fabric VM**, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.


### <a name="compute"></a>Compute

<!-- 3164607 – IS, ASDK -->
- Återansluta en kopplas från disk till samma virtuella-dator (VM) med samma namn och LUN misslyckas med ett fel som **det går inte att koppla data disk 'datadisk' till virtuell dator ”vm1”**. Felet inträffar eftersom disken håller på att frånkopplas eller senaste frånkopplingen misslyckades. Vänta tills disken är helt frånkopplad och sedan försök igen eller ta bort/frånkoppla disken igen. Lösningen är att ansluta den igen med ett annat namn eller på en annan logisk enhet. 

<!-- 3099544 – IS, ASDK --> 
- När du skapar en ny virtuell dator med hjälp av Azure Stack-portalen och du väljer virtuella datorstorlek, kolumnen USD/månad visas med en **ej tillgänglig** meddelande. Den här kolumnen visas inte; Visar den virtuella datorn stöds prissättning kolumn inte i Azure Stack.

<!-- 3090289 – IS, ASDK --> 
- Efter att ha tillämpat 1808 uppdatera, du kan stöta på följande problem när du distribuerar virtuella datorer med hanterade diskar:

   1. Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
   2. Om du har konfigurerat en miljö med flera organisationer kan misslyckas distribuera virtuella datorer i en prenumeration som är associerade med en gästkatalogen med ett internt felmeddelande. Följ dessa steg för att lösa problemet:
      1. Tillämpa den [1808 Azure Stack snabbkorrigering](https://support.microsoft.com/help/4471992/).
      2. Följ stegen i [i den här artikeln](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.
      
<!-- 3179561 - IS --> 
- Managed Disks-användning rapporteras i timmar, enligt beskrivningen i den [vanliga frågor om användning i Azure Stack](azure-stack-usage-related-faq.md#managed-disks). Fakturering för Azure Stack används dock månadspriset i stället så att du felaktigt kan debiteras för Managed Disks-användning på eller före September 27. Vi har tillfälligt inaktiverats avgifterna för Managed Disks efter 27 September tills fakturering problemet åtgärdas. Om du har debiterats felaktigt för Managed Disks-användning, kontakta Microsoft Support för fakturering.
Användningsrapporter som genereras från API: er för Azure Stack-användning visa rätt antal och kan användas.

<!-- 2869209 – IS, ASDK --> 
- När du använder den [ **Lägg till AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan cmdleten misslyckas med följande fel: *tidskrävande åtgärden misslyckades med statusen ”misslyckades”*. 

<!--  2966665 – IS, ASDK --> 
- Koppla datadiskar som SSD till premium storlek managed disk virtuella datorer (DS, DSv2, Fs, Fs_V2) misslyckas med felmeddelandet: *gick inte att uppdatera diskar för den virtuella datorn 'vmname' fel: begärda åtgärden inte kan utföras eftersom lagringskontot typ ' Premium_LRS' stöds inte för VM-storleken ”Standard_DS/Ds_V2/FS/Fs_v2)*

   Undvik problemet genom att använda *Standard_LRS* datadiskar i stället för *Premium_LRS diskar*. Användning av *Standard_LRS* datadiskar ändras inte IOPs eller fakturering kostnaden. 

<!--  2795678 – IS, ASDK --> 
- När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. 

   Du kan ignorera varningen där det står: *du har valt att använda en standardisk med en storlek som har stöd för premiumdiskar. Detta kan påverka operativsystemets prestanda och rekommenderas inte. Överväg att använda premium storage (SSD) i stället.*

<!-- 2967447 - IS, ASDK --> 
- Virtuella datorns skalningsuppsättning (VMSS) skapa upplevelse ger 7.2 CentOS-baserade som ett alternativ för distribution. Välj en annan OS för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace av operatorn eftersom avbildningen inte är tillgänglig på Azure Stack.  

<!-- 2724873 - IS --> 
- När du använder PowerShell-cmdlets **Start AzsScaleUnitNode** eller **Stop-AzsScaleunitNode** för att hantera skalningsenheter, det första försöket att starta eller stoppa skalningsenheten kan misslyckas. Om cmdleten misslyckas på den första körningen, kör du cmdlet en gång. Den andra körningen bör fungera för att slutföra åtgärden. 

<!-- TBD - IS ASDK --> 
- När du skapar virtuella datorer på Azure Stack-användarportalen visar portalen ett felaktigt antal datadiskar som kan ansluta till virtuella datorer i DS-serien. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

<!-- TBD - IS ASDK --> 
- Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

<!-- 1662991 IS ASDK --> 
- Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  

<!-- 2724961- IS ASDK --> 
- När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverad, kan inte visa måttdata i diagrammet CPU-procent på översiktssidan för virtuell dator.

   För att hitta diagrammet CPU-procent för den virtuella datorn, gå till den **mått** gästen mått bladet och visa alla Windows-VM som stöds.



### <a name="networking"></a>Nätverk  

<!-- 1766332 - IS ASDK --> 
- Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

<!-- 16309153 - IS ASDK --> 
- I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

<!-- 2702741 -  IS ASDK --> 
- Offentliga IP-adresser som distribueras med hjälp av dynamisk fördelning garanteras att bevaras när en frigörandet har utfärdats.

<!-- 2529607 - IS ASDK --> 
- Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

<!-- 2664148 - IS ASDK --> 
- De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

<!-- 2489178 - IS ASDK --> 
- För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.



### <a name="usage"></a>Användning  
<!-- TBD - IS ASDK --> 
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Hämta uppdateringen
Du kan ladda ned Azure Stack 1808 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Nästa steg
- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).  
