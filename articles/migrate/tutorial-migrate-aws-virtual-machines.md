---
title: Identifiera, utvärdera och migrera Amazon Web Services (AWS) EC2 virtuella datorer till Azure
description: I den här artikeln beskrivs hur du migrerar virtuella AWS-datorer till Azure med Azure Migrate.
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: MVC
ms.openlocfilehash: 9aad6993af4a90acb41316da0056da84f2e95f70
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066652"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Upptäck, utvärdera och migrera virtuella AWS-datorer (Amazon Web Services) till Azure

Den här självstudien visar hur du identifierar, utvärderar och migrerar Amazon Web Services virtuella datorer (VM) till virtuella Azure-datorer med hjälp av Azure Migrate: Server utvärdering och Migreringsverktyg för Server

> [!NOTE]
> När du migrerar dina virtuella AWS-datorer till Azure behandlas de virtuella datorerna som om de var fysiska servrar. Du använder Server migrations flödet för migrering av fysiska datorer för att migrera dina virtuella AWS-datorer till Azure.

I den här självstudien får du lära dig hur man:
> [!div class="checklist"]
> * Verifiera krav för migrering.
> * Förbered Azure-resurser med Azure Migrate: Server-migrering. Konfigurera behörigheter för ditt Azure-konto och resurser för att arbeta med Azure Migrate.
> * Förbered AWS EC2-instanser för migrering.
> * Lägg till verktyget Azure Migrate: Migreringsverktyg i Azure Migrate Hub.
> * Konfigurera replikerings enheten och distribuera konfigurations servern.
> * Installera mobilitets tjänsten på virtuella AWS-datorer som du vill migrera.
> * Aktivera replikering för virtuella datorer.
> * Spåra och övervaka replikeringsstatus. 
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="discover-and-assess-aws-vms"></a>Identifiera och utvärdera virtuella AWS-datorer  

Innan du migrerar till Azure rekommenderar vi att du utför en utvärdering av VM-identifiering och migrering. Den här utvärderingen hjälper till att anpassa dina virtuella AWS-datorer för migrering till Azure och beräkna möjliga kostnader för Azure-körning.

Konfigurera en utvärdering på följande sätt:

1. En utvärdering kan utföras genom att behandla dina virtuella AWS-datorer som fysiska datorer i syfte att utföra en utvärdering med hjälp av verktyget Azure Migrate: Server bedömning. Följ [själv studie kursen](./tutorial-prepare-physical.md) för att konfigurera Azure och förbereda dina virtuella AWS-datorer för en utvärdering.
2. Följ sedan den här [självstudien](./tutorial-assess-physical.md) för att skapa en Azure Migrate-projekt och-apparat för att identifiera och utvärdera dina virtuella AWS-datorer.

Även om vi rekommenderar att du testar en utvärdering är inte ett obligatoriskt steg att utföra en utvärdering för att kunna migrera virtuella datorer.

## <a name="migrate-aws-vms"></a>Migrera virtuella AWS-datorer   

## <a name="1-prerequisites-for-migration"></a>1. krav för migrering

- Se till att de virtuella AWS-datorerna som du vill migrera kör en operativ system version som stöds. Virtuella AWS-datorer behandlas som fysiska datorer för migreringen. Granska de [operativ system som stöds](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för migreringen av den fysiska servern. Vi rekommenderar att du utför en testmigrering (redundanstest) för att kontrol lera om den virtuella datorn fungerar som förväntat innan du fortsätter med den faktiska migreringen.
- Se till att dina virtuella AWS-datorer uppfyller de [konfigurationer som stöds](./migrate-support-matrix-physical-migration.md#physical-server-requirements) för migrering till Azure.
- Kontrol lera att de virtuella AWS-datorerna som du replikerar till Azure uppfyller [kraven för virtuella Azure](./migrate-support-matrix-physical-migration.md#azure-vm-requirements) -datorer.
- Vissa ändringar krävs på de virtuella datorerna innan du migrerar dem till Azure.
    - För vissa operativ system gör Azure Migrate dessa ändringar automatiskt.
    - Det är viktigt att du gör dessa ändringar innan du påbörjar migrering. Om du migrerar den virtuella datorn innan du gör ändringen kanske den virtuella datorn inte startar i Azure.
Granska de [Windows](prepare-for-migration.md#windows-machines) -och [Linux](prepare-for-migration.md#linux-machines) -ändringar du behöver göra.

## <a name="2-prepare-azure-resources-for-migration"></a>2. Förbered Azure-resurser för migrering

Förbered Azure för migrering med Azure Migrate: Migreringsverktyg för Server.

**Aktivitet** | **Information**
--- | ---
**Skapa ett Azure Migrate-projekt** | Ditt Azure-konto måste ha Contributes eller ägar behörigheter för att skapa ett projekt.
**Verifiera behörigheter för ditt Azure-konto** | Ditt Azure-konto måste ha behörighet att skapa en virtuell dator och skriva till en Azure-hanterad disk.

### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-azure-account-permissions"></a>Tilldela behörigheter för Azure-konto

Tilldela Azure-kontot rollen virtuell dator deltagare. Detta ger behörighet att:

- Skapa en virtuell dator i den valda resursgruppen.
- Skapa en virtuell dator i det valda virtuella nätverket.
- Skriv till en Azure-hanterad disk. 

### <a name="create-an-azure-network"></a>Skapa ett Azure-nätverk

[Konfigurera](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ett virtuellt Azure-nätverk (VNet). När du replikerar till Azure är de virtuella Azure-datorerna som skapas anslutna till det virtuella Azure-nätverket som du anger när du konfigurerar migrering.

## <a name="3-prepare-aws-instances-for-migration"></a>3. Förbered AWS-instanser för migrering

För att förbereda för AWS till Azure-migrering måste du förbereda och distribuera en replikeringsprincip för migrering.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Förbereda en dator för replikerings enheten

Azure Migrate: Server-migreringen använder en replikeringsfil för att replikera datorer till Azure. Replikeringssystemet kör följande komponenter.

- **Konfigurations**Server: konfigurations servern samordnar kommunikationen mellan AWS-miljön och Azure och hanterar datareplikering.
- **Processerver**: processervern fungerar som en gateway för replikering. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till ett cache Storage-konto i Azure.

Förbered distribution av installationer enligt följande:

- Konfigurera en separat virtuell EC2-dator för att vara värd för replikerings enheten. Den här instansen måste köra Windows Server 2012 R2 eller Windows Server 2016. [Granska](./migrate-replication-appliance.md#appliance-requirements) maskin vara, program vara och nätverks krav för enheten.
- Installationen bör inte installeras på en virtuell käll dator som du vill replikera eller på Azure Migrate identifierings-och utvärderings installation som du kan ha installerat tidigare. Den bör distribueras på en annan virtuell dator.
- De virtuella datorer som ska migreras måste ha en nätverks rad syn för AWS. Konfigurera de säkerhets grupps regler som krävs för att aktivera detta. Det rekommenderas att du distribuerar replikeringen i samma VPC som de virtuella käll datorerna som ska migreras. Om replikerings enheten måste finnas i en annan VPC måste VPCs vara ansluten via VPC-peering.
- Den virtuella AWS-datorns virtuella datorer kommunicerar med replikeringssystemet på portarna HTTPS 443 (kontroll av kanal dirigering) och TCP 9443 (data transport) inkommande för hantering av replikering och data överföring för replikering. Replikeringssystemet i vänder sig till att dirigera och skicka replikeringsdata till Azure via port HTTPS 443 utgående. Om du vill konfigurera de här reglerna redigerar du reglerna för inkommande/utgående säkerhets grupp med lämpliga portar och käll-IP-information.

   ![AWS säkerhets grupper ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Redigera säkerhets inställningar ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- Replikerings enheten använder MySQL. Granska [alternativen](migrate-replication-appliance.md#mysql-installation) för att installera MySQL på-enheten.
- Granska de Azure-URL: er som krävs för att replikeringssystemet ska kunna komma åt [offentliga](migrate-replication-appliance.md#url-access) och [offentliga](migrate-replication-appliance.md#azure-government-url-access) moln.

## <a name="4-add-the-server-migration-tool"></a>4. Lägg till Migreringsverktyg för Server

Konfigurera ett Azure Migrate projekt och Lägg sedan till Migreringsverktyg för server i det.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **identifiera, utvärdera och migrera servrar**klickar du på **utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
6. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
7. I **Projektinformation** anger du projektnamnet och geografin där du vill skapa projektet och klickar på **Nästa**. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).
    - Projektets geografi används bara för att lagra metadata som samlats in från AWS-datorer.
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. I **Välj utvärderingsverktyg** väljer du **Hoppa över att lägga till ett utvärderingsverktyg just nu** > **Nästa**.
9. I **Välj migreringsverktyg** väljer du  **Azure Migrate: Servermigrering** > **Nästa**.
10. I **Review + add tools** (Granska + lägg till verktyg)
granskar du inställningarna och klickar på **Lägg till verktyg**
11. När du har lagt till verktyget visas det i Azure Migrate för Project > **Server**-  >  **Migreringsverktyg**.

## <a name="5-set-up-the-replication-appliance"></a>5. Konfigurera replikerings enheten

Det första steget i migreringen är att konfigurera replikerings enheten. Om du vill konfigurera installations programmet för AWS VM-migrering måste du hämta installations filen för installationen och sedan köra den på den [virtuella datorn som du för beredde](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Ladda ned installations programmet för replikerings enheten

1. I **Azure Migrate: Server-migrering**i Azure Migrate Project >- **servrar**klickar du på **identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **inte virtualiserad/annan**.
3. I **mål region**väljer du den Azure-region som du vill migrera datorerna till.
4. Välj **Bekräfta att mål regionen för migrering är <region namn>**.
5. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery valv i bakgrunden.
    - Om du redan har konfigurerat migrering med Azure Migrate Server-migreringen kan du inte konfigurera mål alternativet eftersom resurserna tidigare har kon figurer ATS.
    - Du kan inte ändra mål region för projektet när du har klickat på den här knappen.
    - Om du vill migrera dina virtuella datorer till en annan region måste du skapa ett nytt/annat Azure Migrate-projekt.

6. I vill **du installera en ny replikeringsprincip? väljer du** **installera en replikeringsprincip**.
7. I **Hämta och installera installations programmet för replikering**laddar du ned installations programmet för installationen och registrerings nyckeln. Du behöver nyckeln för att kunna registrera installationen. Nyckeln är giltig i fem dagar efter att den har laddats ned.

    ![Hämta Provider](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Kopiera installations filen och nyckel filen för installations programmet till Windows Server 2016 eller Windows Server 2012 AWS VM som du skapade för replikeringen.
9. Kör installations filen för replikeringstjänsten enligt beskrivningen i nästa procedur.  
    9.1. I **Innan du börjar** väljer du **Installera konfigurerationsservern och processervern** och väljer sedan **Nästa**.   
    9,2 i **program vara från tredje part**väljer **du jag accepterar licens avtalet från tredje part**och väljer sedan **Nästa**.   
    9,3 under **registrering**väljer du **Bläddra**och går sedan till den plats där du vill placera valv registrerings nyckel filen. Välj **Nästa**.  
    9,4 i **Internet inställningar**väljer **du Anslut till Azure Site Recovery utan proxyserver**och väljer sedan **Nästa**.  
    9,5 kontroll sidan för **krav kontroll** körs söker efter flera objekt. När den är klar väljer du **Nästa**.  
    9,6 i **MySQL-konfiguration**anger du ett lösen ord för MySQL db och väljer sedan **Nästa**.  
    9,7 i **miljö information**väljer du **Nej**. Du behöver inte skydda dina virtuella datorer. Välj sedan **Nästa**.  
    9,8 på **installations plats**väljer du **Nästa** för att acceptera standardvärdet.  
    9,9 i **Val av nätverk**väljer du **Nästa** för att acceptera standardvärdet.  
    9,10 i **Sammanfattning**väljer du **Installera**.   
    9,11 **installations förloppet** visar information om installations processen. När den är klar väljer du **Avsluta**. Ett fönster visar ett meddelande om en omstart. Välj **OK**.   
    9,12 härnäst visas ett meddelande om anslutnings lösen frasen för konfigurations servern. Kopiera lösen frasen till Urklipp och spara lösen frasen i en tillfällig textfil på de virtuella käll datorerna. Du behöver den här lösen frasen senare under installationen av mobilitets tjänsten.
10. När installationen är klar startas konfigurations guiden för enheten automatiskt (du kan också starta guiden manuellt genom att använda cspsconfigtool-genvägen som skapas på Skriv bordet på enheten). Använd fliken Hantera konton i guiden för att lägga till konto information som ska användas för push-installation av mobilitets tjänsten. I den här självstudien kommer vi att manuellt installera mobilitets tjänsten på virtuella käll datorer som ska replikeras, så skapa ett dummy-konto i det här steget och fortsätt. Du kan ange följande information för att skapa vår dummy-konto – "gäst" som eget namn, "username" som användar namn och "lösen ord" som lösen ord för kontot. Du kommer att använda det här dummy-kontot i steget aktivera replikering. 
11. När installationen har startats om efter installationen går du till **identifiera datorer**, väljer den nya installationen i **Välj konfigurations Server**och klickar på **Slutför registrering**. Genom att slutföra registreringen utförs några slutliga uppgifter för att förbereda replikeringen.

    ![Slutför registrering](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="6-install-the-mobility-service"></a>6. Installera mobilitets tjänsten

En mobilitets tjänst agent måste vara installerad på de virtuella AWS-datorer som ska migreras. Agent installationerna är tillgängliga på replikerings enheten. Du hittar rätt installations program och installerar agenten på varje dator som du vill migrera. Gör så här:

1. Logga in på replikerings enheten.
2. Navigera till **%programdata%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Hitta installations programmet för käll AWS VM-operativsystem och version. Granska [operativ system som stöds](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Kopiera installations filen till den virtuella AWS-dator som du vill migrera.
5. Kontrol lera att du har den sparade lösen Frass text filen som skapades när du installerade replikeringen.
    - Om du har glömt att spara lösen frasen kan du Visa lösen frasen på den replikerade enheten med det här steget. Från kommando raden kör **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** för att visa den aktuella lösen frasen.
    - Kopiera nu den här lösen frasen till Urklipp och spara den i en tillfällig textfil på de virtuella käll datorerna.

### <a name="installation-guide-for-windows-aws-vms"></a>Installations guide för virtuella Windows AWS-datorer

1. Extrahera innehållet i installations filen till en lokal mapp (till exempel C:\Temp) på den virtuella datorn AWS enligt följande:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Kör installations programmet för mobilitets tjänsten:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Registrera agenten med replikerings enheten:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Installations guide för virtuella Linux AWS-datorer

1. Extrahera innehållet i installations tarball till en lokal mapp (till exempel/tmp/MobSvcInstaller) på den virtuella datorn AWS enligt följande:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Kör installations skriptet:
    ```
    sudo ./install -r MS -q
    ```  

3. Registrera agenten med replikerings enheten:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="7-enable-replication-for-aws-vms"></a>7. Aktivera replikering för virtuella AWS-datorer

> [!NOTE]
> Via portalen kan du lägga till upp till 10 virtuella datorer för replikering samtidigt. Om du vill replikera fler virtuella datorer samtidigt kan du lägga till dem i batchar med 10.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering**klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. I **Replikera**, > **käll inställningar**  >  **att datorerna har virtualiserats?** väljer du **inte virtualiserad/övrigt**.
3. I **lokal**installation väljer du namnet på Azure Migrate-installationen som du konfigurerar.
4. I **processerver**väljer du namnet på replikerings enheten. 
5. I **autentiseringsuppgifter för gäst**väljer du det dummy-konto som skapades tidigare under [installationen av installations programmet för replikering](#download-the-replication-appliance-installer) för att installera mobilitets tjänsten manuellt (push-installation stöds inte). Klicka sedan på **Nästa: virtuella datorer**.   
 
    ![Replikera virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. I **Virtual Machines**, i **Importera migreringsjobb från en utvärdering?**, lämnar du standardinställningen **Nej, jag anger inställningarna för migrering manuellt**.
7. Markera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.

    ![Välj virtuella datorer](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
9. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
10. I **Azure Hybrid-förmån**:
    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

    ![Mål inställningar](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

11. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-storlek**: Azure Migrate Server-migreringen väljer som standard en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**.
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets uppsättning**: om den virtuella datorn ska finnas i en Azure-tillgänglighets uppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

    ![Beräknings inställningar](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

12. I **diskar**anger du om de virtuella dator diskarna ska replikeras till Azure och väljer disk typ (standard SSD/HDD eller Premium Managed Disks) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

    ![Disk inställningar](./media/tutorial-migrate-physical-virtual-machines/disks.png)

13. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar varje tid innan replikeringen startar, **Hantera**  >  **replikering av datorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.

## <a name="8-track-and-monitor-replication-status"></a>8. spåra och övervaka replikeringsstatus

- När du klickar på **Replikera** startar du jobbet starta replikering.
- När jobbet starta replikeringen har slutförts börjar de virtuella datorerna med den inledande replikeringen till Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar i AWS VM-diskar replikeras regelbundet till replik diskarna i Azure.

Du kan spåra jobb status i Portal meddelanden.

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.  

![Övervaka replikering](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="9-run-a-test-migration"></a>9. kör en testmigrering

När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Test migreringen rekommenderas starkt och ger en möjlighet att identifiera eventuella problem och åtgärda dem innan du fortsätter med den faktiska migreringen. Vi rekommenderar att du gör detta minst en gång för varje virtuell dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att påverka de virtuella AWS-datorerna, som förblir operativa och fortsätter att replikera.
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:

1. I Server för **migrerings mål**  >  **Servers**  >  **Azure Migrate: Server migrering**klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="10-migrate-aws-vms"></a>10. migrera virtuella AWS-datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de virtuella AWS-datorerna.

1. I Azure Migrate Project >- **servrar**  >  **Azure Migrate: Server-migrering**klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **migrera**  >  **Stäng virtuella datorer och utför en planerad migrering utan data förlust**väljer du **Ja**  >  **OK**.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**.
4. Ett migreringsjobb startas för den virtuella datorn. Du kan visa jobbets status genom att klicka på ikonen för meddelande klocka längst upp till höger på Portal sidan eller genom att gå till sidan jobb i Migreringsverktyg för Server (klicka på Översikt på verktygs panelen > välja jobb på den vänstra menyn).
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan Virtual Machines.

### <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppa migreringen**. Det här gör följande:
    - Stoppar replikering för den virtuella AWS-datorn.
    - Tar bort den virtuella datorn AWS från antalet **replikerade servrar** i Azure Migrate: Server-migrering.
    - Rensar statusinformation om replikering för den virtuella datorn.
2. Installera [Linux](../virtual-machines/extensions/agent-linux.md) -agenten på de migrerade datorerna. Windows-agenten för Azure VM är förinstallerad under migreringsprocessen.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa inkommande trafik åtkomst med [Azure Security Center – just-in-Time-administration](../security-center/security-center-just-in-time.md).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
    - Distribuera [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) för att övervaka användning och utgifter.

## <a name="next-steps"></a>Nästa steg

Undersök [resan för migrering i molnet](/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud adoption Framework.

## <a name="troubleshooting--tips"></a>Fel sökning/tips

**Fråga:** Jag kan inte se min AWS VM i listan över identifierade servrar för migrering   
**Svar:** Kontrol lera om din replikeringsprincip uppfyller kraven. Kontrol lera att mobilitets agenten är installerad på den virtuella käll datorn som ska migreras och är registrerad på konfigurations servern. Kontrol lera nätverks inställningen och brand Väggs reglerna för att aktivera en nätverks Sök väg mellan replikeringstjänsten och käll AWS virtuella datorer.  

**Fråga:** Hur gör jag för att veta om den virtuella datorn har migrerats   
**Svar:** Efter migreringen kan du Visa och hantera den virtuella datorn från sidan Virtual Machines. Anslut till den migrerade virtuella datorn för att verifiera.  

**Fråga:** Jag kan inte importera virtuella datorer för migrering från mina tidigare skapade Server utvärderings resultat   
**Svar:** Vi stöder för närvarande inte import av utvärdering för det här arbets flödet. Som en lösning kan du exportera utvärderingen och sedan manuellt välja den virtuella dator rekommendationen under steget aktivera replikering.
  
**Fråga:** Jag får felet "Det gick inte att hämta BIOS-GUID" vid försök att identifiera mina virtuella AWS-datorer   
**Svar:** Granska operativ system som stöds för virtuella AWS-datorer.  

**Fråga:** Min replikeringsstatus fortlöper inte    
**Svar:** Kontrol lera om din replikeringsprincip uppfyller kraven. Kontrol lera att du har aktiverat de portar som krävs på replikerings enheten TCP-port 9443 och HTTPS 443 för data transport. Se till att det inte finns några inaktuella dubbletter av Replikerings enheten som är ansluten till samma projekt.   

**Fråga:** Jag kan inte identifiera AWS-instanser med Azure Migrate på grund av HTTP-statuskod 504 från fjärrhanterings tjänsten för Windows    
**Svar:** Se till att granska kraven för Azure Migrate-installationen och URL-åtkomst behoven. Kontrol lera att inga proxyinställningar blockerar installationen av enheten.   
