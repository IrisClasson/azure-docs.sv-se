---
title: Enhetlig aviseringar och övervakning i Azure Monitor ersätter klassiska aviseringar och övervakning
description: Översikt över tillbakadragning av klassiska övervakningstjänster och funktioner som tidigare visas i Azure-portalen under aviseringar (klassisk). Klassisk aviseringar och övervakning innehåller klassiska måttaviseringar för Azure-resurser, klassiska måttaviseringar för Application Insights, klassiska webbtest aviseringar för Application Insights, klassiska anpassat mått-baserade aviseringar för Application Insights och klassisk aviseringar för Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9164c335eb85676a419c1cb805b2217ea73c40fe
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497833"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Enhetlig aviseringar och övervakning i Azure Monitor ersätter klassiska aviseringar och övervakning

Azure Monitor har nu blivit en enhetlig fullständig stack övervakningstjänst som nu har stöd för ”ett mått” och ”en aviseringar' mellan resurser. Mer information finns i vår [blogginlägget på den nya Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Den nya Azure övervakning och avisering plattformar har skapats för att vara snabbare, smartare och utökningsbara – att se till att takt med växande expanse med molnbaserad databehandling och i linje med Microsoft Intelligent moln filosofin. 

Med den nya Azure-övervakning och avisering plattform på plats kan vi tas ur bruk ”klassiska” övervakning och avisering plattform - finns i *Visa klassiska aviseringar* avsnitt av Azure-aviseringar **kommer att bli inaktuell från juni 2019**.

 ![Klassiska avisering i Azure-portalen](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Vi uppmanar dig att komma igång och återskapa dina aviseringar i den nya plattformen. För kunder som har ett stort antal aviseringar kan vi arbetar för att ge en automatiserad metod för att flytta befintliga klassiska aviseringar till det nya aviseringar systemet utan avbrott eller lagt till kostnader.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Enhetlig mått och aviseringar i Application Insights

Azure Monitor nyare måttavisering plattformen kommer nu att power övervakning kommer från Application Insights. Flytten innebär Application Insights ska anslutas till åtgärdsgrupper, vilket gör att mycket fler alternativ än bara de föregående e-post och webhook-anrop. Aviseringar kan nu utlösa röstsamtal, Azure Functions, Logic Apps, SMS och ITSM-verktyg som ServiceNow och Automation-Runbooks. Med nästan i realtid övervakning och avisering, kan den nya plattformen Application Insights användare använda samma teknik som driver övervakning över andra Azure-resurser och ligger till grund för övervakning av Microsoft-produkter.

Ny enhetlig övervakning och aviseringar för Application Insights omfattar:

- **Mätvärden för Application Insights-plattformen** – som innehåller populära fördefinierade mått från Application Insights-produkten. Mer information finns i den här artikeln om hur du använder [plattform mätvärden för Application Insights på den nya Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights tillgänglighet och Web test** – vilket ger dig möjlighet att utvärdera svarstider och tillgängligheten för din webbapp eller en server. Mer information finns i den här artikeln om hur du använder [Tillgänglighetstester och aviseringar för Application Insights på den nya Azure Monitor](../../azure-monitor/app/monitor-web-app-availability.md).
- **Mätvärden för Application Insights anpassad** – där du kan definiera och skapa egna mått för övervakning och aviseringar. Mer information finns i den här artikeln om hur du använder [anpassade mått för Application Insights på den nya Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Felavvikelser (del av Smart identifiering) för Application Insights** – som meddelar dig automatiskt i nära realtid om din webbapp upplever en onormal ökning av misslyckade begäranden för HTTP- eller beroendeanrop. Application Insights-Felavvikelser (del av Smart identifiering) som en del av den nya Azure Monitor kommer snart att vara tillgängliga och vi kommer att uppdatera det här dokumentet med länkarna i nästa iteration ännu eftersom den är lanseras under de kommande månaderna.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Enhetlig mått och aviseringar för andra Azure-resurser

Sedan mars 2018 har nästa generation av avisering och flerdimensionella övervakning för Azure-resurser tillgängliga. Nu plattformen för nyare mått och aviseringar är snabbare med funktioner för nästan i realtid. Nyare måttavisering plattform aviseringar ge planet blir mer detaljrikedom, eftersom nyare plattformen har alternativet av dimensioner som gör att du kan segment och filter för att specifikt värde kombination, villkoret eller åtgärden. Precis som alla aviseringar i den nya Azure Monitor är nyare måttaviseringar mer omfattande med hjälp av ActionGroups – så att meddelanden att expandera utöver e-post eller webhook till SMS, röst, Azure-funktion, Automation-Runbook och mycket mer. Mer information finns i [skapa, visa och hantera aviseringar för mått med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
Nyare mätvärden för Azure-resurser är tillgängliga som:

- **Azure Monitor-plattformen standardmått** – som innehåller populära förifyllda mått från olika Azure-tjänster och produkter. Mer information finns i den här artikeln på [stöds mått i Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) och [stöder måttaviseringar på Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor anpassade mått** – som tillhandahåller mått från användarstyrd källor, inklusive Azure Diagnostics-agenten. Mer information finns i den här artikeln på [anpassade mått i Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Med anpassade mått kan du även publicera mätvärden som samlats in av [Windows Azure Diagnostics-agenten](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) och [InfluxData Telegraf agenten](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Tillbakadragning av klassisk övervakning och avisering plattform

Som vi nämnt tidigare, klassisk övervakning och avisering plattform för närvarande användas från den [aviseringar (klassisk) avsnittet](../../azure-monitor/platform/alerts-classic.overview.md) av Azure portal kommer att dras tillbaka i kommande månader får de har ersatts av det nyare systemet.
Äldre klassisk övervakning och avisering tas ur bruk den 30 juni 2019; inklusive stängningen av relaterade API: er, Azure portal-gränssnittet och tjänster i den. Mer specifikt kan att dessa funktioner bli inaktuell:

- Äldre (klassisk) mått och aviseringar för Azure-resurser som för närvarande tillgänglig via [aviseringar (klassisk) avsnittet](../../azure-monitor/platform/alerts-classic.overview.md) av Azure portal, tillgänglig som [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) resurs
- Äldre (klassisk)-plattformen och anpassade mått för Application Insights samt aviseringar på dem som för närvarande tillgänglig via [aviseringar (klassisk) avsnittet](../../azure-monitor/platform/alerts-classic.overview.md) av Azure-portalen och tillgänglig som [microsoft.insights/ alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) resurs
- Äldre (klassisk) Felavvikelser avisering för närvarande tillgängligt som [Smart identifiering i Application Insights](../../azure-monitor/app/proactive-diagnostics.md) på Azure portal, med aviseringar har konfigurerats visas i [aviseringar (klassisk) avsnittet](../../azure-monitor/platform/alerts-classic.overview.md) i Azure Portal

Alla klassiska övervakning och avisering system, inklusive motsvarande [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [Azure-portalsidan](../../azure-monitor/platform/alerts-classic-portal.md), och [ Resursmall](../../azure-monitor/platform/alerts-enable-template.md) förblir användbart fram till slutet av juni 2019. 

I slutet av juni 2019 i Azure Monitor:

- Klassiska tjänsten för övervakning och aviseringar kommer att dras tillbaka och inte längre tillgänglig för att skapa nya Varningsregler
- Varningsregler som förblir desamma i aviseringar (klassisk) utöver juni 2019 fortsätter att köra och utlösa aviseringar, men inte är tillgängliga för ändring av.
- Startar juli 2019 flyttas Varningsregler i klassiska övervakning och avisering automatiskt av Microsoft till motsvarande i den nya Azure monitor-plattformen. Processen att sömlös utan någon avbrottstid och kunder kommer att ha någon minskad övervakning täckning.
- Avisera regler migreras till den nya aviseringar plattformen ger övervakning täckning som tidigare men utlöses meddelande med nya nyttolaster. Någon e-postadress, webhook-slutpunkt eller logic applänk associerad med klassisk aviseringsregel överförs när migreras, men kanske inte fungerar korrekt när aviseringen nyttolast kommer att bli annorlunda i den nya plattformen

> [!IMPORTANT]
> Microsoft Azure Monitor får du Verktyg för kunder att automatiskt migrera klassiska notifieringsregler in på den nya plattformen snart. Och kör den automatiskt för alla klassiska Varningsregler som fortfarande finns, startar juli 2019. Kunderna måste se till att automation konsumerande klassisk aviseringsregel nyttolasten är anpassad för att hantera nya nyttolast från [Unified mått och aviseringar i Application Insights](#unified-metrics-and-alerts-in-application-insights) eller [Unified mått och aviseringar för andra Azure resurser](#unified-metrics-and-alerts-for-other-azure-resources), efter migreringen av de klassiska varningsreglerna. 

Vi kommer snart med verktyg så att du kan migrera dina aviseringar från frivilligt [aviseringar (klassisk) avsnittet](../../azure-monitor/platform/alerts-classic.overview.md) Azure-portalen till de nya Azure-aviseringarna. Alla regler som konfigurerats i aviseringar (klassisk) som har migrerats till den nya Azure Monitor fortsätter att vara kostnadsfria och inte att debiteras. Migrerade klassiska Varningsregler bär också inte någon avgift för push-meddelanden via e-post, webhook eller LogicApp. Användning av typen nyare meddelande eller åtgärd (till exempel SMS, röstsamtal, ITSM-integration, osv.) är dock debiterbar om lagts till i en migrerade eller ny avisering. Mer information finns i [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Dessutom kan följande ska debiteras under omfattas av [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Alla nya (icke-migrerade) varningsregel skapas utöver kostnadsfria enheter på den nya Azure Monitor-plattformen
- Alla data som matas in och returneras efter kostnadsfria enheter som ingår i Azure Monitor
- Alla webbtester för flera test som körs av Application Insights
- Alla anpassade mått som lagras efter kostnadsfria enheter som ingår i Azure Monitor

Den här artikeln uppdateras kontinuerligt med länkar och information om den nya Azure övervakning och avisering funktioner, samt vilka verktyg kan hjälpa användare att börja använda den nya Azure Monitor-plattformen.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om den [nya enhetliga Azure Monitor](../../azure-monitor/overview.md).
* Mer information om den nya [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).

