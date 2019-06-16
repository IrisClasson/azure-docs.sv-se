---
title: Datamodell för Azure Application Insights telemetri | Microsoft Docs
description: Översikt över Application Insights data model
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 749b4077b457eff836ec515f21d97e892e663156
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60899206"
---
# <a name="application-insights-telemetry-data-model"></a>Datamodell för Application Insights-telemetri

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) skickar telemetri från din webbapp till Azure-portalen så att du kan analysera prestanda och användning av ditt program. Telemetri modellen är standardiserat så att det är möjligt att skapa plattform och språkoberoende övervakning. 

Data som samlas in av Application Insights-modeller det här mönstret för körning av vanliga program:

![Application Insights Application Model](./media/data-model/application-insights-data-model.png)

Följande typer av telemetri används för att övervaka körning av din app. Följande tre typer är vanligtvis automatiskt som samlas in av Application Insights SDK från web application framework:

* [**Begäran** ](data-model-request-telemetry.md) - genererade att logga en begäran tas emot av din app. Till exempel Application Insights web SDK genererar automatiskt ett begärandetelemetriobjekt för varje HTTP-begäran som tar emot din webbapp. 

    En **åtgärden** är trådar för körning som bearbetar en begäran. Du kan också [skriva kod](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) att övervaka andra typer av åtgärden, som en ”aktivera” i ett webb-jobb eller fungera som regelbundet bearbetar data.  Varje åtgärd har ett ID. Detta ID som kan användas för att [grupp](../../azure-monitor/app/correlation.md) all telemetri som uppstår när din app är behandlingen av begäran. Varje åtgärd antingen lyckas eller misslyckas och har en tidsperiod.
* [**Undantag** ](data-model-exception-telemetry.md) -representerar vanligen ett undantag som orsakar en åtgärd misslyckas.
* [**Beroende** ](data-model-dependency-telemetry.md) -representerar ett anrop från din app till en extern tjänst eller lagringsenheter, till exempel en REST API eller SQL. I ASP.NET, beroendeanrop till SQL definieras av `System.Data`. Anrop till HTTP-slutpunkter definieras av `System.Net`. 

Application Insights tillhandahåller tre ytterligare datatyper för anpassad telemetri:

* [Spårningen](data-model-trace-telemetry.md) – används antingen direkt eller via en adapter för att implementera diagnostikloggning med hjälp av ett ramverk för instrumentation som är bekant, till exempel `Log4Net` eller `System.Diagnostics`.
* [Händelsen](data-model-event-telemetry.md) – vanligtvis används för att avbilda användarinteraktion med din tjänst, att analysera användningsmönster.
* [Mått](data-model-metric-telemetry.md) – används för att rapporten periodiska skalära mätningar.

Varje telemetriobjekt kan definiera den [kontextinformation](data-model-context.md) som programmet version eller användare sessions-id. Kontexten är en uppsättning starkt typifierad fält som häver blockeringen för vissa scenarier. När programversion har initierats korrekt, kan Application Insights identifiera nya mönster i programmets beteende med omdistributionen. Sessions-id kan användas för att beräkna avbrottet eller ett problem inverkan på användarna. Det gick inte att beräkna Distinkt antal för sessions-id-värden för vissa beroende, fel spårningen eller kritiskt undantag ger en god förståelse av skillnad.

Programmodell insikter telemetri definierar ett sätt att [korrelera](../../azure-monitor/app/correlation.md) telemetri till åtgärden som den är en del. En begäran kan göra en SQL Database-anrop och registreras diagnostik information. Du kan ange Korrelations-kontext för nödvändiga objekt för telemetri att koppla dem till begärandetelemetri.

## <a name="schema-improvements"></a>Förbättringar av schemat

Application Insights-datamodellen är ett enkelt och grundläggande men kraftfullt sätt att utforma din programtelemetri. Vi strävar efter att hålla modellen enkel och smidig för viktiga scenarier och tillåta för att utöka schemat för avancerad användning.

Rapportera data modell eller schemat problem och förslag använder GitHub [ApplicationInsights hem](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) lagringsplats.

## <a name="next-steps"></a>Nästa steg

- [Skriva anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md)
- Lär dig hur du [utöka och filtrera telemetri](../../azure-monitor/app/api-filtering-sampling.md).
- Använd [sampling](../../azure-monitor/app/sampling.md) att minimera mängden telemetri som baseras på datamodellen.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) stöds av Application Insights.
