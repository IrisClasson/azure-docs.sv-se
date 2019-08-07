---
title: Azure Application Insights Snapshot Debugger för .NET-appar | Microsoft Docs
description: Felsök ögonblicksbilder automatiskt samlas in när undantag utlöses i produktion .NET-appar
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 08/06/2019
ms.author: mbullwin
ms.openlocfilehash: 02d72ab877577e97592dfdd763a58cb01b201d8b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839366"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Felsök ögonblicksbilder på undantag i .NET-appar
Om ett undantag inträffar, kan du automatiskt samla in en felsökning för ögonblicksbilder från ditt webbprogram. Ögonblicksbilden visar tillståndet för källkoden och variabler för tillfället som undantaget uppstod. Felsökning av ögonblicksbild (förhandsversion) i [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) övervakar undantagstelemetri från din webbapp. Den samlar in ögonblicksbilder i din upp utlöser undantag så att du har den information du behöver att diagnostisera problem i produktionsmiljön. Inkludera den [Snapshot collector NuGet-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) i ditt program och om du vill konfigurera insamlingsparametrarna i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Ögonblicksbilder som visas på [undantag](../../azure-monitor/app/asp-net-exceptions.md) i Application Insights-portalen.

Du kan visa debug ögonblicksbilder i portalen för att se anropet stacken och inspektera variabler vid varje anropsstacken. För att få en mer kraftfull fel söknings upplevelse med källkod öppnar du ögonblicks bilder med Visual Studio 2019 Enterprise. I Visual Studio kan du också [ange Snappoints att interaktivt ta ögonblicksbilder](https://aka.ms/snappoint) utan att behöva vänta ett undantag.

Felsök ögonblicksbilder lagras i sju dagar. Den här bevarandeprincipen är inställd på basis av per program. Om du vill öka det här värdet kan du begära en ökning genom att öppna ett supportärende i Azure-portalen.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Aktivera Application Insights Snapshot Debugger för ditt program
Ögonblicksbild samling är tillgängligt för:
* .NET framework och ASP.NET-program med .NET Framework 4.5 eller senare.
* .NET core 2.0 och ASP.NET Core 2.0-program som körs på Windows.

Följande miljöer stöds:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör OS-familjen 4 eller senare
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som körs på Windows Server 2012 R2 eller senare
* [Azure-Virtual Machines och skalnings uppsättningar för virtuella datorer som](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) kör Windows Server 2012 R2 eller senare
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör Windows Server 2012 R2 eller senare

> [!NOTE]
> Klientprogram (till exempel WPF, Windows-formulär eller UWP) stöds inte.

Om du har aktiverat Snapshot Debugger men inte ser ögonblicks bilder, se vår [fel söknings guide](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Bevilja behörigheter

Åtkomst till ögonblicksbilder är skyddat av rollbaserad åtkomstkontroll (RBAC). Om du vill kontrollera en ögonblicksbild, måste du först läggas till rollen krävs av en prenumerationsägare.

> [!NOTE]
> Ägare och deltagare har inte den här rollen automatiskt. Om de vill visa ögonblicksbilder måste de lägga till sig själva till rollen.

Prenumerationsägare ska tilldela den `Application Insights Snapshot Debugger` roll till användare som inspektera ögonblicksbilder. Den här rollen kan tilldelas till enskilda användare eller grupper av prenumerationsägare för målets Application Insights-resursen eller dess resursgrupp eller prenumeration.

1. Gå till Application Insights-resurs i Azure-portalen.
1. Klicka på **åtkomstkontroll (IAM)** .
1. Klicka på den **+ Lägg till rolltilldelning** knappen.
1. Välj **Application Insights Snapshot Debugger** från den **roller** listrutan.
1. Sök efter och ange ett namn för att lägga till.
1. Klicka på den **spara** för att lägga till användaren till rollen.


> [!IMPORTANT]
> Ögonblicksbilder kan innehålla personligt och annan känslig information i variabeln och parametervärden.

## <a name="view-snapshots-in-the-portal"></a>Visa ögonblicks bilder i portalen

När ett undantag har inträffat i ditt program och en ögonblicks bild har skapats bör du ha ögonblicks bilder att visa. Det kan ta 5 till 10 minuter från ett undantag som inträffar till en ögonblicks bild som är klar och kan visas från portalen. Om du vill visa ögonblicks bilder väljer du knappen **åtgärder** i rutan **fel** när du visar fliken **åtgärder** , eller så klickar du på knappen **undantag** när du visar fliken **undantag** :

![Sidan problem](./media/snapshot-debugger/failures-page.png)

Välj en åtgärd eller ett undantag i den högra rutan för att öppna fönstret **transaktions information från slut punkt till slut punkt** och välj sedan undantags händelsen. Om det finns en ögonblicks bild för det här undantaget visas en knapp för att **Öppna fel söknings ögonblicks bilder** i den högra rutan med information om [undantaget](../../azure-monitor/app/asp-net-exceptions.md).

![Öppna Felsök ögonblicksbild knappen undantag](./media/snapshot-debugger/e2e-transaction-page.png)

I vyn Felsök ögonblicksbild visas en anropsstack och en variabler-fönstret. När du väljer bildrutor i anropsstacken i fönstret anrop stack, du kan visa lokala variabler och parametrar för funktionen anropa i fönstret variabler.

![Visa Felsök ögonblicksbilder i portal](./media/snapshot-debugger/open-snapshot-portal.png)

Ögonblicksbilder kan innehålla känslig information och som standard de inte kan visas. Om du vill visa ögonblicksbilder, måste du ha den `Application Insights Snapshot Debugger` roll som tilldelats dig.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visa ögonblicks bilder i Visual Studio 2017 Enterprise eller senare
1. Klicka på knappen **Hämta ögonblicks bild** för `.diagsession` att ladda ned en fil som kan öppnas av Visual Studio Enterprise.

2. Du måste ha `.diagsession` installerat Snapshot debugger Visual Studio-komponenten för att kunna öppna filen. Snapshot Debugger-komponenten är en obligatorisk komponent i ASP.net-arbetsbelastningen i Visual Studio och kan väljas från listan med enskilda komponenter i Visual Studio-installationsprogrammet. Om du använder en version av Visual Studio innan du börjar med Visual Studio 2017 version 15,5 måste du installera tillägget från [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. När du har öppnat ögonblicksbildfilen visas sidan Minidump felsökning i Visual Studio. Klicka på **felsöka förvaltad kod** att starta felsökningen ögonblicksbilden. Ögonblicksbilden öppnar rad med kod som undantaget uppstod där så att du kan felsöka det aktuella tillståndet för processen.

    ![Visa ögonblicksbild för felsökning i Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Hämtade ögonblicksbilden innehåller symbolfiler som hittades på webbservern för programmet. Symbolfilerna krävs för att associera ögonblicksbilddata med källkoden. Se till att aktivera symbolen distribution när du publicerar dina web apps för App Service-appar.

## <a name="how-snapshots-work"></a>Så här fungerar ögonblicksbilder

Snapshot Collector implementeras som en [Application Insights Telemetry Processor](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). När programmet körs, läggs Snapshot Collector telemetri processorn till programmets telemetri pipeline.
Varje gång dina program anrop [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), Snapshot Collector beräknar ett Problem-ID från vilken typ av undantag som genereras och metoden utlösande.
Varje gång programmet anropar TrackException, ökar en räknaren stegvis för lämpliga problemet-ID. När räknaren når den `ThresholdForSnapshotting` värde, Problem-ID läggs till i en samling-Plan.

Snapshot Collector övervakar även undantag när de är utlöst genom att prenumerera på den [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) händelse. När händelsen utlöses beräknas Problem-ID för undantaget och jämföra mot Problem-ID: N i samlingen Plan.
Om det finns en matchning, skapas en ögonblicksbild av processen som körs. Ögonblicksbilden har tilldelats en unik identifierare och undantaget är stämplad med en identifierare. När FirstChanceException hanteraren returnerar bearbetas undantaget utlöses som vanligt. Undantaget når så småningom igen metoden TrackException där den, tillsammans med ögonblicksbild-ID har rapporterats till Application Insights.

Den huvudsakliga processen fortsätter att köra och hantera trafik till användare med lite avbrott. Under tiden kan skickas ögonblicksbilden till ögonblicksbild Gilgit-processen. Ögonblicksbild-överförare skapar en minidump och överför dem till Application Insights tillsammans med alla relevanta symbolfiler (.pdb).

> [!TIP]
> - En process ögonblicksbild är en pausad klon av processen.
> - Det tar cirka 10 till 20 millisekunder för att skapa ögonblicksbilden.
> - Standardvärdet för `ThresholdForSnapshotting` är 1. Detta är också det minsta värdet. Därför kan din app har att utlösa samma undantag **två gånger** innan en ögonblicksbild skapas.
> - Ange `IsEnabledInDeveloperMode` till true om du vill generera ögonblicksbilder vid felsökning i Visual Studio.
> - Frekvensen för ögonblicksbilder skapas begränsas av de `SnapshotsPerTenMinutesLimit` inställningen. Som standard är gränsen en ögonblicksbild varje tio minuter.
> - Mer än 50 ögonblicksbilder per dag kan laddas upp.

## <a name="limitations"></a>Begränsningar

Standardvärdet för kvarhållning av data är 15 dagar. För varje Application Insights instans tillåts ett maximalt antal 50 ögonblicks bilder per dag.

### <a name="publish-symbols"></a>Publicera symboler
Snapshot Debugger kräver symbolfiler på produktionsservern att avkoda variabler och felsöka problem i Visual Studio.
Version 15.2 (eller senare) för Visual Studio 2017 publicerar symboler för versionen skapar som standard när du publicerar den till App Service. I tidigare versioner måste du lägga till följande rad i din publiceringsprofil `.pubxml` filen så att symboler som publiceras i Produktionsläge:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

För Azure Compute och andra typer, se till att symbolfiler finns i samma mapp med huvudprogrammet DLL-filen (vanligtvis `wwwroot/bin`) eller är tillgängliga på den aktuella sökvägen.

> [!NOTE]
> Mer information om de olika symbol alternativen som är tillgängliga finns [i Visual Studio-dokumentationen.](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
) För bästa resultat rekommenderar vi att du använder "fullständig", "portabel" eller "inbäddad".

### <a name="optimized-builds"></a>Optimerad versioner
I vissa fall kan visas lokala variabler inte i version versioner på grund av optimeringar av JIT-kompilatorn.
I Azure App Services, kan dock Snapshot Collector deoptimize utlösande metoder som är en del av dess Plan för datainsamling.

> [!TIP]
> Installera Application Insights-Platstillägget i din App Service för att få deoptimization support.

## <a name="next-steps"></a>Nästa steg
Aktivera Application Insights Snapshot Debugger för ditt program:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Utöver Application Insights Snapshot Debugger:
 
* [Ange snappoints i din kod](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) att hämta ögonblicksbilder utan att behöva vänta ett undantag.
* [Diagnostisera undantag i dina webbappar](../../azure-monitor/app/asp-net-exceptions.md) förklarar hur du gör flera undantag som är synliga för Application Insights.
* [Smart identifiering](../../azure-monitor/app/proactive-diagnostics.md) identifierar automatiskt prestandaavvikelser.
