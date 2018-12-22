---
title: Vad hände med Workbench?
titleSuffix: Azure Machine Learning service
description: Läs mer om vad som hände med Workbench-programmet, vad som har ändrats i tjänsten Azure Machine Learning och vad supporttidslinjen är.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c9559e07cc70cbd7adafd75c23b9e67d45bee48a
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184313"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-service"></a>Vad händer med Workbench i tjänsten Azure Machine Learning?

Workbench-programmet och vissa andra tidiga funktioner togs bort och ersattes i versionen från september 2018 för att frigöra plats för en förbättrad [arkitektur](concept-azure-machine-learning-architecture.md). Versionen innehåller många viktiga uppdateringar som baseras på feedback från kunder och förbättrar upplevelsen. Grundläggande funktioner från experimentkörningar till modelldistribution har inte ändrats, men nu kan du använda den robusta <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> och [CLI](reference-azure-machine-learning-cli.md) för att utföra dina maskininlärningsaktiviteter och pipelines.  

I den här artikeln får lära du dig om vad som ändrats och hur det påverkar ditt befintliga arbete med Azure Machine Learning Workbench och dess API:er.

## <a name="what-changed"></a>Vad har ändrats?

Den senaste versionen av Azure Machine Learning-tjänsten innehåller:
+ En [förenklad Azure-resursmodell](concept-azure-machine-learning-architecture.md)
+ [Den nya portalens användargränssnitt](how-to-track-experiments.md) för att hantera experiment och beräkningsmål
+ Ett nytt och mer omfattande Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>
+ Ett nytt, utökat [Azure CLI-tillägg](reference-azure-machine-learning-cli.md) för maskininlärning

[Arkitekturen](concept-azure-machine-learning-architecture.md) har gjorts om för enklare användning. I stället för flera Azure-resurser och konton behöver du bara en [Azure Machine Learning-tjänstarbetsyta](concept-azure-machine-learning-architecture.md#workspace).  Du kan skapa arbetsytor snabbt i [Azure-portalen](quickstart-get-started.md).  En arbetsyta kan användas av flera användare för att lagra tränings- och distributionsberäkningsmål, modellexperiment, Docker-avbildningar, distribuerade modeller och så vidare.

Det finns nya förbättrade CLI- och SDK-klienter i den aktuella versionen, men själva Workbench-skrivbordsprogrammet är inaktuellt. Nu kan du övervaka dina experiment i [instrumentpanelen för arbetsytor i Azure-webbportalen](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Använd instrumentpanelen för att hämta din experimenthistorik, hantera beräkningsmål som är kopplade till din arbetsyta, hantera modeller och Docker-avbildningar och även distribuera webbtjänster.

## <a name="how-do-i-migrate"></a>Hur migrerar jag?

De flesta av de artefakter som skapades i den tidigare versionen av Azure Machine Learning-tjänsten lagras i din egen lokala lagring eller molnlagring. Dessa artefakter försvinner aldrig. Om du vill migrera behöver du registrera artefakterna igen med den uppdaterade Azure Machine Learning-tjänsten. Lär dig vad du kan migrera och hur det går till i den här [migreringsartikeln](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Supporttidslinje

Du kan fortsätta att använda dina konton för experimentering och modellhantering samt Workbench-programmet ett tag till efter september 2018. Stödet för följande resurser tas bort progressivt under cirka 3–4 månader efter lanseringen. Du hittar fortfarande dokumentation för de gamla funktionerna i [avsnittet Resurser](../desktop-workbench/tutorial-classifying-iris-part-1.md) längst ned i innehållsförteckningen.

|Tillbakadragningsfas|Information som stöd för äldre funktioner|
|:---:|----------------|
|4 december 2018|Möjligheten att skapa ett _konto för Azure Machine Learning-experimentering_ och _ett modellhanteringskonto_ på Azure-portalen och från CLI har avslutats. Möjligheten att skapa ML-beräkningsmiljöer från CLI har också avslutats. Om du har ett befintligt konto fortsätter CLI och Workbench för skrivbord att fungera i den här fasen.|
|9 januari 2019|Stöd för allt annat, däribland återstående API:er och Workbench för skrivbord, avslutas det här datumet.|

[Börja migrera](how-to-migrate.md) i dag. Alla nya funktioner är tillgängliga med vår nya <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md) och [portalen](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Vad händer med körhistorik?

Körhistorik förblir tillgänglig ett tag. När du är redo att flytta till den uppdaterade versionen av Azure Machine Learning-tjänsten kan du exportera denna körhistorik om du vill behålla en kopia.

Körhistorik kallas nu _experiment_ i den aktuella versionen. Du kan samla in modellens experiment och utforska dem med hjälp av SDK, CLI eller webbportalen.

Portalens instrumentpanel för arbetsytor stöds endast i webbläsarna Edge, Chrome och Firefox.

[ ![Onlineportalen](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Går det fortfarande att förbereda data?

Dina befintliga dataförberedelsefiler kan inte överföras till den senaste versionen eftersom vi inte längre har Workbench. Du kan dock fortfarande förbereda dina data för modellering.  

Med mindre datamängder kan du använda <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning-SDK för dataförberedelse</a> för att snabbt förbereda dina data före modellering. 

Du kan använda den här samma <a href="https://aka.ms/aml-sdk" target="_blank">SDK:n</a> för större datamängder eller använda Azure Databricks för att förbereda stordatamängder. 

## <a name="will-projects-persist"></a>Kommer projekt att bevaras?

Du förlorar inte någon kod eller något arbete. I den äldre versionen är projekt molnentiteter med en lokal katalog. I den senaste versionen kopplar du lokala kataloger till Azure Machine Learning-tjänstarbetsytan med hjälp av en lokal konfigurationsfil. [Se ett diagram över den senaste arkitekturen](concept-azure-machine-learning-architecture.md).

Eftersom mycket av projektinnehållet redan fanns på den lokala datorn behöver du bara skapa en konfigurationsfil i den katalogen och referera till den i koden för att ansluta till din arbetsyta. [Lär dig hur migrerar din befintliga projekt.](how-to-migrate.md#projects)

Lär dig hur du kommer igång [i Python med huvud-SDK:n](quickstart-get-started.md).

## <a name="what-about-my-registers-models-and-images"></a>Vad händer med mina registrerade modeller och avbildningar?
 
De modeller som du registrerade i ditt gamla modellregister måste migreras till din nya arbetsyta om du vill fortsätta att använda dem. Du kan göra detta genom att [ladda ned modeller och registrera om dem](how-to-migrate.md) i den nya arbetsytan. 

Bilder som du skapade i ditt gamla avbildningsregister måste återskapas i den nya arbetsytan om du vill fortsätta att använda dem. Du kan göra detta genom att följa avsnittet [Konfigurera och skapa avbildning](how-to-deploy-and-where.md#configureimage). 

## <a name="what-about-deployed-web-services"></a>Vad händer med distribuerade webbtjänster?

Modeller som du distribuerade som webbtjänster med hjälp av ditt modellhanteringskonto fortsätter att fungera så länge det finns stöd för Azure Container Service (ACS). Dessa webbtjänster fungerar även när supporten har upphört för modellhanteringskonton. När stödet för det gamla CLI upphör att gälla upphör dock även möjligheten att hantera de webbtjänsterna.

I den nyare versionen distribueras modeller som webbtjänster till Azure Container Instances (ACI) eller Azure Kubernetes Service-kluster (AKS). Du kan även distribuera till FPGA och till IoT-kanten. Mer information finns i dokumentet [Hur och var du ska distribuera](how-to-deploy-and-where.md). Utan att behöva ändra några av dina bedömningsfiler, beroenden och scheman kan du distribuera om dina modeller med hjälp av den nya SDK:n eller CLI. 

## <a name="what-about-the-old-sdk--cli"></a>Vad gäller för den gamla SDK:n och CLI?

Ja, de fortsätter att fungera fram till januari (se [tidslinjen](#timeline) ovan). Vi rekommenderar att du börjar skapa dina nya experiment och modeller med den senaste SDK:n och/eller CLI.

I den senaste versionen gör den nya Python SDK att du kan interagera med Azure Machine Learning-tjänsten i alla Python-miljöer. Lär dig hur du installerar den senast <a href="https://aka.ms/aml-sdk" target="_blank">SDK:n</a>.  Du kan också använda [det uppdaterade Azure CLI-maskininlärningstillägget](reference-azure-machine-learning-cli.md) med den omfattande uppsättningen `az ml`-kommandon för att interagera med tjänsten i valfri kommandoradmiljö, inklusive Azure-portalens molngränssnitt.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Vad händer med Azure Machine Learning för Visual Studio Code?

Med den senaste versionen har Azure Machine Learning för Visual Studio (VS) Code utökats och förbättrats för att fungera med de nya funktionerna ovan.

[ ![Azure Machine Learning för Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png) ] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Vad gäller för domänpaket?

Domänpaketen för [Visuellt innehåll, Textanalys och Prognostisering](../desktop-workbench/reference-python-package-overview.md) kan inte användas med den senaste versionen av Azure Machine Learning. Du kan dock fortfarande skapa och träna modeller för visuellt innehåll, textanalys och prognostisering med den senaste Azure Machine Learning Python-<a href="https://aka.ms/aml-sdk" target="_blank">SDK:n</a>. Om du vill lära dig mer om att migrera befintliga modeller som skapats med Visuellt innehåll-, Textanalys- och Prognostisering-paket kontaktar du oss på [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [den senaste arkitekturen för Azure Machine Learning-tjänsten](concept-azure-machine-learning-architecture.md) och prova någon av snabbstarterna eller självstudierna:

* [Vad är Azure Machine Learning-tjänsten?](overview-what-is-azure-ml.md)
* [Snabbstart: Skapa en arbetsyta med Python](quickstart-get-started.md)
* [Självstudie: Träna en modell](tutorial-train-models-with-aml.md)
