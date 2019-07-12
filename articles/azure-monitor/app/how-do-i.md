---
title: Hur kan jag... i Azure Application Insights | Microsoft Docs
description: Vanliga frågor och svar i Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 9f80edf18a531d6c2850658ddef9c7007edb350f
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795514"
---
# <a name="how-do-i--in-application-insights"></a>Hur kan jag ... i Application Insights?
## <a name="get-an-email-when-"></a>Få ett e-postmeddelande när...
### <a name="email-if-my-site-goes-down"></a>E-post om min plats kraschar
Ange en [tillgänglighet webbtest](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-post om min webbplats är överbelastad
Ange en [avisering](../../azure-monitor/app/alerts.md) på **serversvarstid**. Ett tröskelvärde mellan 1 och 2 sekunder ska fungera.

![](./media/how-do-i/030-server.png)

Din app kan också visa loggar belastning genom att returnera felkoder. Ställa in dataaviseringar på **misslyckade förfrågningar**.

Om du vill ställa in dataaviseringar på **serverundantagen**, du kan behöva göra [vissa ytterligare inställningar](../../azure-monitor/app/asp-net-exceptions.md) om du vill se data.

### <a name="email-on-exceptions"></a>Skicka e-postmeddelande undantag
1. [Konfigurera undantagsövervakning](../../azure-monitor/app/asp-net-exceptions.md)
2. [Ställa in en avisering](../../azure-monitor/app/alerts.md) på undantaget antal mått

### <a name="email-on-an-event-in-my-app"></a>E-post på en händelse i min app
Anta att du vill få ett e-postmeddelande när en viss händelse inträffar. Application Insights ger inte den här funktionen direkt, men det kan [skicka en avisering när ett mått överskrider ett tröskelvärde](../../azure-monitor/app/alerts.md).

Aviseringar kan ställas in på [anpassade mått](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), men inte anpassade händelser. Skriva kod för att öka ett mått när händelsen inträffar:

    telemetry.TrackMetric("Alarm", 10);

Eller:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Eftersom aviseringar har två tillstånd, som du behöver skicka ett lågt värde när du ska välja vilken avisering du vill ha tagit slut:

    telemetry.TrackMetric("Alarm", 0.5);

Skapa ett diagram i [metric explorer](../../azure-monitor/app/metrics-explorer.md) att se din larm:

![](./media/how-do-i/010-alarm.png)

Nu ska du ställa in en avisering utlöses när måttet mer än ett mid värde under en kort period:

![](./media/how-do-i/020-threshold.png)

Ange genomsnittsperioden till minst.

Du får e-postmeddelanden när måttet går över såväl under tröskeln.

Några saker att tänka på:

* En avisering har två tillstånd (”varning” och ”felfri”). Tillståndet utvärderas bara när ett mått tas emot.
* Ett e-postmeddelande skickas bara när tillståndet ändras. Det här är varför du behöver skicka både hög och låg / värde-mått.
* Om du vill utvärdera aviseringen tas medelvärdet av de mottagna värdena under föregående period. Detta sker varje gång ett mått tas emot, så e-postmeddelanden kan skickas oftare än den angivna perioden.
* Eftersom e-postmeddelanden skickas både ”varning” och ”felfri”, kan du tänka på nytt har du funderingar one-shot evenemanget som ett villkor för två tillstånd. Till exempel i stället för en ”jobb slutfört”-händelse, har en ”jobb pågår” villkor, där du får e-postmeddelanden i början och slutet av ett jobb.

### <a name="set-up-alerts-automatically"></a>Ställa in aviseringar automatiskt
[Använd PowerShell för att skapa nya aviseringar](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Använd PowerShell för att hantera Application Insights
* [Skapa nya resurser](../../azure-monitor/app/powershell-script-create-resource.md)
* [Skapa nya aviseringar](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Separata telemetri från olika versioner

* Flera roller i en app: Använda en enda Application Insights-resurs och filtrerar på [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Att avgränsa utveckling, testning och versioner: Använd olika Application Insights-resurser. Hämta instrumenteringsnycklar från web.config. [Läs mer](../../azure-monitor/app/separate-resources.md)
* Reporting build-versioner: Lägga till en egenskap med hjälp av en telemetri-initierare. [Läs mer](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Övervaka backend-servrar och skrivbordsappar
[Använda Windows Server SDK-modulen](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Visualisera data
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Instrumentpanel med mått från flera appar
* I [Metric Explorer](../../azure-monitor/app/metrics-explorer.md), anpassa ditt diagram och spara den som en favorit. Fästa den på instrumentpanelen i Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Instrumentpanel med data från andra källor och Application Insights
* [Exportera telemetri till Power BI](../../azure-monitor/app/export-power-bi.md ).

Eller

* Använd SharePoint som din instrumentpanel som visar data i SharePoint-webbdelar. [Använd löpande export och Stream Analytics för att exportera till SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Använder PowerView för att undersöka databasen och skapa en SharePoint-webbdel för PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrera bort anonym eller autentiserade användare
Om användarna måste logga in, kan du ange den [autentiserad användar-id](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Det inte sker automatiskt.)

Du kan sedan:

* Söker i specifika användar-ID

![](./media/how-do-i/110-search.png)

* Filtrera mått till anonyma och autentiserade användare

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Ändra egenskapsnamn eller värden
Skapa en [filter](../../azure-monitor/app/api-filtering-sampling.md#filtering). På så sätt kan du ändra eller filtrera telemetri innan den skickas från din app till Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista över specifika användare och deras användning
Om du bara vill [Sök efter specifika användare](#search-specific-users), du kan ange den [autentiserad användar-id](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Om du vill ha en lista över användare med data, till exempel vilka sidor du de tittar på eller hur ofta de loggar in, har du två alternativ:

* [Ställ in autentiserat användar-id](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [exportera till en databas](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) och använda lämpliga verktyg för att analysera dina användardata.
* Om du har bara ett litet antal användare kan skicka anpassade händelser eller mått, med hjälp av data av intresse som måttnamnet värde eller händelse och ange användar-id som en egenskap. Ersätt anropet flyttat trackPageView standard JavaScript för att analysera sidvisningar. Använd en telemetri-initierare att lägga till användar-id i all telemetri för att analysera telemetri på serversidan. Därefter kan du filtrera och segmentera mått och sökningar på användar-id.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Minska trafiken från min app till Application Insights
* I [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), inaktivera alla moduler som du inte behöver dessa prestandaräknaren insamlaren.
* Använd [Sampling och filtrering](../../azure-monitor/app/api-filtering-sampling.md) på SDK.
* Begränsa antalet Ajax-anrop som rapporteras för alla sidor i dina webbsidor. I kodfragmentet skript efter `instrumentationKey:...` , infoga: `,maxAjaxCallsPerView:3` (eller ett lämpligt antal).
* Om du använder [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), compute sammanställningen av batchar med måttvärden innan du skickar resultatet. Det finns en överlagring för TrackMetric() som tillhandahåller för som.

Läs mer om [priser och kvoter](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Inaktivera telemetri
Att **dynamiskt stoppa och starta** insamling och överföring av telemetri från servern:

### <a name="aspnet-classic-applications"></a>Klassisk för ASP.NET-program

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Andra program
Det rekommenderas inte att använda `TelemetryConfiguration.Active` singleton på konsolen eller ASP.NET Core-program.
Om du har skapat `TelemetryConfiguration` instansen själv - ange `DisableTelemetry` till `true`.

För ASP.NET Core-program kan du komma åt `TelemetryConfiguration` instans med [ASP.NET Core beroendeinmatning](/aspnet/core/fundamentals/dependency-injection/). Hittar du mer information finns i [ApplicationInsights för ASP.NET Core-program](../../azure-monitor/app/asp-net-core.md) artikeln.

## <a name="disable-selected-standard-collectors"></a>Inaktivera valda standard insamlare
Du kan inaktivera standard-insamlare (till exempel prestandaräknare, HTTP-begäranden eller beroenden)

* **ASP.NET-program** – ta bort eller kommentera ut de relevanta raderna i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core-program** -Följ telemetri moduler konfigurationsalternativ i [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Visa systemprestandaräknare
Bland de mått som du kan visa i metrics explorer är en uppsättning system prestandaräknare. Det finns en fördefinierad bladet med rubriken **servrar** som visar flera stycken.

![Öppna Application Insights-resursen och klicka på servrar](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Om du ser inga prestandaräknardata
* **IIS-server** på din egen dator eller på en virtuell dator. [Installera Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-webbplats** -vi stöder inte prestandaräknare ännu. Det finns flera mått som du kan hämta som en del av Azure-webbplats på Kontrollpanelen.
* **UNIX-server** - [installera insamlade](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Visa fler prestandaräknare
* Först [lägga till ett nytt diagram](../../azure-monitor/app/metrics-explorer.md) och se om räknaren finns i den grundläggande uppsättningen som vi erbjuder.
* Om inte, [lägga till räknaren i uppsättningen som samlas in av modulen för prestandaräknare](../../azure-monitor/app/performance-counters.md).
