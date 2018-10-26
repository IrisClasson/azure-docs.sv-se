---
title: Köra SSIS-paket med köra SSIS-paket aktivitet – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kör ett SQL Server Integration Services (SSIS)-paket i en Azure Data Factory-pipeline med aktiviteten kör SSIS-paket.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: cda439973c584a57cadc30de7fb931732682de00
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092465"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Kör ett SSIS-paket med aktiviteten kör SSIS-paket i Azure Data Factory
Den här artikeln beskriver hur du kör ett SSIS-paket i en Azure Data Factory-pipeline med hjälp av en aktivitet kör SSIS-paket. 

## <a name="prerequisites"></a>Förutsättningar

**Azure SQL Database**. I den här artikeln använder en Azure SQL-databas som är värd för SSIS-katalogen. Du kan också använda en Azure SQL Database Managed Instance.

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS integration runtime om du inte har en genom att följa de stegvisa anvisningarna i den [självstudie: distribuera SSIS-paket](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Kör ett paket i Azure portal
I det här avsnittet använder du Användargränssnittet för Data Factory för att skapa Data Factory-pipeline med en köra SSIS-paket-aktivitet som kör ett SSIS-paket.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Första steget är att skapa en datafabrik med hjälp av Azure portal. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Navigera till [Azure-portalen](https://portal.azure.com). 
3. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Namnet är inte tillgängligt – fel](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
    Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds av Data Factory visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas på andra platser.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en aktivitet kör SSIS-paket
I det här steget använder du Användargränssnittet för Data Factory för att skapa en pipeline. Du lägger till en köra SSIS-paket-aktivitet i pipelinen och konfigurera den för att köra SSIS-paket. 

1. I sidan komma igång klickar du på **skapa pipeline**: 

    ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. I den **aktiviteter** verktygslådan Expandera **Allmänt**, och dra och släpp den **köra SSIS-paket** aktiviteten till pipelinedesignytan. 

   ![Dra aktiviteten kör SSIS-paket till designytan](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. På den **Allmänt** fliken av egenskaper för aktiviteten kör SSIS-paket, ange ett namn och beskrivning för aktiviteten. Ange tidsgränsen för valfritt och försök värden.

    ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. På den **inställningar** i Egenskaper för aktiviteten kör SSIS-paket, Välj den Azure-SSIS Integration Runtime som är associerade med den `SSISDB` databasen där paketet har distribuerats. Ange paketsökväg i den `SSISDB` databasen i formatet `<folder name>/<project name>/<package name>.dtsx`. Du kan också ange 32-bitars körning och en fördefinierad eller anpassad loggningsnivå och ger en miljö sökväg i formatet `<folder name>/<environment name>`.

    ![Ange egenskaper på fliken Inställningar](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Verifiera pipeline-konfigurationen genom att klicka på **verifiera** i verktygsfältet. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

6. Publicera pipeline i Data Factory genom att klicka på **publicera alla** knappen. 

### <a name="optionally-parameterize-the-activity"></a>Du kan också Parameterisera aktiviteten

Du kan också tilldela värden, uttryck eller funktioner som kan hänvisa till Data Factory systemvariabler, dina projekt eller paketets parametrar i JSON-format med hjälp av knappen ”Visa källkoden” längst ned på din aktivitet box-enhet för köra SSIS-paket eller ”Code” knappen i det övre högra hörnet av din pipeline-området. Du kan till exempel tilldela Data Factory pipeline-parametrar i projektet SSIS eller paketparametrar som visas i följande skärmbilder:

![Redigera JSON-skript för att köra SSIS-paket-aktivitet](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![Lägga till parametrar till aktiviteten kör SSIS-paket](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

![Lägga till parametrar till aktiviteten kör SSIS-paket](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-the-pipeline"></a>Köra en pipeline
I det här avsnittet ska du utlösa en pipelinekörning och övervaka den. 

1. För att utlösa en pipelinekörning klickar du på **utlösaren** i verktygsfältet och sedan på **Utlös nu**. 

    ![Utlös nu](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du ser pipelinekörningen och dess status tillsammans med annan information (till exempel kör starttid). Om du vill uppdatera vyn klickar du på **Uppdatera**.

    ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Du ser bara en aktivitetskörning eftersom pipelinen har endast en aktivitet (köra SSIS-paket-aktivitet).

    ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Du kan köra följande **fråga** mot SSISDB-databasen i Azure SQL-servern att kontrollera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

    ![Verifiera paketet körningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Du kan också hämta SSISDB körnings-ID från utdata från aktiviteten pipelinekörning och använda det ID: T för att kontrollera mer omfattande körningsloggar och felmeddelanden i SSMS.

    ![Få körnings-ID.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägga en hel pipeline med en utlösare

Du kan också skapa en schemalagd utlösare för din pipeline, så att pipelinen körs enligt ett schema (varje timme, varje dag, osv.). Ett exempel finns i [och skapa en data factory - Användargränssnittet för Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Kör ett paket med PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa Data Factory-pipeline med en köra SSIS-paket-aktivitet som kör ett SSIS-paket. 

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Du kan använda samma data factory som har Azure-SSIS IR, eller så kan du skapa en separat data factory. Följande procedur innehåller steg för att skapa en datafabrik. Du kan skapa en pipeline med en aktivitet för köra SSIS-paket i den här datafabriken. Köra SSIS-paket-aktiviteten kör SSIS-paket. 

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Till exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen
2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. För att skapa datafabriken kör du följande **Set-AzureRmDataFactoryV2**-cmdlet med hjälp av platsen och egenskapen ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en aktivitet kör SSIS-paket 
I det här steget skapar du en pipeline med en aktivitet kör SSIS-paket. Aktiviteten kör SSIS-paket. 

1. Skapa en JSON-fil med namnet **RunSSISPackagePipeline.json** i den **C:\ADF\RunSSISPackage** mapp med innehåll som liknar följande exempel:

    > [!IMPORTANT]
    > Ersätt objektnamn, beskrivningar och sökvägar, rättigheter och parametervärden, lösenord och andra variabelvärden innan du sparar filen. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  I Azure PowerShell växlar du till den `C:\ADF\RunSSISPackage` mapp.

3. Att skapa pipelinen **RunSSISPackagePipeline**, kör den **Set-AzureRmDataFactoryV2Pipeline** cmdlet.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Här är exempel på utdata:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="run-the-pipeline"></a>Köra en pipeline
Använd den **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet för att köra en pipeline. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Du kan också övervaka pipelinen med hjälp av Azure portal. Stegvisa instruktioner finns i [övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägga en hel pipeline med en utlösare
I föregående steg körde du den pipeline på begäran. Du kan också skapa en schemautlösare för att köra pipelinen enligt ett schema (varje timme, varje dag, osv.).

1. Skapa en JSON-fil med namnet **MyTrigger.json** i **C:\ADF\RunSSISPackage** mapp med följande innehåll: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. I **Azure PowerShell**, växla till den **C:\ADF\RunSSISPackage** mapp.
3. Kör den **Set-AzureRmDataFactoryV2Trigger** cmdlet, vilket skapar utlösaren. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Som standard har utlösaren stoppats. Starta utlösaren genom att köra den **Start-AzureRmDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Bekräfta att utlösaren har startats genom att köra den **Get-AzureRmDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Kör följande kommando efter nästa timma. Till exempel om den aktuella tiden är 15:25:00 UTC, kör kommandot på 4 PM UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Du kan köra följande fråga mot SSIDB-databasen i Azure SQL-servern att kontrollera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Se i följande blogginlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
