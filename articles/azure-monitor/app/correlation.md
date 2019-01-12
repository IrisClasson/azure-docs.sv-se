---
title: Azure Application Insights Telemetrikorrelation | Microsoft Docs
description: Telemetrikorrelation för Application Insights
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 8b31a85abf1c6034aaff511f23d96fae9ee64561
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230058"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetrikorrelation i Application Insights

I en värld av mikrotjänster kräver varje logisk åtgärd jobbet klart på olika komponenter i tjänsten. Var och en av de här komponenterna separat kan övervakas av [Application Insights](../../azure-monitor/app/app-insights-overview.md). Webbkomponenten appen kommunicerar med autentisering providerns komponent för att verifiera användarens autentiseringsuppgifter och med API-komponenten för att hämta data för visualisering. API-komponenten i sin tur kan fråga efter data från andra tjänster använder cache-provider-komponenter och meddela komponenten fakturering om det här anropet. Application Insights har stöd för distribuerade telemetrikorrelation. Det kan du identifiera vilka komponenten är ansvarig för fel eller försämrade prestanda.

Den här artikeln beskriver den datamodell som används av Application Insights för att korrelera telemetri som skickas av flera komponenter. Den behandlar kontext spridning metoder och protokoll. Den behandlar också implementeringen av Korrelations-koncept på olika språk och plattformar.

## <a name="telemetry-correlation-data-model"></a>Datamodell för telemetri korrelation

Application Insights definierar en [datamodellen](../../azure-monitor/app/data-model.md) för distribuerade telemetrikorrelation. Om du vill associera telemetri med åtgärden logiska varje telemetriobjekt har en kontextfält med namnet `operation_Id`. Den här identifieraren är gemensam för alla telemetriobjekt i distribuerade spårningen. Och med förlust av telemetri från ett lager kan fortfarande du associera telemetri som rapporterats av andra komponenter.

Distribuerad logisk åtgärd består vanligtvis av en uppsättning mindre operations - begäranden som bearbetas av en av komponenterna. Dessa åtgärder definieras av [begär telemetri](../../azure-monitor/app/data-model-request-telemetry.md). Varje begärandetelemetri har sin egen `id` som identifierar det unikt globalt. Och all telemetri - spårningar, undantag och annat som är associerade med den här begäran bör ange den `operation_parentId` till värdet för begäran `id`.

Varje utgående åtgärd (till exempel ett http-anrop till en annan komponent) representeras av [beroendetelemetri](../../azure-monitor/app/data-model-dependency-telemetry.md). Beroendetelemetri definierar också sin egen `id` som är globalt unikt. Begärandetelemetri initieras av det här beroendeanropet använder den som `operation_parentId`.

Du kan skapa vy över distribuerade logiska importförloppet med hjälp av `operation_Id`, `operation_parentId`, och `request.id` med `dependency.id`. Dessa fält kan du också definiera orsakssamband ordningen för telemetri-anrop.

Spårningar från komponenter kan gå till olika lagringsutrymmen i micro-tjänster. Alla komponenter kan ha sin egen instrumenteringsnyckeln i Application Insights. Om du vill hämta telemetri för den logiska åtgärden, måste du köra frågor mot data från varje storage. När antalet lagringsutrymmen är stor, måste ha ett tips på var du vill titta lite närmare.

Application Insights datamodellen definierar två fält för att lösa problemet: `request.source` och `dependency.target`. Det första fältet identifierar den komponent som initierade beroende begäran och andra identifierar vilken komponent returnerade svaret beroende-anropet.


## <a name="example"></a>Exempel

Låt oss ta ett exempel på ett program lager priser som visar det aktuella marknad priset aktier externt API kallas aktier API. PRISER för lager-programmet har en sida `Stock page` öppnas av klienten web browser använder `GET /Home/Stock`. Programmet frågar lager-API med hjälp av ett HTTP-anrop `GET /api/stock/value`.

Du kan analysera resulterande telemetri som en fråga som körs:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

I Obs visa resultat som att alla objekt i telemetrin dela roten `operation_Id`. När ajax-anrop görs från sidan – nytt unikt ID `qJSXU` tilldelas telemetri om beroenden och Sidvisningars ID används som `operation_ParentId`. Serverbegäran använder i sin tur ajax's-ID som `operation_ParentId`osv.

| ItemType   | namn                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| sidvisningar   | Lagerartiklar sidan                |              | STYz               | STYz         |
| beroende | GET/Home/Stock           | qJSXU        | STYz               | STYz         |
| begäran    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| beroende | Hämta /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA=       | STYz         |

Nu när anropet `GET /api/stock/value` görs till en extern tjänst som du vill veta identiteten för servern. Där du kan ange `dependency.target` fältet på rätt sätt. När den externa tjänsten inte stöder övervakning – `target` anges till namnet på tjänsten som `stock-prices-api.com`. Men om tjänsten identifierar sig genom att returnera en fördefinierad HTTP-huvud - `target` innehåller tjänstidentiteten där Application Insights för att skapa distribuerade spårning genom att fråga telemetri från tjänsten. 

## <a name="correlation-headers"></a>Korrelations-huvuden

Vi arbetar på en RFC-förslag för den [korrelation HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Förslaget definierar två rubriker:

- `Request-Id` utföra ett globalt unikt ID för anropet
- `Correlation-Context` -utföra namn värde-par insamlingen av egenskaper för distribuerad spårning

Standarden definierar också två scheman för `Request-Id` generation - platta och hierarkiska. Med fast schema, det är ett välkänt `Id` key som definierats för den `Correlation-Context` samling.

Application Insights definierar den [tillägget](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) för korrelation HTTP-protokollet. Den använder `Request-Context` namn/värdepar för sprida insamlingen av egenskaper som används av den omedelbara anroparen eller mottagaren. Application Insights SDK använder den här rubriken för att ange `dependency.target` och `request.source` fält.

### <a name="w3c-distributed-tracing"></a>W3C distribuerad spårning

Vi övergår till [W3C distribuerad spårning format](https://w3c.github.io/trace-context/). Den definierar:
- `traceparent` -globalt unika åtgärds-ID och unik identifierare för anropet
- `tracestate` -innebär spårning av systemspecifika kontext.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Aktivera stöd för W3C distribuerad spårning för ASP.NET, klassisk appar

Den här funktionen är tillgänglig i Microsoft.ApplicationInsights.Web och Microsoft.ApplicationInsights.DependencyCollector paket från och med version 2.8.0-beta1.
Den har **av** som standard om du vill göra det måste ändra `ApplicationInsights.config`:

* under `RequestTrackingTelemetryModule` lägga till `EnableW3CHeadersExtraction` element med värdet satt till `true`
* under `DependencyTrackingTelemetryModule` lägga till `EnableW3CHeadersInjection` element med värdet satt till `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivera stöd för W3C distribuerad spårning för ASP.NET Core-appar

Den här funktionen är Microsoft.ApplicationInsights.AspNetCore med version 2.5.0-beta1 och Microsoft.ApplicationInsights.DependencyCollector version 2.8.0-beta1.
Den har **av** så att den som standard `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` till `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivera W3C distribuerad spårning av stöd för Java-appar

Inkommande:

**J2EE appar** Lägg till följande till den `<TelemetryModules>` tagg i ApplicationInsights.xml

```xml
<Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
   <Param name = "W3CEnabled" value ="true"/>
   <Param name ="enableW3CBackCompat" value = "true" />
</Add>
```

**Spring Boot-appar** Lägg till följande egenskaper:

`azure.application-insights.web.enable-W3C=true`
`azure.application-insights.web.enable-W3C-backcompat-mode=true`

Utgående:

Lägg till följande AI-Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Bakåtkompatibilitet läge är aktiverat som standard och enableW3CBackCompat-parametern är valfri och bör endast användas när du vill stänga av den. 

Vi rekommenderar är detta fallet när alla tjänster har uppdaterats till nyare version av SDK: er stöder W3C-protokollet. Vi rekommenderar starkt att flytta till nyare version av SDK: er med stöd för W3C så snart som möjligt. 

Se till att båda **inkommande och utgående konfigurationer är exakt samma**.

## <a name="open-tracing-and-application-insights"></a>Öppna spårning och Application Insights

Den [öppen spårning datamodelldata specifikationen](https://opentracing.io/) och Application Insights-datamodeller mappa på följande sätt:

| Application Insights                  | Öppna spårning                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` med `span.kind = server`                  |
| `Dependency`                          | `Span` med `span.kind = client`                  |
| `Id` av `Request` och `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` av typen `ChildOf` (den överordnade span)   |

Läs mer på Application Insights-datamodell, [datamodellen](../../azure-monitor/app/data-model.md). 

Se öppen spårning [specifikationen](https://github.com/opentracing/specification/blob/master/specification.md) och [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) för definitioner av öppen spårning begrepp.

## <a name="telemetry-correlation-in-net"></a>Telemetrikorrelation i .NET

.NET definierats flera olika sätt att korrelera telemetri och diagnostik loggar över tid. Det finns `System.Diagnostics.CorrelationManager` så att du kan spåra [LogicalOperationStack och ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` och Windows ETW definiera metoden [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` använder [Log scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF- och Http under överföring upp ”aktuella” kontexten spridning.

Men inte aktivera automatisk distribuerad spårning stöd för dessa metoder. `DiagnosticsSource` är ett sätt att stödja automatisk mellan datorn korrelation. .NET-bibliotek stöder diagnostik och Tillåt automatisk mellan datorn spridningen av Korrelations-kontexten via transport som http.

Den [guide till aktiviteter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) förklarar grunderna med att spåra aktiviteter i diagnostik källan. 

ASP.NET Core 2.0 har stöd för extrahering av Http-huvuden och starta den nya aktiviteten. 

`System.Net.HttpClient` startversion `4.1.0` stöder automatisk inmatning för korrelationen Http-huvuden och spåra http-anrop som en aktivitet.

Det finns en ny Http-modul [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) för ASP.NET-klassisk. Den här modulen implementerar telemetrikorrelation med DiagnosticsSource. Startar aktivitet baserat på inkommande begärandehuvuden. Det kan även korrelera telemetri från olika faser av begäranbearbetningen. Även för fall när varje steg i IIS-processen körs på en annan hantera trådar.

Application Insights SDK startversion `2.4.0-beta1` använder DiagnosticsSource och aktiviteten för att samla in telemetri och associera den med den aktuella aktiviteten. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetrikorrelation i Java SDK
Den [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md) stöder automatisk korrelation av telemetri från och med version `2.0.0`. Fyller automatiskt `operation_id` för all telemetri (spårningar, undantag, anpassade händelser, osv.) som utfärdade inom omfånget för en begäran. Den också tar hand om sprider Korrelations-huvuden (beskrivs ovan) för tjänst till tjänst-anrop via HTTP om den [Java SDK-agenten](../../azure-monitor/app/java-agent.md) har konfigurerats. Obs: endast anrop som görs via Apache HTTP-klienten har stöd för funktionen korrelation. Om du använder Spring Rest mall eller Feign användas både med Apache HTTP-klient under huven.

För närvarande stöds automatisk kontext spridning över meddelandetekniker (till exempel Kafka, RabbitMQ, Azure Service Bus) inte. Det är möjligt, men att skriva kod sådana scenarier manuellt med hjälp av den `trackDependency` och `trackRequest` API: er, där en beroendetelemetri representerar ett meddelande som köas genom en producent och begäran representerar ett meddelande som bearbetas av en konsument. I det här fallet både `operation_id` och `operation_parentId` ska spridas i meddelandets egenskaper.

<a name="java-role-name"></a>
## <a name="role-name"></a>Rollnamn

Ibland kanske du vill anpassa visningen av Komponentnamn i den [Programkartan](../../azure-monitor/app/app-map.md). Om du vill göra det, kan du manuellt ange den `cloud_RoleName` genom att göra något av följande:

Om du använder Spring Boot med Application Insights Spring Boot starter är den enda önskade ändringen att ange ditt eget namn för programmet i application.properties-filen.

`spring.application.name=<name-of-app>`

Spring Boot starter tilldelas automatiskt cloudRoleName till värdet du anger för egenskapen spring.application.name.

Om du använder den `WebRequestTrackingFilter`, `WebAppNameContextInitializer` anger namnet på programmet automatiskt. Lägg till följande i konfigurationsfilen (ApplicationInsights.xml):
```XML
<ContextInitializers>
  <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
</ContextInitializers>
```

Via molnet kontextklassen:

```Java
telemetryClient.getContext().getCloud().setRole("My Component Name");
```

## <a name="next-steps"></a>Nästa steg

- [Skriva anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md)
- [Läs mer om](../../azure-monitor/app/app-map.md#set-cloudrolename) anger cloud_RoleName för andra SDK: er.
- Publicera alla komponenter i din micro tjänst med Application Insights. Kolla in [plattformar som stöds](../../azure-monitor/app/platforms.md).
- Se [datamodellen](../../azure-monitor/app/data-model.md) för Application Insights och modellen.
- Lär dig hur du [utöka och filtrera telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- [Application Insights config-referens](configuration-with-applicationinsights-config.md)

