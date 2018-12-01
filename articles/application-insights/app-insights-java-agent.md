---
title: Övervakning av programprestanda för Java-webbappar i Azure Application Insights | Microsoft Docs
description: Utökad övervakning av prestanda och användning av din Java-webbplats med Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: c5a98b9182e3a644a808b363df2fd44b115828f0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727706"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Övervaka beroenden, undantag som fångats och metoden körningstider i Java-webbappar


Om du har [instrumenterats Java-webbapp med Application Insights][java], du kan använda Java-agenten för att få bättre insikter, utan några kodändringar:

* **Beroenden:** Data om anrop som programmet gör för andra komponenter, inklusive:
  * **REST-anrop** görs via HttpClient, OkHttp och RestTemplate (Spring) avbildas.
  * **Redis** anrop som görs via Jedis klienten fångas.
  * **[JDBC-anrop](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  -MySQL-, SQL Server- och Oracle DB-kommandon automatiskt avbildas. För MySQL om anropet tar längre tid än 10s, rapporterar agenten frågeplanen.
* **Påträffades undantag:** Information om undantag som hanteras av din kod.
* **Metoden körningstid:** Information om tid det tar för att köra specifika metoder.

Om du vill använda Java-agenten måste installera du den på din server. Web apps måste vara utrustade med det [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installera Application Insights-agenten för Java
1. På datorn som du kör din Java-server, [ladda ned agenten](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Kontrollera att för att hämta samma verson av Java-agenten som Application Insights Java SDK core och webb-paket.
2. Redigera startskriptet application server och Lägg till följande JVM:
   
    `javaagent:`*fullständig sökväg till agenten JAR-filen*
   
    Till exempel på Tomcat på en Linux-dator:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starta om programservern.

## <a name="configure-the-agent"></a>Konfigurera agenten
Skapa en fil med namnet `AI-Agent.xml` och placera den i samma mapp som agenten JAR-filen.

Ange innehållet i xml-filen. Redigera i följande exempel för att ta med eller utelämna funktionerna du vill.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Du måste aktivera rapporter undantag och metoden val av tidpunkt för enskilda metoder.

Som standard `reportExecutionTime` är SANT och `reportCaughtExceptions` är FALSKT.

## <a name="view-the-data"></a>Visa data
Sammanställda remote beroende- och metoden körningstider visas i Application Insights-resurs [under panelen prestanda][metrics].

För att söka efter enskilda instanser av beroende, undantag, och metoden rapporter, öppna [Search][diagnostic].

[Diagnostisera beroendeproblem - Läs mer](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Har du några frågor? Har du problem?
* Ser du inga data? [Brandväggsundantag för set](app-insights-ip-addresses.md)
* [Felsöka Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
