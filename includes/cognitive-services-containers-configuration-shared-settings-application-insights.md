---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116721"
---
Den `ApplicationInsights` inställningen kan du lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren. Application Insights erbjuder djupgående övervakning av din behållare. Du kan enkelt övervaka din behållare för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i din behållare.

I följande tabell beskrivs de konfigurationsinställningar som stöds den `ApplicationInsights` avsnittet.

|Krävs| Namn | Datatyp | Beskrivning |
|--|------|-----------|-------------|
|Nej| `InstrumentationKey` | Sträng | Instrumenteringsnyckeln för Application Insights-instans till vilken telemetri skickas data för behållaren. Mer information finns i [Application Insights för ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exempel:<br>`InstrumentationKey=123456789`|

