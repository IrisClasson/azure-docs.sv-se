---
title: Förfina en utvärdering av grupp med beroendemappning för gruppen i Azure Migrate | Microsoft Docs
description: Beskriver hur du förfina en utvärdering med beroendemappning för gruppen i Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/25/2018
ms.author: raynew
ms.openlocfilehash: d79513569b53d2b8958f44fbfac3839c91caaff8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634310"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Förfina grupper med beroendemappning för grupp

Den här artikeln beskriver hur du förfina grupper genom att visualisera beroenden för alla datorer i gruppen. Du använder vanligtvis den här metoden när du vill begränsa medlemskap för en befintlig grupp efter dubbelkontrollera gruppberoenden innan du kör en utvärdering. Förfina en grupp med hjälp av beroendevisualisering kan hjälpa dig att effektivt planera din migrering till Azure. Du kan identifiera alla beroende av varandra system som behöver för att migrera tillsammans. Det hjälper dig att se till att inget kvar och förvåning avbrott sker inte när du migrerar till Azure.


> [!NOTE]
> Grupper som du vill visualisera beroenden får inte innehålla fler än 10 datorer. Om du har fler än 10 datorer i gruppen, rekommenderar vi att du dela upp den i mindre grupper att utnyttja beroendevisualiseringsfunktionen.


## <a name="prepare-for-dependency-visualization"></a>Förbereda för visualisering av beroenden
Azure Migrate använder Tjänstkarta-lösningen i Log Analytics för att aktivera beroendevisualisering av datorer.

### <a name="associate-a-log-analytics-workspace"></a>Associera en Log Analytics-arbetsyta
Om du vill använda visualisering av beroenden, måste du koppla en Log Analytics-arbetsyta, ny eller befintlig, med ett Azure Migrate-projekt. Du kan bara skapa eller koppla en arbetsyta i samma prenumeration där migration-projekt skapas.

- Att koppla en Log Analytics-arbetsyta till ett projekt i **översikt**går du till **Essentials** delen av projektet klickar du på **kräver konfiguration**

    ![Associera arbetsytan för Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- När du skapar en ny arbetsyta kan behöva du ange ett namn för arbetsytan. Arbetsytan skapas i samma prenumeration som migration-projekt och i en region i samma [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) som migration-projekt.
- Den **Använd befintlig** alternativet visas endast arbetsytor som skapas i regioner där Tjänstkarta är tillgänglig. Om du har en arbetsyta i en region där Tjänstkarta inte är tillgänglig kan visas den inte i listrutan.

> [!NOTE]
> Du kan inte ändra arbetsytan som är kopplad till ett migreringsprojekt.

### <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna
Om du vill visa beroenden för en grupp som du behöver hämta och installera agenter på varje lokal dator som ingår i gruppen. Dessutom, om du har datorer som saknar Internetanslutning måste du ladda ned och installera [Log Analytics gateway](../azure-monitor/platform/gateway.md) på dem.

1. I **översikt**, klickar du på **hantera** > **grupper**går du till den nödvändiga gruppen.
2. I listan över datorer, i den **beroendeagenten** kolumnen, klickar du på **kräver installation** att se information om hur du hämtar och installerar agenterna.
3. På den **beroenden** sidan, hämta och installera Microsoft Monitoring Agent (MMA) och beroendeagenten på varje virtuell dator som ingår i gruppen.
4. Kopiera arbetsytans ID och nyckel. Du behöver dem när du installerar MMA på lokala datorer.

### <a name="install-the-mma"></a>Installera MMA

Installera agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar** för att acceptera licensen.
3. I **målmapp**, behålla eller ändra standardinstallationsmappen > **nästa**.
4. I **installationsalternativ för Agent**väljer **Azure Log Analytics** > **nästa**.
5. Klicka på **Lägg till** att lägga till en ny Log Analytics-arbetsyta. Klistra in i arbetsytans ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.


Installera agenten på en Linux-dator:

1. Över rätt samling (x86 eller x64) till Linux-datorn med scp/sftp.
2. Installera paketet med hjälp av argumentet--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Installera beroendeagenten
1. Installera beroendeagenten på en Windows-dator, dubbelklicka på installationsfilen och följ anvisningarna i guiden.
2. Installera beroendeagenten på en Linux-dator, installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

Mer information om stöd för beroende-agenten för den [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) och [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) operativsystem.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Sedan ändra gruppen utifrån beroendevisualisering
När du har installerat agenter på alla datorer i gruppen kan du visualisera beroenden i gruppen och förfina genom att följa de stegen nedan.

1. I Azure Migrate-projektet under **hantera**, klickar du på **grupper**, och välj gruppen.
2. På gruppsidan **Visa beroenden**, för att öppna beroendekarta för gruppen.
3. Beroendekarta för gruppen visar följande information:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till/från alla datorer som ingår i gruppen
        - Beroende datorer som inte har MMA och beroendeagenter agenten installerad är grupperade efter portnummer
        - Beroende datorer som har MMA och beroendeagenten installerat visas som separata rutor
    - Processer som körs på datorn, som du kan expandera varje datorrutan för att visa processer
    - Egenskaper som fullständigt kvalificerade namn, operativsystem, MAC-adress osv på varje dator som du kan klicka på varje datorrutan för att visa den här informationen

     ![Visa gruppberoenden](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Om du vill visa mer detaljerade beroenden, klickar du på tidsintervall om du vill ändra den. Intervallet är en timme som standard. Du kan ändra tidsintervallet eller ange start- och slutdatum och varaktighet.
4. Kontrollera beroende datorer den process som körs på varje dator och identifiera de datorer som ska läggas till eller tas bort från gruppen.
5. Använd Ctrl + klicka för att välja datorer på kartan för att lägga till eller ta bort dem från gruppen.
    - Du kan bara lägga till datorer som har identifierats.
    - Att lägga till och ta bort datorer från en grupp upphäver tidigare utvärderingar för den.
    - Du kan också skapa en ny utvärdering när du ändrar gruppen.
5. Klicka på **OK** ska sparas.

    ![Lägga till eller ta bort datorer](./media/how-to-create-group-dependencies/add-remove.png)

Om du vill kontrollera beroenden för en specifik dator som visas i beroendekarta grupp [konfigurera datorberoende mappning](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Nästa steg
- [Läs mer](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) om frågor och svar på beroendevisualisering.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
