---
title: Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
description: Lär dig hur du skapar anpassade aktiviteter och använda dem i ett Azure Data Factory-pipelinen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: ea409d6705d0146e9cb32ba11e6b785cf527739c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66165960"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-use-custom-activities.md)
> * [Aktuell version](transform-data-using-dotnet-custom-activity.md)

Det finns två typer av aktiviteter som du kan använda i en Azure Data Factory-pipeline.

- [Dataförflyttningsaktiviteter](copy-activity-overview.md) att flytta data mellan [datalager för källa och mottagare som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- [Datatransformeringsaktiviteter](transform-data.md) för att omvandla data med Beräkningstjänster som Azure HDInsight, Azure Batch och Azure Machine Learning.

För att flytta data till/från ett datalager att Data Factory stöder inte eller för att transformera/bearbeta data på ett sätt som inte stöds av Data Factory, kan du skapa en **anpassad aktivitet** med dina egna dataförflyttning eller omvandling logik och användning aktivitet i en pipeline. Den anpassade aktiviteten körs din anpassade kod logik på ett **Azure Batch** pool med virtuella datorer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se följande artiklar om du är nybörjare på Azure Batch-tjänsten:

* [Azure Batch-grunder](../batch/batch-technical-overview.md) en översikt över Azure Batch-tjänsten.
* [Ny AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet för att skapa ett Batch-konto (eller) [Azure-portalen](../batch/batch-account-create-portal.md) att skapa Azure Batch-konto med hjälp av Azure portal. Se [använda PowerShell för att hantera Azure Batch-konto](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artikeln detaljerade anvisningar om hur du använder cmdlet: en.
* [Ny AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet för att skapa en Azure Batch-pool.

## <a name="azure-batch-linked-service"></a>Tjänsten Azure Batch länkad

Följande JSON definierar ett exempel på Azure Batch-länkad tjänst. Mer information finns i [Compute miljöer som stöds av Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Läs mer om Azure Batch-länkade tjänsten i [länkade tjänster för Compute](compute-linked-services.md) artikeln.

## <a name="custom-activity"></a>Egen aktivitet

Följande JSON-kodfragmentet definierar en pipeline med en enkel anpassad aktivitet. Aktivitetsdefinitionen innehåller en referens till tjänsten Azure Batch länkad.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

I det här exemplet är helloworld.exe ett anpassat program som lagras i mappen customactv2/helloworld för Azure Storage-kontot som används i resourceLinkedService. Den anpassade aktiviteten skickar den här anpassade program som ska köras på Azure Batch. Du kan ersätta kommandot för att alla önskade program som kan köras på måldatorn Azure Batch-poolnoder-operativsystemet.

I följande tabell beskrivs namn och beskrivningar för egenskaper som är specifika för den här aktiviteten.

| Egenskap               | Beskrivning                              | Krävs |
| :-------------------- | :--------------------------------------- | :------- |
| namn                  | Namnet på aktiviteten i pipelinen     | Ja      |
| description           | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                  | Anpassad aktivitet aktivitetstyp av är **anpassad**. | Ja      |
| linkedServiceName     | Länkad tjänst till Azure Batch. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln.  | Ja      |
| Kommandot               | Kommandot i anpassade program som ska köras. Om programmet redan är tillgängliga på Azure Batch Pool Node, kan resourceLinkedService och folderPath hoppas över. Du kan till exempel ange kommandot för att vara `cmd /c dir`, som stöds internt av Batch-Pool för Windows-nod. | Ja      |
| resourceLinkedService | Azure Storage länkade tjänsten till det lagringskonto där programmet lagras | Nej&#42;       |
| folderPath            | Sökvägen till mappen för anpassade program och alla dess beroenden<br/><br/>Om du har beroenden som lagras i undermappar – det vill säga i en hierarkisk mappstruktur under *folderPath* -mappstrukturen för närvarande förenklas när filerna har kopierats till Azure Batch. Det vill säga kopieras alla filer till en enda mapp med utan undermappar. Överväg att komprimerar filerna, kopiera den komprimerade filen och packat upp den med anpassad kod i önskad plats för att undvika problemet. | Nej&#42;       |
| referenceObjects      | En matris med befintliga länkade tjänster och datauppsättningar. Refererade länkade tjänster och datauppsättningar som skickas till det anpassa programmet i JSON-format så att din anpassade kod kan hänvisa till resurser av Data Factory | Nej       |
| extendedProperties    | Användardefinierade egenskaper som kan skickas till det anpassa programmet i JSON-format så att din anpassade kod kan referera till ytterligare egenskaper | Nej       |
| retentionTimeInDays | Kvarhållningstid för filer som skickats för anpassad aktivitet. Standardvärdet är 30 dagar. | Nej |

&#42;Egenskaperna `resourceLinkedService` och `folderPath` måste antingen anges eller båda utelämnas.

> [!NOTE]
> Om du skickar länkade tjänster som referenceObjects i anpassad aktivitet, är det en bra säkerhetsrutin att skicka ett Azure Key Vault aktiverat länkad tjänst (eftersom den inte innehåller någon säker strängar) och hämta autentiseringsuppgifterna med hemligt namn direkt från nyckel Valvet från koden. Du kan se ett exempel [här](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) att referenser AKV aktiverade länkade tjänsten hämtar autentiseringsuppgifterna från Key Vault och sedan får åtkomst till lagringen i koden.

## <a name="custom-activity-permissions"></a>Behörigheter för anpassad aktivitet

Den anpassade aktiviteten anger Azure Batch-kontot automatiskt användare till *inte är administratörer åtkomst med uppgiften omfattning* (standard automatiskt användare specifikation). Du kan inte ändra automatisk-användarkontot behörighetsnivå. Mer information finns i [kör aktiviteter på användarkonton i Batch | Automatisk användarkonton](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Kör kommandon

Du kan direkt köra ett kommando med en anpassad aktivitet. I följande exempel körs kommandot ”echo hello world” på Azure Batch-Pool målnoder och skriver ut utdata till stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Skicka objekt och egenskaper

Detta exempel visar hur du kan använda referenceObjects och extendedProperties för att skicka Data Factory-objekt och användardefinierade egenskaper till ditt anpassade program.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

När aktiviteten körs lagras referenceObjects och extendedProperties i följande filer som har distribuerats till samma mapp för körning av SampleApp.exe:

- `activity.json`

  Lagrar extendedProperties och egenskaperna för den anpassade aktiviteten.

- `linkedServices.json`

  Lagrar en matris med länkade tjänster som har definierats i egenskapen referenceObjects.

- `datasets.json`

  Lagrar en matris med datauppsättningar som har definierats i egenskapen referenceObjects.

Följande exempelkod visa hur SampleApp.exe kan komma åt informationen som krävs från JSON-filer:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Hämta utdata från körningen

Du kan starta en pipelinekörning med hjälp av följande PowerShell-kommando:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Om pipelinen är igång kan kontrollera du utförande-utdatan med hjälp av följande kommandon:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

Den **stdout** och **stderr** av ditt anpassade program sparas i den **adfjobs** behållare i Azure Storage länkade tjänsten du definierade när du skapar Azure Batch länkade Tjänsten med en GUID för uppgiften. Du kan få detaljerad sökväg från aktiviteten kör utdata som visas i följande kodavsnitt:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Om du vill använda innehåll för stdout.txt i underordnade aktiviteter kan du hämta sökvägen till filen stdout.txt i uttrycket ”\@activity('MyCustomActivity').output.outputs [0]”.

> [!IMPORTANT]
> - Den activity.json och linkedServices.json datasets.json lagras i mappen körning av Batch-aktiviteter. I det här exemplet lagras den activity.json och linkedServices.json datasets.json i ”https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/” sökväg. Om det behövs kan behöva du rensa dem separat.
> - För länkade tjänster som använder den lokala Integration Runtime definierats känslig information som nycklar eller lösenord krypteras med den lokala Integration Runtime för att se till att autentiseringsuppgifterna är kvar i kundens privata nätverk. Vissa känsliga fält kan vara saknas när de refereras av din anpassade programkoden i det här sättet. Använd SecureString i extendedProperties istället för att använda länkade tjänstreferensen om det behövs.

## <a name="pass-outputs-to-another-activity"></a>Pass utdata till en annan aktivitet

Du kan skicka anpassade värden från din kod i en anpassad aktivitet tillbaka till Azure Data Factory. Du kan göra det genom att skriva dem till `outputs.json` från ditt program. Data Factory kopierar innehållet i `outputs.json` och lägger till dem i utdata för aktiviteten som värde för den `customOutput` egenskapen. (Storleksgränsen är 2MB.) Om du vill använda innehåll av `outputs.json` i underordnade aktiviteter kan du hämta värdet med uttrycket `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Hämta SecureString-utdata

Känsliga egenskapsvärden som är utsedd till typen *SecureString*, enligt vissa av exemplen i den här artikeln maskeras ut på fliken övervakning i Data Factory-användargränssnittet.  I faktiska pipeline-åtgärd, men en *SecureString* egenskap serialiserad som JSON inom den `activity.json` filen som oformaterad text. Exempel:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Den här serialiseringen är inte helt säkert och är inte avsedd att vara säkra. Avsikten är att tips till Data Factory för att maskera värdet i fliken övervakning.

Komma åt egenskaper av typen *SecureString* från en anpassad aktivitet läsa den `activity.json` -fil som placeras i samma mapp som din. EXE, deserialisera JSON och öppna JSON-egenskap (extendedProperties = > [egenskapsnamn] = > värde).

## <a name="compare-v2-v1"></a> Jämför v2 anpassad aktivitet och version 1 (anpassad) DotNet-aktivitet

I Azure Data Factory version 1 kan du implementera en (anpassad) DotNet-aktivitet genom att skapa ett projekt för .NET-klassbiblioteket med en klass som implementerar den `Execute` -metoden för den `IDotNetActivity` gränssnitt. Den länkade tjänster, datauppsättningar och utökade egenskaper i JSON-nyttolast med en (anpassad) DotNet-aktivitet skickas till metoden körning som starkt typifierade objekt. Mer information om beteendet version 1 finns [(anpassad) DotNet i version 1](v1/data-factory-use-custom-activities.md). På grund av den här implementeringen har din version 1 DotNet-Aktivitetskod att fokusera på .NET Framework 4.5.2. Version 1 DotNet aktiviteten har också köras på Windows-baserade Azure Batch-Pool-noder.

I Azure Data Factory V2 anpassade aktiviteten behöver du inte implementera ett .NET-gränssnitt. Du kan nu direkt köra kommandon, skript och din egen kod, kompilerad som en körbar fil. Om du vill konfigurera den här implementeringen, anger du den `Command` egenskapen tillsammans med den `folderPath` egenskapen. Den anpassade aktiviteten laddar upp den körbara filen och dess beroenden till `folderpath` och kör kommandot för dig.

Länkade tjänster, datauppsättningar (definieras i referenceObjects) och utökade egenskaper som definierats i JSON-nyttolasten för en Data Factory v2 anpassad aktivitet kan nås av den körbara filen som JSON-filer. Du kan komma åt de nödvändiga egenskaperna med hjälp av en JSON-serialisering enligt föregående SampleApp.exe kodexemplet.

Med ändringar som införs i den anpassade aktiviteten för Data Factory V2, kan du skriva anpassad kod logik på ditt språk och köra den på Windows och Linux-operativsystem som stöds av Azure Batch.

I följande tabell beskrivs skillnaderna mellan Data Factory V2 anpassad aktivitet och Data Factory version 1 (anpassad) DotNet-aktivitet:

|Skillnader      | Anpassad aktivitet      | version 1 (anpassad) DotNet-aktivitet      |
| ---- | ---- | ---- |
|Hur anpassad logik har definierats      |Genom att tillhandahålla en körbar fil      |Genom att implementera ett .NET-DLL      |
|Körningsmiljö för den anpassade logiken      |Windows- eller Linux      |Windows (.NET Framework 4.5.2)      |
|Kör skript      |Har stöd för körning av skript direkt (till exempel ”cmd /c echo hello world” på Windows virtuell dator)      |Kräver implementering i .NET-DLL      |
|Datauppsättning som krävs      |Valfritt      |Krävs för att länka aktiviteter och skickar information      |
|Skicka information från aktiviteten till anpassad logik      |Via ReferenceObjects (LinkedServices och datauppsättningar) och ExtendedProperties (anpassade egenskaper)      |Via ExtendedProperties (anpassade egenskaper), indata och utdata datauppsättningar      |
|Hämta information i anpassad logik      |Parsar activity.json och linkedServices.json datasets.json som lagras i samma mapp på den körbara filen      |Via .NET SDK (.NET bildruta 4.5.2)      |
|Loggning      |Skriver direkt till STDOUT      |Implementera loggaren i .NET-DLL      |

Om du har befintliga .NET-kod som skrivits för en version 1 (anpassad) DotNet-aktiviteten kan behöva du ändra koden att fungera med den aktuella versionen av den anpassade aktiviteten. Uppdatera din kod genom att följa riktlinjerna på hög nivå:

  - Ändra projektet från en .NET-klassbiblioteket till en Konsolapp.
  - Starta programmet med den `Main` metoden. Den `Execute` -metoden för den `IDotNetActivity` gränssnittet inte längre behövs.
  - Läsa och tolka länkade tjänster, datauppsättningar och aktivitet med en JSON-serialisering, men inte starkt typifierade objekt. Skicka värdena för obligatoriska egenskaper för dina viktigaste anpassad kod logik. Referera till föregående SampleApp.exe kod som ett exempel.
  - Loggaren-objektet stöds inte längre. Utdata från den körbara filen kan skrivas till konsolen och sparas på stdout.txt.
  - Det krävs inte längre Microsoft.Azure.Management.DataFactories NuGet-paketet.
  - Kompilera koden, överför den körbara filen och dess beroenden till Azure Storage och definiera sökvägen i den `folderPath` egenskapen.

Ett mer komplett exempel på hur slutpunkt till slutpunkt DLL-filen och pipeline exemplet beskrivs i Data Factory version 1 artikeln [Använd anpassade aktiviteter i en Azure Data Factory-pipeline](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) kan skrivas om som en Data Factory-anpassad aktivitet, se [ Data Factory anpassad aktivitet, exempel](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning i Azure Batch

Du kan också skapa ett Azure Batch-pool med **Autoskala** funktionen. Du kan till exempel skapa en azure batch-pool med 0 dedikerade virtuella datorer och en formel för automatisk skalning baserat på antalet väntande aktiviteter.

Exemplet formeln här uppnår på följande: När poolen skapas, börjar det med 1 virtuell dator. $PendingTasks mått definierar antalet uppgifter i körs + aktiv (köad) tillstånd. Formeln hittar det genomsnittliga antalet väntande aktiviteter de senaste 180 sekunderna och anger TargetDedicated därefter. Det innebär att TargetDedicated aldrig är mer omfattande än 25 virtuella datorer. Så när nya aktiviteter skickas pool växer automatiskt och som aktiviteterna slutförs kan virtuella datorer blir kostnadsfria en i taget och autoskalning minskar storleken på de virtuella datorerna. startingNumberOfVMs och maxNumberofVMs kan justeras efter dina behov.

Formel för automatisk skalning:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Se [skala beräkningsnoder automatiskt i en Azure Batch-pool](../batch/batch-automatic-scaling.md) mer information.

Om poolen använder standard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch-tjänsten kan ta 15-30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten. Om poolen använder en annan autoScaleEvaluationInterval, kan Batch-tjänsten ta autoScaleEvaluationInterval + 10: e minut.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
