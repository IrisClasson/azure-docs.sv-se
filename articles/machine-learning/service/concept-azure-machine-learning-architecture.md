---
title: Arkitektur och nyckelkoncept
titleSuffix: Azure Machine Learning service
description: Läs mer om arkitekturen, villkor, begrepp och arbetsflöde som utgör Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: 294b376665ba6b62f79f826520bc933543b38bda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059281"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Så här fungerar Azure Machine Learning-tjänsten: Arkitektur och begrepp

Läs mer om arkitekturen, begrepp och arbetsflöde för Azure Machine Learning-tjänsten. De viktigaste komponenterna i tjänsten och det allmänna arbetsflödet för att använda tjänsten visas i följande diagram:

[![Azure Machine Learning-service-arkitektur och arbetsflöde](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>Arbetsflöde

Machine learning-arbetsflöde Allmänt följer den här sekvensen:

1. Utveckla maskininlärning utbildning skript i **Python** eller med det visuella gränssnittet.
1. Skapa och konfigurera en **beräkningsmålet**.
1. **Skicka skripten** till den konfigurerade beräkningsmål ska köras i den miljön. Vid träning, skripten kan läsa från eller skriva till **datalager**. Posterna i körningen sparas som **körs** i den **arbetsytan** och grupperade under **experiment**.
1. **Fråga experimentet** för loggade mått från de aktuella och tidigare körningarna. Om mått som inte visar önskat utfall ska köras i slinga att gå tillbaka till steg 1 och iterera på dina skript.
1. När du har hittat ett tillfredsställande kör registrera beständiga modellen i den **modellen registret**.
1. Utveckla ett bedömningsskript som använder modellen och **distribuera modellen** som en **webbtjänsten** i Azure eller till en **IoT Edge-enhet**.

Du utför de här stegen med någon av följande:
+ [Azure Machine Learning-SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Azure Machine Learning VS Code-tillägg](how-to-vscode-tools.md)
+  Den [visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten](ui-concept-visual-interface.md)


## <a name="glossary-of-concepts"></a>Ordlista med begrepp

+ <a href="#workspaces">Workspace</a>
+ <a href="#experiments">Experiment</a>
+ <a href="#models">modeller</a>
+ <a href="#run-configurations">Köra konfiguration</a>
+ <a href="#datasets-and-datastores">Datauppsättning och datalager</a>
+ <a href="#compute-targets">Beräkningsmål</a>
+ <a href="#training-scripts">Inlärningsskript</a>
+ <a href="#runs">Kör</a>
+ <a href="#github-tracking-and-integration">Git-spårning</a>
+ <a href="#snapshots">ögonblicksbild</a>
+ <a href="#activities">Aktivitet</a>
+ <a href="#images">Avbildning</a>
+ <a href="#deployment">Distribution</a>
+ <a href="#web-service-deployments">Webbtjänster</a>
+ <a href="#iot-module-deployments">IoT-moduler</a>
+ <a href="#ml-pipelines">ML-pipelines</a>
+ <a href="#logging">Loggning</a>

> [!NOTE]
> Även om den här artikeln definierar termer och begrepp som används av Azure Machine Learning-tjänsten, anger inte termer och begrepp för Azure-plattformen. Läs mer om Azure-plattformen terminologi, den [Microsoft Azures ordlista](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).


### <a name="workspaces"></a>Arbetsytor

[Arbetsytan](concept-workspace.md) är den översta resursen för Azure Machine Learning-tjänsten. Det ger en centraliserad plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning-tjänsten.

En taxonomi för arbetsytan illustreras i följande diagram:

[![Arbetsytan taxonomi](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Mer information om arbetsytor finns i [vad är en Azure Machine Learning-arbetsyta?](concept-workspace.md).

### <a name="experiments"></a>Experiment

Ett experiment är en gruppering av många körs från ett angivet skript. Det är alltid hör till en arbetsyta. När du skickar in en körning ska ange du ett namn på experiment. Information om körningen lagras under försöket. Om du skickar en körning och ange ett namn på experiment som inte finns, skapas automatiskt ett nytt experiment med det nyss angivna namnet.

Ett exempel på hur du använder ett experiment finns i [snabbstarten: Kom igång med Azure Machine Learning-tjänsten](quickstart-run-cloud-notebook.md).

### <a name="models"></a>Modeller

I sin enklaste är en modell en typ av kod som hämtar indata och utdata. Skapa en modell för maskininlärning innebär att välja en algoritm, att förse den med data och justering av hyperparametrar. Utbildning är en iterativ process som skapar en tränad modell, som kapslar in det modellen har lärt dig under utbildning.

En modell produceras av en körning i Azure Machine Learning. Du kan också använda en modell som har tränats utanför Azure Machine Learning. Du kan registrera en modell i en arbetsyta för Azure Machine Learning-tjänsten.

Azure Machine Learning-tjänsten är framework oberoende. När du skapar en modell kan använda du alla populära machine learning-ramverk, som lär du dig Scikit, XGBoost, PyTorch, TensorFlow och Chainer.

Ett exempel för att träna en modell finns i [självstudien: Träna en modell för bildklassificering med Azure Machine Learning-tjänsten](tutorial-train-models-with-aml.md).

Den **modellen registret** håller reda på alla modeller i din arbetsyta för Azure Machine Learning-tjänsten.

Modeller identifieras av namn och version. Varje gång som du registrerar en modell med samma namn som en befintlig förutsätter registret att det är en ny version. Versionen ökas och den nya modellen är registrerad med samma namn.

När du registrerar modellen kan du ange ytterligare metadatataggar och sedan använda taggar när du söker efter modeller.

Du kan inte ta bort modeller som används av en aktiv distribution.

Ett exempel med att registrera en modell finns i [tränar en modell för klassificering av avbildning med Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Kör konfigurationer

En kör konfiguration är en uppsättning instruktioner som definierar hur ett skript ska köras i en angiven beräkningsmål. Konfigurationen innehåller en bred uppsättning beteende definitioner, till exempel om du vill använda en befintlig Python-miljö eller använda en Conda-miljö som är byggd från en specifikation.

En kör konfiguration kan vara kvar i en fil på den katalog som innehåller utbildning skriptet eller den kan konstrueras som ett objekt i minnet och används för att skicka en körning.

Till exempel kör konfigurationer, se [Använd beräkningsmål träna din modell](how-to-set-up-training-targets.md).

### <a name="datasets-and-datastores"></a>Datauppsättningar och datalager

**Azure Machine Learning datauppsättningar** (förhandsversion) gör det enklare att komma åt och arbeta med dina data. Datauppsättningar hantera data i olika scenarier, till exempel modellträning och pipeline skapas. Med Azure Machine Learning SDK kan du komma åt underliggande lagring, utforska och förbereda data, hantera livscykeln för olika datauppsättning definitioner och jämför datauppsättningar som används i utbildning och i produktion.

Datauppsättningar ger metoder för att arbeta med data i populära format, till exempel med hjälp av `from_delimited_files()` eller `to_pandas_dataframe()`.

Mer information finns i [skapa och registrera Azure Machine Learning datauppsättningar](how-to-create-register-datasets.md).  Fler exempel med datauppsättningar, finns det [exempel anteckningsböcker](https://aka.ms/dataset-tutorial).

En **datalager** är en abstraktion av lagring över ett Azure storage-konto. Datalagringen kan använda en Azure blob-behållare eller en Azure-filresurs som backend-lagring. Varje arbetsyta har ett standard-datalager, och du kan registrera ytterligare datalager. Använda Python SDK-API eller Azure Machine Learning CLI för att lagra och hämta filer från databasen.

### <a name="compute-targets"></a>Beräkningsmål

En [beräkningsmålet](concept-compute-target.md) kan du ange beräkningsresursen där du kör dina utbildningsskript eller värden tjänstdistributionen. Den här platsen kan vara din lokala dator eller en molnbaserad beräkningsresurs. Beräkningsmål gör det enkelt att ändra din beräkningsmiljö utan att ändra koden. 

Läs mer om den [tillgängliga beräkningsmål för inlärning och distribuering](concept-compute-target.md). 

### <a name="training-scripts"></a>Utbildningsskript

För att träna en modell kan du ange den katalog som innehåller inlärningsskript och tillhörande filer. Du kan även ange ett namn på experiment, som används för att lagra information som samlas in under utbildning. Hela katalogen kopieras till miljön (beräkningsmål) vid träning, och skriptet som anges av körningskonfigurationen har startats. En ögonblicksbild av katalogen lagras också under experiment på arbetsytan.

Ett exempel finns i [Självstudier: Träna en modell för bildklassificering med Azure Machine Learning-tjänsten](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Körningar

En körning är en post som innehåller följande information:

* Metadata om körningen (tidsstämpel, varaktighet och så vidare)
* Mått som loggas av skriptet
* Utdatafilerna som autocollected av experimentet eller uttryckligen överförts av dig
* En ögonblicksbild av den katalog som innehåller dina skript innan körningen

Du kan skapa en körning när du skickar in ett skript för att träna en modell. En körning kan ha noll eller flera underordnade körs. Den översta körningen kan till exempel ha två underordnade körningar, som kan ha sin egen underordnade kör.

Ett exempel med att visa körningar som genereras av träna en modell finns i [snabbstarten: Kom igång med Azure Machine Learning-tjänsten](quickstart-run-cloud-notebook.md).

### <a name="github-tracking-and-integration"></a>GitHub-spårning och integration

När du startar en utbildning som kör där källkatalogen är en lokal Git-lagringsplats, lagras information om databasen i körningshistoriken. Till exempel loggas aktuellt genomförande-ID för lagringsplatsen som en del av historiken. Detta fungerar med körningar som skickas med en kostnadsuppskattning, ML pipeline eller skript som körs. Den fungerar även för körningar som har skickats från Machine Learning CLI eller SDK.

### <a name="snapshots"></a>Ögonblicksbilder

När du skickar en körning komprimerar katalogen som innehåller skriptet som en zip-fil och skickar dem till beräkningsmål-i Azure Machine Learning. Zip-filen hämtas sedan och skriptet körs det. Azure Machine Learning lagrar också zip-filen som en ögonblicksbild som en del av den kör posten. Alla som har åtkomst till arbetsytan kan bläddra en kör post och ladda ned ögonblicksbilden.

> [!NOTE]
> Skapa en Ignorera-fil (.gitignore eller .amlignore) för att förhindra att onödiga filer inkluderas i ögonblicksbilden. Placera filen i katalogen ögonblicksbild och Lägg till filnamnen som bör ignoreras i den. Filen .amlignore använder samma [syntax och mönster som .gitignore-fil](https://git-scm.com/docs/gitignore). Om båda filerna finns företräde filen .amlignore.

### <a name="activities"></a>Aktiviteter

En aktivitet representerar en långvarig åtgärd. Följande åtgärder är exempel på aktiviteter:

* Skapa eller ta bort ett beräkningsmål
* Köra ett skript på ett beräkningsmål

Aktiviteter kan ge meddelanden via SDK eller webbläsaren så att du enkelt kan övervaka förloppet för dessa åtgärder.

### <a name="images"></a>Avbildningar

Bilder är ett sätt att distribuera en modell, tillsammans med alla komponenter måste du använda modellen på ett tillförlitligt sätt. En avbildning innehåller följande objekt:

* En modell.
* En arbetsflödesbaserad skript eller program. Du kan använda skript för att skicka indata till modellen och returnera utdata från modellen.
* De beroenden som krävs av modellen eller bedömnings-skript eller program. Du kan exempelvis innehålla en miljö Conda-fil som listar beroendena för Python-paketet.

Azure Machine Learning kan skapa två typer av bilder:

* **FPGA bild**: Används när du distribuerar till en fält-programmable gate array i Azure.
* **Docker-avbildning**: Används när du distribuerar till beräkningsmål än FPGA. Exempel är Azure Container Instances och Azure Kubernetes Service.

Azure Machine Learning-tjänsten tillhandahåller en basavbildning som används som standard. Du kan också ange dina egna anpassade avbildningar.

### <a name="image-registry"></a>Avbildningsregister

Bilder är katalogiserade i den **avbildningsregister** i din arbetsyta. Du kan ange ytterligare metadatataggar när du skapar avbildningen, så att du kan skicka frågor mot dem för att hitta din avbildning senare.

Ett exempel för att skapa en avbildning finns i [distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

Ett exempel för att distribuera en modell med en anpassad avbildning finns i [hur du distribuerar en modell med en anpassad dockeravbildning](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Distribution

En distribution är en instansiering av modellen i antingen en webbtjänst som kan användas i molnet eller en IoT-modul för integrerad enhet distributioner.

#### <a name="web-service-deployments"></a>Webbtjänstdistributioner

En distribuerad webbtjänst kan använda Azure Container Instances, Azure Kubernetes Service eller FPGA. Du har skapat tjänsten från din modell, skript och tillhörande filer. Dessa är inkapslade i en bild, vilket ger körningsmiljö för webbtjänsten. Avbildningen har en Utjämning av nätverksbelastning, HTTP-slutpunkt som tar emot bedömnings förfrågningar som skickas till webbtjänsten.

Azure hjälper dig att övervaka din distribution av webbtjänster genom att samla in Application Insights telemetry eller modell telemetri om du har valt att aktivera den här funktionen. Dessa data är tillgänglig endast för dig och lagras i dina Application Insights och storage-konto instanser.

Om du har aktiverat automatisk skalning, skalar Azure automatiskt din distribution.

Ett exempel för att distribuera en modell som en webbtjänst finns i [distribuera en modell för klassificering av avbildning i Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>IoT-modul-distributioner

En distribuerad IoT-modul är en Docker-behållare som innehåller din modell och associerade skript eller program och eventuella ytterligare beroenden. Du kan distribuera dessa moduler med hjälp av Azure IoT Edge på edge-enheter.

Om du har aktiverat övervakning, Azure att samlar in telemetridata från modellen i Azure IoT Edge-modul. Dessa data är tillgänglig för dig och lagras i din instans av storage-konto.

Azure IoT Edge säkerställer att din modul körs och det övervakar den enhet som är värd för den.

### <a name="ml-pipelines"></a>ML-Pipelines

Använder du machine learning pipelines för att skapa och hantera arbetsflöden som sätta ihop machine learning faser. En pipeline kan till exempel innehålla förberedelse av data, modellträning, distribution av modeller och inferens/bedömning faser. Varje fas kan omfatta flera steg, som kan köras obevakat i olika beräkningsmål.

Mer information om machine learning pipelines med den här tjänsten finns i [Pipelines och Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Loggning

När du utvecklar din lösning kan du använda Azure Machine Learning Python SDK i ditt Python-skript för att logga godtyckligt mått. När du kör, fråga om mått för att avgöra om körningen har producerat modellen som du vill distribuera.

### <a name="next-steps"></a>Nästa steg

Kom igång med Azure Machine Learning-tjänsten, se:

* [Vad är Azure Machine Learning Service?](overview-what-is-azure-ml.md)
* [Skapa en arbetsyta för Azure Machine Learning-tjänsten](setup-create-workspace.md)
* [Självstudie (del 1): Träna en modell](tutorial-train-models-with-aml.md)
