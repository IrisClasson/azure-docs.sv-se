---
title: Återställa SAP HANA databaser på virtuella Azure-datorer
description: I den här artikeln får du lära dig hur du återställer SAP HANA databaser som körs på Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c62ea68683355fc703a5258e6e5fa0f3795f7e34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503599"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Återställa SAP HANA databaser på virtuella Azure-datorer

Den här artikeln beskriver hur du återställer SAP HANA databaser som körs på en virtuell Azure-dator (VM), som Azure Backup-tjänsten har säkerhetskopierat till ett Recovery Services-valv. Återställningar kan användas för att skapa kopior av data för utvecklings-och test scenarier eller för att återgå till ett tidigare tillstånd.

Mer information om hur du säkerhetskopierar SAP HANA databaser finns i [säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Återställa till en tidpunkt eller till en återställnings punkt

Azure Backup kan återställa SAP HANA databaser som körs på virtuella Azure-datorer på följande sätt:

* Återställ till ett visst datum eller en angiven tidpunkt (till den andra) med hjälp av logg säkerhets kopior. Azure Backup identifierar automatiskt lämpliga fullständiga, differentiella säkerhets kopior och kedja av logg säkerhets kopior som krävs för att återställa baserat på den valda tiden.

* Återställ till en viss fullständig eller differentiell säkerhets kopia för att återställa till en viss återställnings punkt.

## <a name="prerequisites"></a>Förutsättningar

Observera följande innan du återställer en databas:

* Du kan bara återställa databasen till en SAP HANA-instans i samma region

* Mål instansen måste registreras med samma valv som källan

* Azure Backup kan inte identifiera två olika SAP HANAs instanser på samma virtuella dator. Det går inte att återställa data från en instans till en annan på samma virtuella dator.

* Kontrol lera statusen för **säkerhets kopierings beredskap** för att säkerställa att mål SAP HANAs instansen är redo för återställning:

  1. Öppna valvet där mål SAP HANAs instansen är registrerad

  1. På instrument panelen för valvet, under **komma igång**, väljer du **säkerhets kopiering**

      ![Säkerhets kopiering i valvets instrument panel](media/sap-hana-db-restore/getting-started-backup.png)

  1. Under **säkerhets kopiering**under **vad vill du säkerhetskopiera? väljer du** **SAP HANA i virtuell Azure-dator**

      ![Välj SAP HANA i virtuell Azure-dator](media/sap-hana-db-restore/sap-hana-backup.png)

  1. Klicka på **Visa information** under **identifiera databaser i virtuella datorer**

      ![Visa detaljer](media/sap-hana-db-restore/view-details.png)

  1. Granska **beredskap för säkerhets kopieringen** för den virtuella mål datorn

      ![Skyddade servrar](media/sap-hana-db-restore/protected-servers.png)

* Mer information om de återställnings typer som SAP HANA stöder finns i SAP HANA anmärkning [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Återställ en databas

För att återställa måste du ha följande behörigheter:

* Behörighet för **säkerhets kopierings ansvarig** i valvet där du utför återställningen.
* **Deltagar** åtkomst till den virtuella käll datorn som säkerhets kopie ras.
* **Deltagar**åtkomst till den virtuella mål datorn:
  * Om du återställer till samma virtuella dator är det här den virtuella käll datorn.
  * Om du återställer till en annan plats är detta den nya virtuella mål datorn.

1. Öppna valvet där SAP HANA databas som ska återställas har registrerats

1. Välj **säkerhets kopierings objekt** under **skyddade objekt**på instrument panelen för valvet

    ![Säkerhetskopiera objekt](media/sap-hana-db-restore/backup-items.png)

1. Under **säkerhets kopierings objekt**, under **säkerhets kopierings hanterings typ** väljer du **SAP HANA i Azure VM**

    ![Typ av säkerhets kopierings hantering](media/sap-hana-db-restore/backup-management-type.png)

1. Välj den databas som ska återställas

    ![Databas som ska återställas](media/sap-hana-db-restore/database-to-restore.png)

1. Granska menyn databas. Den innehåller information om säkerhets kopiering av databasen, inklusive:

    * De äldsta och senaste återställnings punkterna

    * Status för logg säkerhets kopiering under de senaste 24 och 72 timmarna för databasen

    ![Menyn databas](media/sap-hana-db-restore/database-menu.png)

1. Välj **Återställ databas**

1. Under **Återställ konfiguration**anger du var (eller hur) du vill återställa data:

    * **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga käll databasen.

    * **Skriv över DB**: Återställ data till samma SAP HANA instans som den ursprungliga källan. Med det här alternativet skrivs den ursprungliga databasen över.

      ![Återställ konfiguration](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Återställ till en annan plats

1. I menyn **Återställ konfiguration** , under **återställnings**läge, väljer du **alternativ plats**.

    ![Återställ till en annan plats](media/sap-hana-db-restore/restore-alternate-location.png)

1. Välj det SAP HANA värddator namn och instans namn som du vill återställa databasen till.
1. Kontrol lera om mål SAP HANAs instansen är redo för återställning genom att se till att **säkerhets kopieringen** är klar. Mer information finns i [avsnittet krav](#prerequisites) .
1. I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.

    > [!NOTE]
    > Återställningar av Enkel databas container (SDC) måste följa dessa [kontroller](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

1. Om det är tillämpligt väljer du **Skriv över om databasen med samma namn redan finns på den valda Hana-instansen**.
1. Välj **OK**.

    ![Återställa konfiguration – slutlig skärm](media/sap-hana-db-restore/restore-configuration-last.png)

1. I **Välj återställnings punkt**väljer du **loggar (tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller Välj **fullständig & differentiell** för att [återställa till en viss återställnings punkt](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Återställ och skriv över

1. I menyn **Återställ konfiguration** , under **återställningen**, väljer du **Skriv över DB**  >  **OK**.

    ![Åsidosätt databas](media/sap-hana-db-restore/overwrite-db.png)

1. I **Välj återställnings punkt**väljer du **loggar (tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller Välj **fullständig & differentiell** för att [återställa till en viss återställnings punkt](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Återställ som filer

Om du vill återställa säkerhets kopierings data som filer i stället för en databas väljer du **Återställ som filer**. När filerna har dumpas till en angiven sökväg kan du ta dessa filer till alla SAP HANA datorer där du vill återställa dem som en databas. Eftersom du kan flytta filerna till vilken dator som helst kan du nu återställa data mellan prenumerationer och regioner.

1. I menyn **Återställ konfiguration** , under **var och hur du ska återställa**, väljer du **Återställ som filer**.
1. Välj det **värdnamn** /Hana-servernamn som du vill återställa säkerhetskopieringsfilerna till.
1. I **mål Sök vägen på servern**anger du mappsökvägen på den server som du valde i steg 2. Detta är den plats där tjänsten kommer att dumpa alla nödvändiga säkerhetskopieringsfiler.

    De filer som har dumpas är:

    * Säkerhetskopieringsfiler för databas
    * Katalogfiler
    * JSON-metadatafiler (för varje säkerhets kopierings fil som ingår)

    Normalt möjliggör en nätverks resurs Sök väg eller sökväg till en monterad Azure-filresurs när den anges som mål Sök väg, vilket ger enklare åtkomst till dessa filer av andra datorer i samma nätverk eller med samma Azure-filresurs som är monterad på dem.

    >[!NOTE]
    >Om du vill återställa säkerhets kopian av databasen på en Azure-filresurs som är monterad på den virtuella mål datorn, kontrollerar du att rot kontot har Läs-/Skriv behörighet för Azure-filresursen.

    ![Välj mål Sök väg](media/sap-hana-db-restore/restore-as-files.png)

1. Välj den **återställnings punkt** som motsvarar alla säkerhetskopieringsfiler och mappar som ska återställas.

    ![Välj återställnings punkt](media/sap-hana-db-restore/select-restore-point.png)

1. Alla säkerhetskopierade filer som är associerade med den valda återställnings punkten dumpas till mål Sök vägen.
1. Baserat på vilken typ av återställnings punkt som valts (**tidpunkt** eller **fullständig & differentiell**) visas en eller flera mappar som skapats i mål Sök vägen. En av mapparna `Data_<date and time of restore>` innehåller fullständiga och differentiella säkerhets kopior och den andra mappen `Log` innehåller logg säkerhets kopiorna.
1. Flytta de återställda filerna till den SAP HANA server där du vill återställa dem som en databas.
1. Följ sedan de här stegen:
    1. Ange behörigheter för mappen/katalogen där de säkerhetskopierade filerna lagras med följande kommando:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Kör nästa uppsättning kommandon som`<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Generera katalog filen för återställning. Extrahera **BackupId** från JSON-metadatafilen för fullständig säkerhets kopiering, vilket kommer att användas senare i återställnings åtgärden. Se till att fullständiga säkerhets kopior och logg säkerhets kopior finns i olika mappar och ta bort katalogfiler och JSON-metadatafiler i dessa mappar.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        I ovanstående kommando:

        * `<DataFileDir>`– mappen som innehåller fullständiga säkerhets kopior
        * `<LogFilesDir>`– mappen som innehåller säkerhets kopiorna av loggen
        * `<PathToPlaceCatalogFile>`– mappen där katalog filen som genereras måste placeras

    1. Återställ med den nyligen genererade katalog filen via HANA Studio eller kör HDBSQL Restore Query med den här nyligen genererade katalogen. HDBSQL-frågor visas nedan:

    * Så här återställer du till en tidpunkt:

        Om du skapar en ny återställd databas kör du kommandot HDBSQL för att skapa en ny databas `<DatabaseName>` och stoppar sedan databasen för återställning. Men om du bara återställer en befintlig databas kan du stoppa databasen genom att köra kommandot HDBSQL.

        Kör sedan följande kommando för att återställa databasen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>`– Namnet på den nya databasen eller en befintlig databas som du vill återställa
        * `<Timestamp>`-Exakt tidsstämpel för tidpunkten för återställning
        * `<DatabaseName@HostName>`– Namnet på den databas vars säkerhets kopia används för återställning och namnet på den **värd** /SAP HANA server som databasen finns på. `USING SOURCE <DatabaseName@HostName>`Alternativet anger att data säkerhets kopieringen (som används för återställning) är av en databas med ett annat sid eller namn än mål SAP HANA datorn. Det behöver därför inte anges för återställningar som har utförts på samma HANA-server som säkerhets kopieringen görs från.
        * `<PathToGeneratedCatalogInStep3>`-Sökväg till katalog filen som genererades i **Steg C**
        * `<DataFileDir>`– mappen som innehåller fullständiga säkerhets kopior
        * `<LogFilesDir>`– mappen som innehåller säkerhets kopiorna av loggen
        * `<BackupIdFromJsonFile>`– **BackupId** extraherades i **Steg C**

    * Återställa till en viss fullständig eller differentiell säkerhets kopia:

        Om du skapar en ny återställd databas kör du kommandot HDBSQL för att skapa en ny databas `<DatabaseName>` och stoppar sedan databasen för återställning. Om du bara återställer en befintlig databas ska du dock köra kommandot HDBSQL för att stoppa databasen:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>`– namnet på den nya databasen eller den befintliga databasen som du vill återställa
        * `<Timestamp>`– den exakta tidsstämpeln för tidpunkten i återställningen
        * `<DatabaseName@HostName>`– namnet på den databas vars säkerhets kopia används för återställning och namnet på den **värd** /SAP HANA server som databasen finns på. `USING SOURCE <DatabaseName@HostName>`Alternativet anger att data säkerhets kopieringen (som används för återställning) är av en databas med ett annat sid eller namn än mål SAP HANA datorn. Så det behöver inte anges för återställningar som har utförts på samma HANA-server som säkerhets kopieringen görs från.
        * `<PathToGeneratedCatalogInStep3>`– sökvägen till katalog filen som genererades i **Steg C**
        * `<DataFileDir>`– mappen som innehåller fullständiga säkerhets kopior
        * `<LogFilesDir>`– mappen som innehåller säkerhets kopiorna av loggen
        * `<BackupIdFromJsonFile>`– **BackupId** extraherades i **Steg C**

### <a name="restore-to-a-specific-point-in-time"></a>Återställ till en viss tidpunkt

Om du har valt **loggar (tidpunkt)** som återställnings typ gör du följande:

1. Välj en återställnings punkt i logg diagrammet och välj sedan **OK** för att välja återställnings punkten.

    ![Återställningspunkt](media/sap-hana-db-restore/restore-point.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.

    ![Välj Återställ](media/sap-hana-db-restore/restore-restore.png)

1. Spåra återställnings förloppet i **aviserings** området eller spåra den genom att välja **återställnings jobb** på menyn databas.

    ![Återställningen har utlösts](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Återställa till en viss återställnings punkt

Om du har valt **fullständig & differentiell** som återställnings typ gör du följande:

1. Välj en återställnings punkt i listan och välj sedan **OK** för att välja återställnings punkten.

    ![Återställa en viss återställnings punkt](media/sap-hana-db-restore/specific-recovery-point.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.

    ![Starta återställnings jobb](media/sap-hana-db-restore/restore-specific.png)

1. Spåra återställnings förloppet i **aviserings** området eller spåra den genom att välja **återställnings jobb** på menyn databas.

    ![Återställnings förlopp](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > I MDC (Multiple Database container) återställs när system databasen har återställts till en mål instansen måste en köra skriptet för för registrering igen. Det går bara att återställa efterföljande klient databas återställningar. Mer information hittar du i [fel sökning – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur](sap-hana-db-manage.md) du hanterar SAP HANA databaser som har säkerhetskopierats med Azure Backup
