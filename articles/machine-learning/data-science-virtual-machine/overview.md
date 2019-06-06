---
title: Introduktion till Azure Data Science Virtual Machine för Linux och Windows | Microsoft Docs
description: Viktiga analysscenarier och -komponenter för virtuella Windows- och Linux-datorer för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: gokuma
ms.openlocfilehash: 384cb274496670e0b0b5a33e001e78a0babed3f0
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427788"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introduktion till Azure Data Science Virtual Machine för Linux och Windows

Den virtuella datorn för datavetenskap (DSVM) är en anpassad VM-avbildning i Microsoft Azure-molnet som skapats specifikt för datavetenskap. Många populära verktyg för datavetenskap och annat finns förinstallerade och förkonfigurerade så att du kan börja direkt med att skapa intelligenta program för avancerade analyser. Det finns i Windows Server och Linux. Vi erbjuder Windows-versionen av DSVM på Server 2016 och Server 2012. Vi erbjuder Linux-versioner av DSVM på Ubuntu 16.04 LTS och CentOS 7.4.

Den här artikeln beskriver vad du kan göra med den virtuella datorn för datavetenskap. Den beskriver några av scenarierna för att använda den virtuella datorn och specificerar viktiga funktioner som är tillgängliga på Windows och Linux-versioner. Artikeln innehåller också anvisningar om hur du kommer igång med dem.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Vad kan jag göra med den virtuella datorn för datavetenskap?
Målet med den virtuella datorn för datavetenskap (DSVM) är att ge datapersonal på alla kunskapsnivåer och inom alla branscher en smidig, förkonfigurerad och helt integrerad datavetenskapsmiljö. I stället för att distribuera en jämförbar arbetsyta på egen hand kan du etablera en DSVM – och på så sätt spara dagar eller till och med _veckor_ på installation, konfiguration och pakethanteringsprocesser. När din DSVM har allokerats kan du omedelbart börja arbeta med ditt datavetenskapsprojekt.

Den virtuella datorn för datavetenskap har utformats och konfigurerats för att arbeta med en rad olika användningsscenarier. Du kan skala din miljö upp eller ned som ditt projekt behov ändras. Du kan också använda det språk du föredrar att programmera datavetenskapsuppgifter och installera andra verktyg för att anpassa systemet efter dina specifika behov.

## <a name="key-scenarios"></a>Viktiga scenarier
I det här avsnittet föreslås några viktiga scenarier som den virtuella datorn för datavetenskap kan distribueras för.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Förkonfigurerat analysskrivbord i molnet
Den virtuella datorn för datavetenskap tillhandahåller en baslinjekonfiguration för datavetenskapsteam som vill ersätta sina lokala skrivbord med ett hanterat molnskrivbord. Den här baslinjen säkerställer att all datapersonal i ett team har en konsekvent konfiguration för att kontrollera experiment och underlätta samarbete. Det sänker även kostnaderna genom att minska. Den här belastningen minskningen sparar tid som behövs för att utvärdera, installera och underhålla de olika programvarupaket som behövs för att göra avancerade analyser.

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap
Enterprise utbildare och lärare som undervisar science dataklasser vanligtvis ger du en avbildning av virtuell dator. De tillhandahåller image för att säkerställa att deras studenter har en konsekvent konfiguration och att exemplen fungerar förutsägbart. Den virtuella datorn för datavetenskap skapar en miljö på begäran med en konsekvent konfiguration som minskar problemen med support och inkompatibilitet. Det är mycket användbart i fall där de här miljöerna behöver skapas ofta, särskilt för kortare kurser.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastisk kapacitet på begäran för storskaliga projekt
Datavetenskapstävlingar/”hackathons” eller storskalig datamodellering och -utforskning kräver utskalad maskinvarukapacitet, vanligtvis under kort tid. Den virtuella datorn för datavetenskap kan bidra till att replikera datavetenskapsmiljö snabbt på begäran, på utskalade servrar där experiment som de kraftfulla resurserna som ska köras.

### <a name="custom-compute-power-for-azure-notebooks"></a>Anpassad beräkningskraft för Azure-anteckningsböcker

[Azure anteckningsböcker](/azure/notebooks/azure-notebooks-overview) är en kostnadsfri värdbaserade tjänst att utveckla, köra och dela Jupyter notebooks i molnet där inte har installerats. Kostnadsfri tjänst-nivå, men är begränsad till 4GB minne och 1GB data. Om du vill frigöra alla gränser kopplar du sedan ett anteckningsböcker projekt till en virtuell dator för datavetenskap eller andra virtuella datorer som kör Jupyter-server. Om du loggar in på Azure-anteckningsböcker med ett konto med Azure Active Directory (till exempel ett företagskonto) visar anteckningsböcker automatiskt virtuella datorer för datavetenskap i alla prenumerationer som är kopplade till kontot. Mer information finns i avsnittet om att [hantera och konfigurera projekt på beräkningsnivån](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier).

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig experimentering och utvärdering
Den virtuella datorn för datavetenskap kan användas för att utvärdera och lära sig verktyg, till exempel Microsoft ML Server, SQL Server, Visual Studio-verktyg, Jupyter, deep learning-/ML-verktyg och nya verktyg som är populära i communityn, med minimal konfiguration. Eftersom den virtuella datorn för datavetenskap kan konfigureras snabbt, kan den användas i andra kortsiktiga Användningsscenarier. Några vanliga scenarier replikera publicerade experiment, köra demonstrationer, följa genomgångar i online-sessioner och konferenssjälvstudier.

### <a name="deep-learning"></a>Deep learning
Den virtuella datorn för datavetenskap kan användas som utbildningsmodeller med hjälp av deep learning-algoritmer på GPU-baserad maskinvara (grafikprocessorer). Genom att utnyttja Azure-molnets VM-skalningsfunktioner hjälper DSVM dig att använda GPU-baserad maskinvara i molnet enligt behov. Det går att växla till en GPU-baserad virtuell dator vid utbildning av stora modeller eller behov av snabba beräkningar samtidigt som samma OS-disk behålls.  Windows Server 2016-versionen av DSVM levereras förinstallerad med GPU-drivrutiner, -ramverk och GPU-versioner av deep learning-ramverk. I Linux-utgåvan är deep learning på GPU aktiverat på DSVM för både CentOS och Ubuntu. Du kan distribuera Ubuntu eller CentOS Windows 2016-versionen av virtuell dator för datavetenskap till en icke GPU-baserad Azure VM. I det här fallet tillbaka alla djupinlärning ramverk till CPU-läget.

## <a name="whats-included-in-the-data-science-vm"></a>Vad ingår i den virtuella datorn för datavetenskap?
Många populära datavetenskaps- och deep learning-verktyg har redan installerats och konfigurerats på den virtuella datorn för datavetenskap. Den innehåller också verktyg som gör det enkelt att arbeta med olika Azure-data och analys-produkter, till exempel Microsoft ML Server (R, Python) för att skapa prediktiva modeller eller SQL Server 2017 för utforskning av storskaliga datauppsättningar. Den virtuella datorn för datavetenskap innehåller en mängd andra verktyg från communityn för öppen källkod och från Microsoft, samt exempelkod och anteckningsböcker. I följande tabell specificeras och jämförs huvudkomponenterna som ingår i Windows- och Linux-versionerna av den virtuella datorn för datavetenskap.


| **Verktyg**                                                           | **Windows-version** | **Linux-version** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) med populära paket förinstallerade   |Y                      | Y             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition innehåller <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) – parallellt och distribuerat högpresterande ramverk (R och Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) – nya avancerade ML-algoritmer från Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R och Python – driftsättning](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-plus med delad aktivering – Excel, Word och PowerPoint   |Y                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 med populära paket förinstallerade    |Y                      |Y              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) med populära paket för Julia-språket förinstallerade                         |Y                      |Y              |
| Relationsdatabaser                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Databasverktyg                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC-/JDBC-drivrutiner| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (frågeverktyg) <br /> * bcp, sqlcmd <br /> * ODBC-/JDBC-drivrutiner|
| Skalbar databasanalys med SQL Server ML-tjänster (R, Python) | Y     |N              |
| **[Jupyter Notebook Server](https://jupyter.org/) med följande kärnor**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | J (endast Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub (anteckningsboksserver för flera användare)| N | Y |
| JupyterLab (anteckningsboksserver för flera användare) | N | J (endast Ubuntu) |
| **Utvecklingsverktyg, IDE: er och kod redigerare**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) med Git Plugin, Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs), och [R Tools för Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia-IDE)](https://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim och Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git och GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | Y | N |
| Power BI Desktop | Y | N |
| SDK:er för åtkomst till Azure och Cortana Intelligence Suite med tjänster | Y | Y |
| **Verktyg för dataförflyttning och -hantering** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE-drivrutin](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB-datamigreringsverktyg](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): Flytta data mellan OnPrem och molnet | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix-/Linux-kommandoradsverktyg | Y | Y |
| [Apache Drill](https://drill.apache.org) för datautforskning | Y | Y |
| **Machine Learning-verktyg** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integrering med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | J (endast Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | J (endast Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | J (endast Ubuntu) |
| **Deep Learning-verktyg** <br>Alla verktyg fungerar på en GPU eller CPU |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | J (Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | J (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | J (Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe och Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA-, cuDNN-, NVIDIA-drivrutin](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **Stordataplattform (endast DevTest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokal [Spark](https://spark.apache.org/) fristående | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Lokal [Hadoop](https://hadoop.apache.org/) (HDFS, YARN) | N | Y |

## <a name="get-started"></a>Kom igång

### <a name="windows-data-science-vm"></a>Virtuell Windows-dator för datavetenskap
* Mer information om hur du skapar en Windows-DSVM och använder den finns i [Etablera den virtuella Windows-datorn för datavetenskap](provision-vm.md). Mer information om hur du utför olika uppgifter som behövs för ett datavetenskapsprojekt på en Windows-DSVM finns i [Tio saker du kan göra på den virtuella datorn för datavetenskap](vm-do-ten-things.md).

### <a name="linux-data-science-vm"></a>Virtuell Linux-dator för datavetenskap
* Mer information om hur du skapar en Ubuntu-DSVM och använder den finns i [Etablera den virtuella datorn för datavetenskap för Linux (Ubuntu)](dsvm-ubuntu-intro.md). Mer information om hur du skapar en CentOS-DSVM och använder den finns i [Etablera en virtuell Linux CentOS-dator för datavetenskap på Azure](linux-dsvm-intro.md).
* En genomgång som visar hur du utför flera vanliga datavetenskapsuppgifter med den virtuella Linux-datorn, både CentOS och Ubuntu, finns i [Datavetenskap på den virtuella Linux-datorn för datavetenskap](linux-dsvm-walkthrough.md).

## <a name="next-steps"></a>Nästa steg
[R-utvecklarens guide till Azure](/azure/architecture/data-guide/technology-choices/r-developers-guide)
