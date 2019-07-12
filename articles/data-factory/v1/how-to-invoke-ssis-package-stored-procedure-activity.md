---
title: Anropa SSIS-paket med Azure Data Factory - lagrade Proceduraktiviteten | Microsoft Docs
description: Den här artikeln beskriver hur du anropar ett SQL Server Integration Services (SSIS)-paket från en Azure Data Factory-pipeline med hjälp av den lagrade Proceduraktiviteten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 030617d3afd73c68793ca0a1d6185264c92b791f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839903"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Anropa ett SSIS-paket med hjälp av aktivitet för lagrad procedur i Azure Data Factory
Den här artikeln beskriver hur du anropar ett SSIS-paket från en Azure Data Factory-pipeline med hjälp av en lagrad procedur-aktivitet. 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [anropa SSIS-paket med lagrad proceduraktivitet i](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-sql-database"></a>Azure SQL Database 
I den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en Azure SQL Database Managed Instance.

### <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS integration runtime om du inte har en genom att följa de stegvisa anvisningarna i den [självstudien: Distribuera SSIS-paket](../tutorial-create-azure-ssis-runtime-portal.md). Du kan inte använda Data Factory version 1 för att skapa en Azure-SSIS integration runtime. 

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en lagrad procedur-aktivitet som anropar ett SSIS-paket.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Följande procedur innehåller steg för att skapa en datafabrik. Du kan skapa en pipeline med en aktivitet för lagrad procedur i den här datafabriken. Lagrad procedur-aktivitet kör en lagrad procedur i SSISDB-databasen för att köra dina SSIS-paket.

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen
2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Om du vill skapa data factory, kör du följande **New AzDataFactory** cmdlet, med hjälp av den platsen och egenskapen ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst för att länka Azure SQL database som är värd för SSIS-katalogen till din datafabrik. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSISDB-databasen och kör en lagrad procedur om du vill köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i **C:\ADF\RunSSISPackage** mapp med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;användarnamn&gt;@&lt;servername&gt; och &lt;lösenord&gt; med värdena för din Azure SQL Database innan sparar filen.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. I **Azure PowerShell**, växla till den **C:\ADF\RunSSISPackage** mapp.
3. Kör den **New AzDataFactoryLinkedService** cmdlet för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Det här är en dummy datauppsättning som styr schemat för pipelinen. Lägg märke till att frekvensen är inställd på Hour och interval anges till 1. Därför körs pipelinen när en timme i pipeline- och sluttider. 

1. Skapa en OutputDataset.json-fil med följande innehåll: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Kör den **New AzDataFactoryDataset** cmdlet för att skapa en datauppsättning. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med en aktivitet för lagrad procedur 
I det här steget skapar du en pipeline med en lagrad procedur-aktivitet. Aktiviteten anropar sp_executesql lagrade proceduren för att köra dina SSIS-paket. 

1. Skapa en JSON-fil med namnet **MyPipeline.json** i den **C:\ADF\RunSSISPackage** mapp med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;mappnamn&gt;, &lt;projektnamn&gt;, &lt;paketnamn&gt; med namn på mapp, projekt och paket i SSIS-katalogen innan du sparar filen.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Så här skapar du pipelinen: **RunSSISPackagePipeline**, kör den **New AzDataFactoryPipeline** cmdlet.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör **Get-AzDataFactorySlice** att få information om alla sektorer av den utdata datauppsättning **, vilket är utdatatabellen för pipelinen.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. 
1. Kör **Get-AzDataFactoryRun** för att hämta information om aktiviteten som körs för en viss sektor.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. 

    Du kan köra följande fråga mot SSIDB-databasen i Azure SQL-servern att kontrollera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om aktivitet för lagrad procedur, finns det [lagringsprocedur-aktivitet](data-factory-stored-proc-activity.md) artikeln.

