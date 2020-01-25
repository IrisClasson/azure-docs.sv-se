---
title: Flytta Blob storage-data med SSIS-anslutningsappar - Team Data Science Process
description: Lär dig hur du flyttar data till eller från Azure Blob Storage med hjälp av SQL Server Integration Services Feature Pack för Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720880"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Flytta data till eller från Azure Blob Storage med SSIS-anslutningsappar
Den [Funktionspaketet för SQL Server Integration Services för Azure](https://msdn.microsoft.com/library/mt146770.aspx) innehåller komponenter för att ansluta till Azure, överföra data mellan Azure och lokala datakällor och bearbeta data som lagras i Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

När kunderna har flyttat lokala data till molnet kan de komma åt sina data från valfri Azure-tjänst för att utnyttja den fulla kraften i serien med Azure-tekniker. Data kan användas senare, till exempel i Azure Machine Learning eller i ett HDInsight-kluster.

Exempel på hur du använder dessa Azure-resurser finns i genom gången av [SQL](sql-walkthrough.md) och [HDInsight](hive-walkthrough.md) .

En beskrivning av canonical scenarier som använder SSIS för att utföra affärsbehov som är vanliga i hybrid för dataintegrering, finns i [göra mer med Funktionspaketet för SQL Server Integration Services för Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogg.

> [!NOTE]
> En fullständig introduktion till Azure blob storage finns [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Krav
För att utföra de uppgifter som beskrivs i den här artikeln måste du ha en Azure-prenumeration och ett Azure Storage-konto konfigurerat. Du behöver Azure Storage konto namnet och konto nyckeln för att ladda upp eller hämta data.

* Du ställer in en **Azure-prenumeration**, se [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett **lagrings konto** och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

Du använder den **SSIS-anslutningsappar**, måste du ladda ned:

* **SQL Server 2014 eller 2016 Standard (eller senare)** : installationen inkluderar SQL Server Integration Services.
* **Microsoft SQL Server 2014-eller 2016 Integration Services Feature Pack för Azure**: dessa anslutningar kan laddas ned från [SQL Server 2014 integrerings tjänster](https://www.microsoft.com/download/details.aspx?id=47366) och [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) -sidor.

> [!NOTE]
> SSIS installeras med SQL Server, men ingår inte i Express-version. Information om vilka program som ingår i olika utgåvor av SQL Server finns i [SQL Server-versioner](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Utbildningsmaterial på SSIS, se [händerna på utbildning för SSIS](https://www.microsoft.com/sql-server/training-certification)

Information om hur du får upp och som körs med SISS för att skapa enkel extrahering, transformering och laddning (ETL)-paket, se [SSIS-självstudie: skapa ett enkelt ETL-paket](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Hämta NYC Taxi datauppsättningen
Exemplet som beskrivs här använda en offentligt tillgänglig datauppsättning – den [NYC Taxi kommunikation](https://www.andresmh.com/nyctaxitrips/) datauppsättning. Datauppsättningen består av cirka 173 miljoner taxi bilar i NYC år 2013. Det finns två typer av data: resa information om data och avgiften data. Eftersom det finns en fil för varje månad har vi 24 filer, som var och en är ungefär 2 GB okomprimerad.

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure blob storage
Om du vill flytta data med hjälp av SSIS funktionspaket från en lokal plats till Azure blob storage, använder vi en instans av den [ **Azure Blob överför uppgift**](https://msdn.microsoft.com/library/mt146776.aspx), visas här:

![Konfigurera –--virtuell dator för datavetenskap](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Här beskrivs de parametrar som används för aktiviteten:

| Field | Beskrivning |
| --- | --- |
| **AzureStorageConnection** |Anger en befintlig Azure Storage anslutnings hanterare eller skapar en ny som refererar till ett Azure Storage konto som pekar på var BLOB-filerna finns. |
| **BlobContainer** |Anger namnet på BLOB-behållaren som innehåller de överförda filerna som blobbar. |
| **BlobDirectory** |Anger den blob-katalogen där den överförda filen lagras som en blockblob. Blob-katalogen är en virtuell hierarkisk struktur. Om blobben som redan finns it ia ersättas. |
| **LocalDirectory** |Anger den lokala katalogen som innehåller filerna som ska laddas upp. |
| **Filnamn** |Anger ett Namnfilter för att markera filer med det angivna namnmönstret. Till exempel MySheet\*.xls\* innehåller filer, till exempel MySheet001.xls och MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Anger ett tidsfilter för intervallet. Filer som modifierats efter *TimeRangeFrom* och innan *TimeRangeTo* ingår. |

> [!NOTE]
> Den **AzureStorageConnection** autentiseringsuppgifter måste vara korrekta och **BlobContainer** måste finnas innan överföringen görs.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Hämta data från Azure blob storage
För att hämta data från Azure blob storage till en lokal lagring med SSIS, använder du en instans av den [Azure Blob hämta uppgift](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Mer avancerade scenarier för Azure-SSIS
Funktionspaketet för SSIS möjliggör mer komplexa flöden som ska hanteras av paketering uppgifter tillsammans. Blob-data kan till exempel feed direkt i ett HDInsight-kluster, vars utdata kan hämtas tillbaka till en blob och sedan till den lokala lagringen. SSIS kan köra Hive och Pig-jobb på ett HDInsight-kluster med ytterligare SSIS-anslutningsappar:

* Använd för att köra ett Hive-skript på ett Azure HDInsight-kluster med SSIS [Azure HDInsight Hive-aktiviteten](https://msdn.microsoft.com/library/mt146771.aspx).
* Använd för att köra ett Pig-skript på ett Azure HDInsight-kluster med SSIS [Azure HDInsight Pig uppgift](https://msdn.microsoft.com/library/mt146781.aspx).

