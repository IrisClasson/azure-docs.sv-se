---
title: Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery | Microsoft Docs
description: Lär dig att konfigurera haveriberedskap i Azure för lokala virtuella VMware-datorer med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 3/3/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ccd62c0b0832622bbc74542674c1d09f59ea301b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57848838"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer

[Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.


I den här självstudien visar vi hur du konfigurerar och aktiverar replikering för en virtuell VMware-dator till Azure med Azure Site Recovery. Självstudier är utformade för att visa dig hur du distribuerar Site Recovery med grundläggande inställningar. De använder den enklaste sökvägen och visar inte alla alternativ. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ange replikeringskälla och mål.
> * Konfigurera källmiljön för replikering, inklusive lokala Azure Site Recovery-komponenter och målmiljön för replikeringen.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar är det bra att:

- [Granska arkitekturen](vmware-azure-architecture.md) för detta haveriberedskapsscenario.
- Om du vill lära dig om att ställa in haveriberedskap för virtuella VMware-datorer i detalj kan du granska och använda följande resurser:
    - [Läs vanliga frågor](vmware-azure-common-questions.md) om haveriberedskap för VMware.
    - [Läs](vmware-physical-azure-support-matrix.md) vad som stöds och krävs för VMware.
-  Läs våra **instruktionsguider** för detaljerade anvisningar som omfattar alla distributionsalternativ för VMware:
    - Konfigurera [replikeringskällan](vmware-azure-set-up-source.md) och [konfigurationsservern](vmware-azure-deploy-configuration-server.md).
    - Konfigurera [replikeringsmålet](vmware-azure-set-up-target.md).
    - Konfigurera en [replikeringsprincip](vmware-azure-set-up-replication.md) och [aktivera replikering](vmware-azure-enable-replication.md).
- I den här självstudien visar vi dig hur du replikerar en enda virtuell dator. Om du distribuerar flera virtuella datorer bör du använda den [verktyget Distributionshanteraren](https://aka.ms/asr-deployment-planner) för att planera distributionen. [Läs mer](site-recovery-deployment-planner.md) om det här verktyget.

Och granska dessa tips:
- I den här självstudien används en OVA-mall för att skapa konfigurationsserverns virtuella VMware-dator. Om du inte kan göra detta, följer du [dessa instructins](physical-manage-configuration-server.md) du ställer in konfigurationsservern manuellt.
- I den här självstudien hämtar och installerar Site Recovery MySQL till konfigurationsservern. Om du vill kan konfigurera du det manuellt i stället. [Läs mer](vmware-azure-deploy-configuration-server.md#configure-settings).
  >Du kan ladda ned den senaste versionen av konfigurationsservermallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
  Licensnumret du fått med OVF-mall är en utvärderingslicens som är giltig i 180 dagar. Windows som körs på den virtuella datorn måste aktiveras med licensen som krävs. 



## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

1. Välj valvets namn i **Recovery Services-valv**. I det här scenariot använder vi namnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.



## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

I källmiljön behöver du en fristående, med hög tillgänglighet, en lokal dator till värd lokala Site Recovery-komponenter. Komponenterna är konfigurationsservern, processervern och huvudmålservern:

- Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- Processervern fungerar som en replikeringsgateway. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar den till cachen storage-konto i Azure. Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.

Om du vill konfigurera konfigurationsservern som en virtuell VMware-dator med hög tillgänglighet laddar du ned en förberedd Open Virtualization Application-mall (OVA) och importerar mallen till VMware för att skapa den virtuella datorn. När du har konfigurerat konfigurationsservern kan du registrera den i valvet. Site Recovery identifierar lokala virtuella VMware-datorer efter registreringen.




### <a name="download-the-vm-template"></a>Ladda ned VM-mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ner OVF-mallen för konfigurationsservern.



## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware


1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden **Distribuera OVF-mall**. 

     ![OVF-mall](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. På **Välj källa** anger du platsen för den nedladdade OVF:en.
4. På **Detaljer för recensionen** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. På **Klar att slutföras** väljer du, om du vill konfigurera den virtuella datorn med standardinställningarna, **Slå på strömmen efter distributionen** > **Slutför**.

   > [!TIP]
   > Om du vill lägga till ytterligare ett NIC avmarkerar du alternativet för att **slå på strömmen efter distributionen** > **Slutför**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till ett extra nätverkskort i konfigurationsservern gör du det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Välj **Nästa** > **Slutför**. Välj sedan **OK**.


## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget inom några sekunder.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in på din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.

### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurera inställningar och lägga till VMware-servern

1. I guiden Konfigurera serverhantering, väljer **Ställ in anslutning**. Först väljer du det nätverkskort som inbyggd processervern som används för identifiering och push-installation av mobilitetstjänsten på källdatorer listrutor, och sedan väljer du det nätverkskort som konfigurationsservern använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valv** väljer du din Azure-prenumeration samt relevant resursgrupp och valv.
3. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server. Om du har placerat MySQL i sökvägen ignoreras det här steget.
4. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
5. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
7. Ange de uppgifter för användarautentisering som ska användas av konfigurationsservern för att ansluta till VMware-servern. Kontrollera att användarnamn och lösenord är korrekta och att de ingår i gruppen Administratörer på den virtuella datorn som ska skyddas. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till** och välj sedan **Fortsätt**.
8. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du det användarnamn och lösenord som ska användas för att automatiskt installera mobilitetstjänsten på virtuella datorer, när replikering har aktiverats.
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
9. Välj **Slutför konfigurationen** för att slutföra registreringen.
10. När registreringen är klar i Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på sidan **Källa** i valvet. Välj sedan **OK** för att konfigurera målinställningarna.


Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Välj **Förbered infrastrukturen** > **Mål**. Ange den prenumeration som du vill använda. Vi använder en Resource Manager-modell.
2. Site Recovery kontrollerar att du har en eller flera virtuella nätverk. Du bör ha dessa när du konfigurerar Azure komponenterna i den [första självstudien](tutorial-prepare-azure.md) i den här serien med självstudier.

   ![Fliken Mål](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Öppna [Azure Portal](https://portal.azure.com) och välj **Alla resurser**.
2. Välj Recovery Services-valvet (**ContosoVMVault** i den här självstudien).
3. Om du vill skapa en replikeringsprincip väljer du **Site Recovery-infrastruktur** > **Replikeringsprinciper** > **+Replikeringsprincip**.
4. I **Skapa replikeringsprincip** anger du principnamnet. Vi använder **VMwareRepPolicy**.
5. I **Tröskelvärde för återställningspunktmål** använder du standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I **Återställningspunkt för kvarhållning** anger du hur länge varje återställningspunkt finns kvar. I den här självstudien använder vi 72 timmar. Replikerade virtuella datorer kan återställas till valfri punkt i ett kvarhållningsfönster.
7. I **Frekvens för programkonsekventa ögonblicksbilder** anger du hur ofta programkonsekventa ögonblicksbilder skapas. Vi använder standardinställningen 60 minuter. Välj **OK** för att skapa principen.

   ![Skapa replikeringsprincip](./media/vmware-azure-tutorial/replication-policy.png)

- Principen associeras automatiskt med konfigurationsservern.
- Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-policy** är failback-principen **rep-policy-failback**. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering kan utföras på följande sätt:

1. Välj **Replikera program** > **Källa**.
1. I **Källa**väljer du **Lokalt** och väljer konfigurationsservern i **källplats**.
1. I **Datortyp** väljer du **Virtual Machines**.
1. I **vCenter/vSphere Hypervisor** väljer du vSphere-värden eller den vCenter-server som hanterar värden.
1. Välj processerver (installeras som standard på konfigurationsserverns virtuella dator). Välj sedan **OK**.
1. I **Mål** väljer du den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Vi använder Resource Manager-distributionsmodellen. 
1. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
1. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla virtuella datorer som du aktiverat replikering för. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
1. I **Virtual Machines** > **Välj virtuella datorer** väljer du de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj sedan **OK**. Om du inte kan visa /välja en viss virtuell dator klickar du [här](https://aka.ms/doc-plugin-VM-not-showing) för att lösa problemet.
1. I **Egenskaper** > **Konfigurera egenskaper** väljer du det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
1. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts.
1. Välj **Aktivera replikering**. Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator.
1. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
1. Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
1. Om du vill övervaka de virtuella datorer som du lägger till, kan du se när de senast identifierades i **Konfigurationsservrar** > **Senaste kontakt**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> När du har aktiverat replikering, [kör ett programåterställningstest](site-recovery-test-failover-to-azure.md) att se till att allt fungerar som förväntat.
