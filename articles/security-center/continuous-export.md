---
title: Exportera Azure Security Center aviseringar och rekommendationer till Siem | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar kontinuerlig export av säkerhets aviseringar och rekommendationer till Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: d101acd3e72e68efd9198cb273fd352967a0cd54
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192377"
---
# <a name="export-security-alerts-and-recommendations"></a>Exportera säkerhetsaviseringar och rekommendationer

Azure Security Center genererar detaljerade säkerhets aviseringar och rekommendationer. Du kan visa dem i portalen eller med programmerings verktyg. Du kan också behöva exportera den här informationen eller skicka den till andra övervaknings verktyg i din miljö. 

I den här artikeln beskrivs de verktyg som du kan använda för att exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

Med de här verktygen kan du:

* Exportera kontinuerligt till Log Analytics arbets ytor
* Exportera kontinuerligt till Azure Event Hubs (för integreringar med Siem från tredje part)
* Exportera till CSV (en tid)



## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig|
|Priset|Kostnadsfri nivå|
|Nödvändiga roller och behörigheter:|**Rollen säkerhets administratör** i resurs gruppen (eller **ägaren**)<br>Måste också ha Skriv behörighet för mål resursen|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nej](./media/icons/no-icon.png) Kina gov, andra gov|
|||



## <a name="setting-up-a-continuous-export"></a>Konfigurera en löpande export

Stegen nedan är nödvändiga om du konfigurerar en kontinuerlig export till Log Analytics arbets yta eller Azure Event Hubs.

1. Välj **pris & inställningar**från Security Center marginal List.

1. Välj den prenumeration som du vill konfigurera data exporten för.
    
1. Från List rutan på sidan Inställningar för den prenumerationen väljer du **löpande export**.

    [ ![ Exportera alternativ i Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) här visas export alternativen. Det finns en flik för varje tillgängligt export mål. 

1. Välj den datatyp som du vill exportera och välj bland filtren för varje typ (till exempel endast exportera aviseringar med hög allvarlighets grad).

1. I området "Exportera mål" väljer du var du vill spara data. Data kan sparas i ett mål för en annan prenumeration (till exempel på en central Event Hub-instans eller en central Log Analytics-arbetsyta).

1. Klicka på **Spara**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Konfigurera SIEM-integrering via Azure Event Hubs

Azure Event Hubs är en bra lösning för program mässigt som förbrukar alla strömmande data. För Azure Security Center aviseringar och rekommendationer är det det bästa sättet att integrera med en SIEM från tredje part.

> [!NOTE]
> Den mest effektiva metoden att strömma övervaknings data till externa verktyg i de flesta fall är att använda Azure Event Hubs. [Den här artikeln](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) innehåller en kort beskrivning av hur du kan strömma övervaknings data från olika källor till en Event Hub och länkar till detaljerad vägledning.

> [!NOTE]
> Om du tidigare har exporterat Security Center aviseringar till en SIEM med hjälp av Azure aktivitets logg ersätter proceduren nedan metoden.

Om du vill visa händelse scheman för de exporterade data typerna kan du gå till händelse [scheman för Event Hub](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integrera med en SIEM 

När du har konfigurerat den löpande exporten av dina valda Security Center data till Azure Event Hubs kan du konfigurera lämplig anslutning för din SIEM:

* **Azure Sentinel** – Använd den interna Azure Security Center aviserings [Data Connector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) som erbjuds där.
* **Splunk** – Använd [Azure Monitor-tillägget för Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM-QRadar** – Använd [en manuellt konfigurerad logg källa](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – Använd [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Om du vill flytta de kontinuerligt exporterade data automatiskt från den konfigurerade Händelsehubben till Azure Datautforskaren, använder du anvisningarna i mata in [data från händelsehubben till azure datautforskaren](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Löpande export till en Log Analytics-arbetsyta

Om du vill analysera Azure Security Center data i en Log Analytics arbets yta eller använda Azure-aviseringar tillsammans med Security Center kan du konfigurera kontinuerlig export till din Log Analytics-arbetsyta.

Om du vill exportera till en Log Analytics arbets yta måste du ha Security Center Log Analytics-lösningar som är aktiverade på din arbets yta. Om du använder Azure Portal aktive ras Security Centers lösning för gratis nivå automatiskt när du aktiverar kontinuerlig export. Men om du konfigurerar dina inställningar för kontinuerlig export program mässigt måste du manuellt välja den kostnads fria eller standard pris nivån för den nödvändiga arbets ytan från **pris & inställningar**.  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabeller och scheman

Säkerhets aviseringar och rekommendationer lagras i tabellerna *SecurityAlert* respektive *SecurityRecommendations* . Namnet på Log Analytics-lösningen som innehåller dessa tabeller beror på om du är på nivån kostnads fri eller standard (se [prissättning](security-center-pricing.md)): säkerhet (' säkerhet och granskning ') eller SecurityCenterFree.

![* SecurityAlert *-tabellen i Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Om du vill visa händelse scheman för de exporterade data typerna går du till [Log Analytics tabell scheman](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Visa exporterade säkerhets aviseringar och rekommendationer i Azure Monitor

I vissa fall kan du välja att visa de exporterade säkerhets aviseringarna och/eller rekommendationerna i [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor ger en enhetlig aviserings upplevelse för en rad olika Azure-aviseringar, inklusive diagnostisk logg, mått aviseringar och anpassade aviseringar baserat på frågor från Log Analytics-arbetsyta.

Om du vill visa aviseringar och rekommendationer från Security Center i Azure Monitor konfigurerar du en varnings regel baserat på Log Analytics frågor (logg avisering):

1. Från Azure Monitor sidan **aviseringar** klickar du på **ny aviserings regel**.

    ![Azure Monitor sidan aviseringar](./media/continuous-export/azure-monitor-alerts.png)

1. På sidan Skapa regel konfigurerar du din nya regel (på samma sätt som du konfigurerar en [logg varnings regel i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * För **resurs**väljer du den Log Analytics arbets yta som du exporterade säkerhets aviseringar och rekommendationer till.

    * För **villkor**väljer du **anpassad loggs ökning**. På sidan som visas konfigurerar du frågan, lookback perioden och frekvens perioden. I Sök frågan kan du skriva *SecurityAlert* eller *SecurityRecommendation* för att fråga data typerna som Security Center kontinuerligt exportera till när du aktiverar funktionen för kontinuerlig export till Log Analytics. 
    
    * Du kan också konfigurera den [Åtgärds grupp](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) som du vill utlösa. Åtgärds grupper kan utlösa e-post som skickas, ITSM biljetter, Webhooks och mycket annat.
    ![Azure Monitor varnings regel](./media/continuous-export/azure-monitor-alert-rule.png)

Nu visas nya Azure Security Center aviseringar eller rekommendationer (beroende på din konfiguration) i Azure Monitor aviseringar, med automatisk utlösning av en åtgärds grupp (om det finns).

## <a name="manual-one-time-export-of-security-alerts"></a>Manuell export av säkerhets aviseringar

Om du vill ladda ned en CSV-rapport för aviseringar eller rekommendationer öppnar du sidan **säkerhets aviseringar** eller **rekommendationer** och klickar på knappen **Hämta CSV-rapport** .

[![Hämta aviserings data som en CSV-fil](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> De här rapporterna innehåller aviseringar och rekommendationer för resurser från de för tillfället valda prenumerationerna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar kontinuerliga exporter av dina rekommendationer och aviseringar. Du har också lärt dig hur du hämtar dina aviserings data som en CSV-fil. 

Information om relaterade material finns i följande dokumentation: 

- [Dokumentation om Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentation om Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentation för Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Scheman för arbets flödes automatisering och kontinuerlig export av data typer](https://aka.ms/ASCAutomationSchemas)
