---
title: Skapa & fråga Azure Data Lake Analytics-PowerShell
description: Använd Azure PowerShell för att skapa Azure Data Lake Analytics-konton och skicka U-SQL-jobb.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: ccee8cef9cfcfc57ac19284b9c42e5c8371aa746
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132559"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure PowerShell för att skapa Azure Data Lake Analytics-konton och sedan skicka och köra U-SQL-jobb. Mer information om Data Lake Analytics finns i [Azure Data Lake Analytics översikt](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du börjar den här självstudiekursen behöver du följande information:

* **Ett Azure Data Lake Analytics-konto**. Se [Kom igång med Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **En arbetsstation med Azure PowerShell**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

## <a name="log-in-to-azure"></a>Logga in på Azure

Den här kursen förutsätter att du redan är bekant med Azure PowerShell. Framför allt behöver du veta hur du loggar in på Azure. Läs [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) om du behöver hjälp.

Logga in med ett prenumerationsnamn:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Du kan också använda ett prenumerations-id för att logga in i stället för prenumerationsnamnet:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Om detta lyckas ser kommandots utdata ut som följande text:

```text
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Förbereda för självstudier

PowerShell-kodfragmenten i den här självstudien använder dessa variabler för att lagra informationen:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Hämta information om ett Data Lake Analytics-konto

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Skicka ett U-SQL-jobb

Skapa en PowerShell-variabel för att lagra U-SQL-skript.

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Skicka skript-text med cmdleten `Submit-AdlJob` och parametern `-Script`.

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Du kan också skicka en skriptfil med parametern `-ScriptPath`:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Hämta status för ett jobb med `Get-AdlJob`. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Istället för att anropa Get-AdlJob om och om igen tills ett jobb blir klart kan du använda cmdleten `Wait-AdlJob`.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Ladda ned utdatafilen med `Export-AdlStoreItem`.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Se även

* Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanterings uppgifter finns i [hantera Azure Data Lake Analytics att använda Azure Portal](data-lake-analytics-manage-use-portal.md).
