---
title: Media Services mått och diagnostiska loggar med Azure Monitor
titleSuffix: Azure Media Services
description: Lär dig hur du övervakar Azure Media Services mått och diagnostikloggar via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 7058160819c9b6ccc3f63511f86e930fcbfc413b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011659"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Övervaka Media Services mått och diagnostikloggar via Azure Monitor

Med [Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina appar presterar. Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer: mått och loggar. Du kan övervaka Media Services diagnostikloggar och skapa aviseringar och aviseringar för insamlade mått och loggar. Du kan visualisera och analysera mått data med hjälp av [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). Du kan skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), exportera dem till [Log Analytics](https://azure.microsoft.com/services/log-analytics/)eller använda tjänster från tredje part.

En detaljerad översikt finns i [Azure Monitor mått](../../azure-monitor/platform/data-platform.md) och [Azure Monitor diagnostikloggar](../../azure-monitor/platform/platform-logs-overview.md).

I det här avsnittet beskrivs [Media Services mått](#media-services-metrics) som stöds och [Media Services diagnostikloggar](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Media Services mått

Mått samlas in med jämna mellanrum oavsett om värdet ändras eller inte. De är användbara för aviseringar eftersom de kan samplas ofta, och en avisering kan utlösas snabbt med relativt enkel logik. Information om hur du skapar mått aviseringar finns i [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Media Services stöder övervaknings mått för följande resurser:

* Konto
* Slutpunkt för direktuppspelning

### <a name="account"></a>Konto

Du kan övervaka följande konto mått.

|Måttnamn|Visningsnamn|Beskrivning|
|---|---|---|
|AssetCount|Antal till gångar|Till gångar i ditt konto.|
|AssetQuota|Till gångs kvot|Till gångs kvot i ditt konto.|
|AssetQuotaUsedPercentage|Använd procent andel till till gångs kvot|Procent andelen av till gångs kvoten som redan används.|
|ContentKeyPolicyCount|Antal nyckel principer för innehåll|Nyckel principer för innehåll i ditt konto.|
|ContentKeyPolicyQuota|Kvot för innehålls nyckel princip|Kvoten för innehålls nyckel principer i ditt konto.|
|ContentKeyPolicyQuotaUsedPercentage|Procent andel av nyckel princip för innehåll|Procent andelen av innehålls nyckelns princip kvot som redan används.|
|StreamingPolicyCount|Antal strömmande principer|Strömmande principer i ditt konto.|
|StreamingPolicyQuota|Kvot för strömmande princip|Kvot för strömning av principer i ditt konto.|
|StreamingPolicyQuotaUsedPercentage|Använd procent andel av princip för strömning|Procent andelen av den strömmande princip kvoten används redan.|

Du bör också granska [konto kvoter och begränsningar](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning

Följande Media Services [slut punkter för direkt uppspelnings](/rest/api/media/streamingendpoints) mått stöds:

|Måttnamn|Visningsnamn|Beskrivning|
|---|---|---|
|Begäranden|Begäranden|Visar det totala antalet HTTP-begäranden som hanteras av slut punkten för direkt uppspelning.|
|Utgående|Utgående|Utgående byte totalt per minut per slut punkt för direkt uppspelning.|
|SuccessE2ELatency|Slutför svars tid för slut punkt till slut punkt|Varaktighet från när strömnings slut punkten fick begäran till när den sista byten av svaret skickades.|

### <a name="why-would-i-want-to-use-metrics"></a>Varför skulle jag vilja använda mått?

Här följer exempel på hur du kan använda övervaknings Media Services mått för att förstå hur dina appar presterar. Några frågor som kan åtgärdas med Media Services mått är:

* Vill du Hur gör jag för att övervaka min standard slut punkt för direkt uppspelning för att veta om jag har överskridit gränserna?
* Hur gör jag för att vet du om det finns tillräckligt med skalnings enheter för slut punkter för direkt uppspelning?
* Hur kan jag ange en avisering för att veta när de ska skala upp mina slut punkter för strömning?
* Hur gör jag för att ange en avisering om du vill veta när det högsta utgående värdet som har kon figurer ATS på kontot uppnåddes?
* Hur kan jag se en nedbrytning av begär Anden som Miss lyckas och vad som orsakar felet?
* Hur kan jag se hur många HLS-eller BINDESTRECKs-begäranden som hämtas från Paketeraren?
* Hur gör jag för att ange en avisering för att veta när tröskelvärdet för antal misslyckade misslyckade förfrågningar träffades?

### <a name="example"></a>Exempel

Se [övervaka Media Services mått](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Media Services diagnostikloggar

Diagnostikloggar ger omfattande och frekventa data om driften av en Azure-resurs. Mer information finns i [så här samlar du in och använder loggdata från dina Azure-resurser](../../azure-monitor/platform/platform-logs-overview.md).

Media Services stöder följande diagnostikloggar:

* Nyckel leverans

### <a name="key-delivery"></a>Nyckel leverans

|Namn|Beskrivning|
|---|---|
|Begäran om nyckel leverans tjänst|Loggar som visar information om viktiga leverans tjänst begär Anden. Mer information finns i [scheman](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Varför skulle jag vilja använda diagnostikloggar?

Några saker som du kan undersöka med nyckel leverans diagnostikloggar är:

* Se antalet licenser som levererats av DRM-typ.
* Se antalet licenser som har levererats av en princip.
* Se fel efter DRM eller princip typ.
* Se antalet otillåtna licens begär Anden från klienter.

### <a name="example"></a>Exempel

Se [så här övervakar du loggar för media service-diagnostik](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Nästa steg

* [Så här samlar du in och använder loggdata från dina Azure-resurser](../../azure-monitor/platform/platform-logs-overview.md)
* [Skapa, visa och hantera måttaviseringar med Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Övervaka Media Services mått](media-services-metrics-howto.md)
* [Övervaka loggar för media service-diagnostik](media-services-diagnostic-logs-howto.md)
