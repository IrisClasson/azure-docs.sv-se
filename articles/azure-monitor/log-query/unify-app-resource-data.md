---
title: Skapa en enhetlig flera Azure Monitor Application Insights-resurser | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder en funktion i Azure Monitor-loggar att skicka frågor till flera Application Insights-resurser och visualisera data.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424656"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Skapa en enhetlig flera Azure Monitor Application Insights-resurser 
Den här artikeln beskriver hur du fråga efter och visa alla dina Application Insights log programdata på samma plats, även om de finns i olika Azure-prenumerationer, som en ersättning för utfasningen av Application Insights-anslutningsprogram. Antalet resurser Application Insights-resurser som ska inkluderas i en enskild fråga är begränsad till 100.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Rekommenderad metod för att skicka frågor till flera Application Insights-resurser 
Lista över flera Application Insights-resurser i en fråga kan vara besvärligt och svårhanterligt att underhålla. I stället kan du använda funktionen för att avgränsa frågelogiken från program som omfång.  

Det här exemplet visar hur du kan övervaka flera Application Insights-resurser och visualisera antal misslyckade begäranden per programnamn. Innan du börjar måste du köra den här frågan i arbetsytan som är ansluten till Application Insights-resurser för att hämta en lista över anslutna program: 

```
ApplicationInsights
| summarize by ApplicationName
```

Skapa en funktion med hjälp av union-operator med listan över program och spara frågan i din arbetsyta som funktionen med alias *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Du kan ändra de listade programmen när som helst i portalen genom att gå till Query explorer i din arbetsyta och välja funktionen för att redigera och spara sedan eller med hjälp av den `SavedSearch` PowerShell-cmdlet. Den `withsource= SourceApp` kommando lägger till en kolumn till resultatet som betecknar programmet som skickas i loggen. 
>
>Frågan använder Application Insights-schema, även om frågan körs i arbetsytan eftersom applicationsScoping-funktionen returnerar Application Insights-datastruktur. 
>
>Operatorn parsa är valfri i det här exemplet, det extraherar programnamnet från SourceApp egenskapen. 

Du är nu redo att använda applicationsScoping funktion i frågan mellan resurser:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Funktionens alias returnerar unionen av begäranden från alla definierade program. Frågan och sedan filtrerar för misslyckade förfrågningar och hjälper dig att visualisera trender av program.

![Exempel för Cross-frågeresultat](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Fråga efter data i Application Insights-resurser och arbetsytan 
När du har slutat anslutningstjänsten och behovet av att köra frågor på ett tidsintervall som var tas bort av Application Insights-datakvarhållning (90 dagar), måste du utföra [mellan resurser frågor](../../azure-monitor/log-query/cross-workspace-query.md) på arbetsytan och Application Insights resurser för ett mellanliggande period. Det här är tills en publiceringskonfiguration ackumuleras programdata ditt per ny Application Insights-datalagring som nämns ovan. Frågan kräver vissa ändringar eftersom alla scheman i Application Insights och arbetsytan är olika. Se tabellen i avsnittet om du markerar schemaolikheter. 

>[!NOTE]
>[Fråga mellan resurser](../log-query/cross-workspace-query.md) i loggen för aviseringar stöds i den nya [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Azure Monitor använder som standard den [äldre Log Analytics avisering API](../platform/api-alerts.md) för att skapa nya log Varningsregler från Azure-portalen om du växlar från [äldre Log aviseringar API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Efter växeln, nya API: et blir standard för nya aviseringsregler i Azure-portalen och du kan skapa mellan resurser fråga log aviseringar regler. Du kan skapa [mellan resurser fråga](../log-query/cross-workspace-query.md) loggar Varningsregler utan att göra växeln med hjälp av den [ARM-mall för scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – men den här aviseringsregeln kan hanteras dock [ scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) och inte från Azure-portalen.

Om anslutningen inte fungerar längre på 2018-11-01, när du fråga loggar i Application Insights-resurser och program på arbetsytan, skulle frågan konstrueras som i följande exempel:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights och Log Analytics-arbetsyta schemaolikheter
I följande tabell visas schemaolikheter mellan Log Analytics och Application Insights.  

| Logga in egenskaper för Analytics-arbetsyta| Application Insights-resursegenskaper|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Programnamn|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | Varaktighet |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Webbläsare | client_browser |
| Stad | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Land | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| Enhetstyp | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| Åtgärds-ID | operation_id |
| OperationName | operation_Name | 
| Operativsystem | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | Varaktighet | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | Varaktighet | 
| Begärande-ID | id | 
| RequestName | name | 
| RequestSuccess | lyckades | 
| ResponseCode | resultCode | 
| Roll | cloud_RoleName |
| Rollinstans | cloud_RoleInstance |
| sessions-ID | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Nästa steg

Använd [Loggsökning](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad information för Application Insights-appar.
