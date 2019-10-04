---
title: Använd PowerShell för att skapa och konfigurera en Log Analytics-arbetsyta | Microsoft Docs
description: Log Analytics arbets ytor i Azure Monitor lagra data från servrar i din lokala infrastruktur eller i molnet. Du kan samla in Maskindata från Azure storage när genereras av Azure-diagnostik.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 16cad34290ecc518e95ec1a0ce0950722cfe0780
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836146"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Hantera Log Analytics arbets yta i Azure Monitor med PowerShell

Du kan använda [Log Analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) -cmdletar för att utföra olika funktioner på en Log Analytics arbets yta i Azure Monitor från en kommando rad eller som en del av ett skript.  Exempel på de uppgifter du kan utföra med PowerShell:

* Skapa en arbetsyta
* Lägga till eller ta bort en lösning
* Importera och exportera sparade sökningar
* Skapa en datorgrupp
* Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
* Samla in prestandaräknare från Linux- och Windows-datorer
* Samla in händelser från syslog på Linux-datorer
* Samla in händelser från Windows-händelseloggar
* Samla in anpassade händelseloggar
* Lägg till log analytics-agenten till en Azure-dator
* Konfigurera log analytics för att indexera data som samlas in med Azure-diagnostik

Den här artikeln innehåller två kodexempel som illustrerar några av de funktioner som du kan utföra från PowerShell.  Du kan referera till den [Log Analytics PowerShell cmdlet-referens](https://docs.microsoft.com/powershell/module/az.operationalinsights/) för andra funktioner.

> [!NOTE]
> Log Analytics kallades tidigare för Operational Insights, vilket är anledningen till det är det namn som används i cmdlets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar
Dessa exempel fungerar med version 1.0.0 eller senare av modulen AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Skapa och konfigurera en Log Analytics-arbetsyta
Följande skriptexempel illustrerar hur du:

1. Skapa en arbetsyta
2. Lista över tillgängliga lösningar
3. Lägga till lösningar i arbetsytan
4. Importera sparade sökningar
5. Exportera sparade sökningar
6. Skapa en datorgrupp
7. Aktivera insamling av IIS-loggar från datorer med Windows-agenten installerad
8. Samla in prestandaräknare för logisk Disk från Linux-datorer (% noder i procent; Ledigt utrymme i MB; Använt utrymme; i % Disköverföringar/sek; Diskläsningar/sek; Diskskrivningar/sek)
9. Samla in syslog-händelser från Linux-datorer
10. Samla in händelser för fel och varningar från programmets händelselogg från Windows-datorer
11. Samla in prestandaräknaren för minne tillgängligt, MB från Windows-datorer
12. Samla in en anpassad logg

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
I exemplet ovan har regexDelimiter definierats som "\\n" för ny rad. Logg avgränsaren kan också vara en tidsstämpel.  Följande format stöds:

| Format | JSON regex-formatet använder \\ två för varje \ i ett standard-regex så om testning i en regex \\ -app minskar till \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> två blank steg efter MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> där + är + eller a- <br> där zzzz tids förskjutning | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T är en literal bokstav T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurera Log Analytics för att skicka Azure Diagnostics
För övervakning utan Agent för Azure-resurser, måste resurserna som har Azure-diagnostik aktiverad och konfigurerad för att skriva till en Log Analytics-arbetsyta. Den här metoden skickar data direkt till arbets ytan och kräver inte att data skrivs till ett lagrings konto. Resurser som stöds är:

| Resurstyp | Logs | Mått |
| --- | --- | --- |
| Programgateways    | Ja | Ja |
| Automation-konton     | Ja | |
| Batch-konton          | Ja | Ja |
| Data Lake analytics     | Ja | |
| Data Lake store         | Ja | |
| Elastiska SQL-Pool        |     | Ja |
| Namnområde för händelsehubb     |     | Ja |
| IoT-hubbar                |     | Ja |
| Key Vault               | Ja | |
| Lastbalanserare          | Ja | |
| Logic Apps              | Ja | Ja |
| Nätverkssäkerhetsgrupper | Ja | |
| Azure Cache for Redis             |     | Ja |
| Söktjänster         | Ja | Ja |
| Service Bus-namnområde   |     | Ja |
| SQL (v12)               |     | Ja |
| Webbplatser               |     | Ja |
| Server webbgrupper        |     | Ja |

Information för tillgängliga mått i [stöds mått med Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Information om tillgängliga loggar finns i [tjänster och -schemat stöds för diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Du kan också använda cmdleten föregående för att samla in loggar från resurser som finns i olika prenumerationer. Cmdleten kan fungera mellan prenumerationer eftersom du tillhandahåller ID: t för både resursen som skapar loggar och arbets ytan som loggarna skickas till.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurera Log Analytics arbets yta för att samla in Azure Diagnostics från Storage
Du måste först skriva data till Azure storage för att samla in loggdata från en instans av en klassisk molntjänst eller ett service fabric-kluster som körs. En Log Analytics arbets yta konfigureras sedan för att samla in loggarna från lagrings kontot. Resurser som stöds är:

* Klassiska cloud services (webb- och worker-roller)
* Service fabric-kluster

I följande exempel visas hur du:

1. Lista de befintliga lagrings konton och platser som arbets ytan kommer att indexera data från
2. Skapa en konfiguration för att läsa från ett lagringskonto
3. Uppdatera nyligen skapade konfigurationen att indexera data från fler platser
4. Ta bort den nyligen skapade konfigurationen

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Du kan också använda det här skriptet för att samla in loggar från lagringskonton i olika prenumerationer. Skriptet kan arbeta mellan prenumerationer eftersom du tillhandahåller resurs-ID för lagrings kontot och en motsvarande åtkomst nyckel. När du ändrar att snabbtangent som du behöver uppdatera storage insight om du vill att den nya nyckeln.


## <a name="next-steps"></a>Nästa steg
* [Granska Log Analytics PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.operationalinsights/) för ytterligare information om hur du använder PowerShell för att konfigurera Log Analytics.

