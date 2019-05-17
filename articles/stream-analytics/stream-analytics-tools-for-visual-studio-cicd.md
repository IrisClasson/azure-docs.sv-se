---
title: Kontinuerlig integrering och utveckla med Azure Stream Analytics CI/CD NuGet-paketet
description: Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NuGet-paketet för att konfigurera en kontinuerlig integrering och distributionsprocessen.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f34139dafffe3d4890f17988114dffdd8b480d2d
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827314"
---
# <a name="continuously-integrate-and-develop-with-azure-stream-analytics-cicd-nuget-package"></a>Kontinuerlig integrering och utveckla med Azure Stream Analytics CI/CD NuGet-paketet
Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NuGet-paketet för att konfigurera en kontinuerlig integrering och distributionsprocessen.

Använd version 2.3.0000.0 eller senare av [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) kan få support för MSBuild.

Det finns ett NuGet-paket: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Den visar MSBuild, lokal körning och distributionsverktyg som stöder kontinuerlig integrering och distribution av [Stream Analytics Visual Studio-projekt](stream-analytics-vs-tools.md). 
> [!NOTE]
> NuGet-paketet kan användas endast med 2.3.0000.0 eller senare version av Stream Analytics Tools för Visual Studio. Om du har projekt som skapats i tidigare versioner av Visual Studio-verktyg kan bara öppna dem med 2.3.0000.0 eller senare version och spara. De nya funktionerna är aktiverade. 

Mer information finns i [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Som standard Visual Studio MSBuild-upplevelsen för att skapa ett projekt har du två alternativ. Du kan högerklicka på projektet och välj sedan **skapa**. Du kan också använda **MSBuild** i NuGet-paketet från kommandoraden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

När ett Stream Analytics Visual Studio-projekt skapas har måste genererar följande två Azure Resource Manager mallfiler under den **bin / [Debug/hos återförsäljare] / distribuera** mapp: 

*  Resource Manager-mallfilen

       [ProjectName].JobTemplate.json 

*  Resource Manager-parameterfilen

       [ProjectName].JobTemplate.parameters.json   

Standardparametrarna i filen parameters.JSON är från inställningarna i Visual Studio-projektet. Ersätt parametrarna i enlighet med detta om du vill distribuera till en annan miljö.

> [!NOTE]
> För alla autentiseringsuppgifterna som standardvärden anges till null. Du är **krävs** du anger värden innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Mer information om hur du [distribuera med en Resource Manager-mallfilen och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Mer information om hur du [använda ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Om du vill använda hanterade identiteter för Azure Data Lake Store Gen1 som utdatamottagare, måste du ge åtkomst till tjänstens huvudnamn med hjälp av PowerShell innan du distribuerar till Azure. Mer information om hur du [distribuera ADLS Gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Kommandoradsverktyget

### <a name="build-the-project"></a>Bygga projektet
NuGet-paketet har ett kommandoradsverktyg som kallas **SA.exe**. Den stöder projekt build och lokal testning på en valfri dator som du kan använda i dina kontinuerlig integrering och kontinuerlig leverans processen. 

Distribution filerna placeras under den aktuella katalogen som standard. Du kan ange sökvägen för utdata med hjälp av parametern - OutputPath följande:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testa skript lokalt

Om ditt projekt har angetts lokala indatafilerna i Visual Studio, kan du köra ett automatiserat skript-test med hjälp av den *localrun* kommando. Det utgående resultatet placeras under den aktuella katalogen.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generera en definitionsfil för jobbet ska användas med Stream Analytics PowerShell-API

Den *arm* kommandot tar jobbmallen och jobbet parametern mallfiler genereras genom build som indata. Sedan kombinerar det dem till en jobbet definition JSON-fil som kan användas med Stream Analytics PowerShell API.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exempel:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa cloud Azure Stream Analytics-jobb i Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Utforska Azure Stream Analytics-jobb med Visual Studio](stream-analytics-vs-tools.md)
