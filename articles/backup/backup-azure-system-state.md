---
title: Säkerhetskopiera Windows system State till Azure
description: Lär dig att säkerhetskopiera system tillstånd för Windows Server och/eller Windows-datorer till Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 4089815f8f76d9868f8fa56f8b2eab3de89541d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "82128176"
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Säkerhetskopiera Windows system State i Resource Manager-distribution

Den här artikeln förklarar hur du säkerhetskopierar ditt Windows Server-systemtillstånd till Azure. Det är avsett att vägleda dig genom grunderna.

Om du vill veta mer om Azure Backup läser du den här [översikten](backup-overview.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) som ger dig åtkomst till Azure-tjänsten.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Om du vill säkerhetskopiera ditt system tillstånd för Windows Server måste du skapa ett Recovery Services-valv i den region där du vill lagra data. Du måste också bestämma hur du vill att lagringen ska replikeras.

### <a name="to-create-a-recovery-services-vault"></a>Så här skapar du ett Recovery Services-valv

1. Logga in på [Azure Portal](https://portal.azure.com/) med din Azure-prenumeration om du inte redan gjort det.
2. På navigeringsmenyn klickar du på **Alla tjänster** och skriver **Recovery Services** i listan över resurser och klickar sedan på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-system-state/open-rs-vault-list.png)

    Om det finns Recovery Services-valv i prenumerationen visas valven.
3. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

    ![Skapa Recovery Services-valv (steg 2)](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Bladet Recovery Services-valv öppnas och du uppmanas att ange **namn**, **prenumeration**, **resursgrupp** och **plats**.

    ![Skapa Recovery Services-valv (steg 3)](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. I **Namn** anger du ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Skriv ett namn som innehåller mellan 2 och 50 tecken. Det måste börja med en bokstav och får endast innehålla bokstäver, siffror och bindestreck.

5. I avsnittet **Prenumeration** använder du listrutan för att välja Azure-prenumerationen. Om du bara använder en prenumeration visas den och du kan gå vidare till nästa steg. Om du inte är säker på vilken prenumeration du ska använda använder du standardprenumerationen (eller den föreslagna). Du kan bara välja mellan flera alternativ om ditt organisationskonto är associerat med flera Azure-prenumerationer.

6. Gör följande i avsnittet **Resursgrupp**:

    * Välj **Skapa ny** om du vill skapa en resursgrupp.
    Eller
    * Välj **Använd befintlig** och klicka på listrutan om du vill se listan över tillgängliga resursgrupper.

   Fullständig information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md).

7. Klicka på **Plats** för att välja en geografisk region för valvet. Det här alternativet anger den geografiska region som dina säkerhetskopierade data skickas till.

8. Längst ned på bladet för Recovery Services-valvet klickar du på **Skapa**.

    Det kan ta flera minuter innan Recovery Services-valvet har skapats. Övervaka statusmeddelandena uppe till höger i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv efter ett par minuter klickar du på **Uppdatera**.

    ![Klicka på Uppdatera](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    När du ser valvet i listan över Recovery Services-valv kan du ange lagringsredundansen.

### <a name="set-storage-redundancy-for-the-vault"></a>Ange lagringsredundans för valvet

När du skapar ett Recovery Services-valv ska du alltid kontrollera att lagringsredundansen är konfigurerad på det sätt som du vill.

1. På bladet **Recovery Services-valv** klickar du på det nya valvet.

    ![Välj det nya valvet i listan över Recovery Services-valv](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Om du väljer valvet minimeras bladet **Recovery Services-valv** och bladet Inställningar (*som har namnet på valvet överst*) och bladet med valvinformation öppnas.

    ![Visa lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. På det nya valvets inställningsblad använder du det lodräta reglaget och bläddrar ned till avsnittet Hantera. Där klickar du på **Infrastruktur för säkerhetskopiering**.
    Bladet Infrastruktur för säkerhetskopiering öppnas.
3. På bladet Infrastruktur för säkerhetskopiering klickar du på **Konfiguration av säkerhetskopiering** för att öppna bladet **Konfiguration av säkerhetskopiering**.

    ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Välj lämpligt alternativ för lagringsreplikering för valvet.

    ![alternativ för lagringskonfiguration](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Valvet använder geo-redundant lagring som standard. Om du använder Azure som primär slutpunkt för lagring av säkerhetskopior fortsätter du att använda **geo-redundant** lagring. Om du inte använder Azure som en slutpunkt för primär lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) i denna [översikt av lagringsredundans](../storage/common/storage-redundancy.md).

Nu när du har skapat ett valv konfigurerar du det för att säkerhetskopiera Windows system tillstånd.

## <a name="configure-the-vault"></a>Konfigurera valvet

1. På bladet Recovery Services-valv (för valvet som du precis har skapat) klickar du på **Säkerhetskopiering** i avsnittet Komma igång och väljer sedan **Säkerhetskopieringsmål** på bladet **Kom igång med säkerhetskopiering**.

    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Bladet **Säkerhetskopieringsmål** öppnas.

    ![Öppna bladet för säkerhetskopieringsmål](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Från listrutan **Var körs din arbetsbelastning?** väljer du **Lokalt**.

    Du väljer **Lokalt** eftersom din Windows Server- eller Windows-dator är en fysisk dator som inte finns i Azure.

3. Från menyn **vad vill du säkerhetskopiera? väljer du** **system tillstånd**och klickar på **OK**.

    ![Konfigurera filer och mappar](./media/backup-azure-system-state/backup-goal-system-state.png)

    När du klickar på OK visas en markering bredvid **Säkerhetskopieringsmål** och bladet **Förbered infrastruktur** öppnas.

    ![När säkerhetskopieringsmålet har konfigurerats går du vidare och förbereder infrastrukturen](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. På bladet **Förbered infrastruktur** klickar du på **Ladda ned agent för Windows Server eller Windows Client**.

    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Om du använder Windows Server Essential väljer du att hämta agenten för Windows Server Essential. En popup-meny visas och du uppmanas att köra eller spara MARSAgentInstaller.exe.

    ![Dialogrutan MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. På snabbmenyn för hämtningen klickar du på **Spara**.

    Som standard sparas filen **MARSagentinstaller.exe** i mappen för nedladdningar. När installationsprogrammet har slutförts visas ett popup-fönster och du tillfrågas om du vill köra installationsprogrammet eller öppna mappen.

    ![förbereda infrastrukturen](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Du behöver inte installera agenten än. Du kan installera agenten när du har hämtat valvautentiseringsuppgifterna.

6. Klicka på **Ladda ned** på bladet **Förbered infrastruktur**.

    ![hämta autentiseringsuppgifter för valvet](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Autentiseringsuppgifterna för valvet hämtas till mappen Hämtningsbara filer. När autentiseringsuppgifterna för valvet har hämtats visas ett popup-fönster och du tillfrågas om du vill öppna eller spara autentiseringsuppgifterna. Klicka på **Spara**. Om du råkar klicka på **Öppna** av misstag väntar du tills dialogrutan som försöker öppna autentiseringsuppgifterna för valvet misslyckas. Du kan inte öppna valvautentiseringsuppgifterna. Gå vidare till nästa steg. Valvautentiseringsuppgifterna finns i mappen Hämtade filer.

    ![valvautentiseringsuppgifterna har hämtats](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Autentiseringsuppgifterna för valvet måste sparas på en plats som är lokal på den Windows Server där du tänker använda agenten.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

> [!NOTE]
> Aktivering av säkerhetskopiering via Azure Portal är inte tillgängligt ännu. Använd Microsoft Azure Recovery Services-agenten för att säkerhetskopiera Windows Server System tillstånd.
>

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** från mappen för nedladdade filer (eller en annan lagringsplats).

    Installationsprogrammet visar ett antal meddelanden när Recovery Services-agenten extraheras, installeras och registreras.

    ![Autentiseringsuppgifter för att köra Recovery Services-agentinstallationsprogrammet](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Slutför installationsguiden för Microsoft Azure Recovery Services Agent. För att slutföra guiden måste du:

   * Välja en plats för installationen och cachelagringsmappen.
   * Ange information om proxyservern om du använder en proxyserver för att ansluta till Internet.
   * Ange ditt användarnamn och lösenord om du använder en autentiserad proxyserver.
   * Ange autentiseringsuppgifterna som du hämtat för valvet.
   * Spara krypteringslösenfrasen på en säker plats.

     > [!NOTE]
     > Om du tappar bort eller glömmer lösenfrasen kan Microsoft inte hjälpa dig att återställa dina säkerhetskopierade data. Spara filen på en säker plats. Det krävs för att återställa en säkerhetskopia.
     >
     >

Nu installeras agenten och datorn registreras i valvet. Nu kan du konfigurera och schemalägga säkerhetskopieringen.

## <a name="back-up-windows-server-system-state"></a>Säkerhetskopiera Windows Server System-tillstånd

Den första säkerhets kopieringen innehåller två uppgifter:

* Schemalägg säkerhetskopieringen
* Säkerhetskopiera system tillstånd för första gången

För att slutföra den första säkerhetskopieringen använder du Microsoft Azure Recovery Services-agenten.

> [!NOTE]
> Du kan säkerhetskopiera system tillstånd på Windows Server 2008 R2 via Windows Server 2016. System tillstånds säkerhets kopiering stöds inte på klient-SKU: er. System tillstånd visas inte som ett alternativ för Windows-klienter eller Windows Server 2008 SP2-datorer.
>
>

### <a name="to-schedule-the-backup-job"></a>Så här schemalägger du säkerhetskopieringsjobbet

1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Starta Azure Recovery Services-agenten](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Klicka på Recovery Services-agenten och sedan på **Schemalägg säkerhetskopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Klicka på **Nästa** på sidan Komma igång i guiden Schemalägg säkerhetskopiering.

4. På sidan Välj objekt som ska säkerhetskopieras klickar du på **Lägg till objekt**.

5. Välj **system tillstånd** och klicka sedan på **OK**.

6. Klicka på **Nästa**.

7. Välj säkerhets kopierings frekvens och bevarande princip för säkerhets kopiering av system tillstånd på efterföljande sidor.

8. Läs informationen på sidan Confirmation (Bekräftelse) och klicka sedan på **Finish** (Slutför).

9. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Säkerhetskopiera Windows Server System State för första gången

1. Se till att det inte finns några väntande uppdateringar för Windows Server som kräver en omstart.

2. I Recovery Services-agenten klickar du på **Säkerhetskopiera nu** för att slutföra en inledande seeding över nätverket.

    ![Säkerhetskopiera Windows Server nu](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Välj **system tillstånd** på skärmen **Välj säkerhets kopierings objekt** som visas och klicka på **Nästa**.

4. Gå igenom inställningarna på sidan Bekräftelse som guiden Säkerhetskopiera nu ska använda för att säkerhetskopiera datorn. Klicka på **Säkerhetskopiera**.

5. Stäng guiden genom att klicka på **Stäng**. Om du stänger guiden innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.
    > [!NOTE]
    > MARS-agenten utlöser SFC-/verifyonly som en del av förincheckningarna före varje säkerhets kopiering av system tillstånd. Detta görs för att säkerställa att filer som säkerhets kopie ras som en del av system tillstånd har rätt versioner som motsvarar Windows-versionen. Läs mer om system fils Checker (SFC) i [den här artikeln](https://docs.microsoft.com/windows-server/administration/windows-commands/sfc).
    >

När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i säkerhetskopieringskonsolen.

  ![IR slutfört](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Har du några frågor?

Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Nästa steg

* Få mer information om hur du [säkerhetskopierar Windows-datorer](backup-windows-with-mars-agent.md).
* Nu när du har säkerhetskopierat system tillstånd för Windows Server kan du [Hantera dina valv och servrar](backup-azure-manage-windows-server.md).
* Om du behöver återställa en säkerhetskopia använder du den här artikeln för att [återställa filer till en Windows-dator](backup-azure-restore-windows-server.md).
