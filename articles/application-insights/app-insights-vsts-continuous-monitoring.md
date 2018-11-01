---
title: Kontinuerlig övervakning av din version DevOps-pipeline med Azure DevOps och Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att snabbt konfigurera kontinuerlig övervakning med Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75401614b6892402083af5192b691f00d82c8d05
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413629"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Lägg till kontinuerlig övervakning i din releasepipeline

Azure DevOps-tjänster som integreras med Azure Application Insights för att tillåta kontinuerlig övervakning av din DevOps-pipeline för versionen i hela livscykel. 

Azure DevOps-tjänster har nu stöd för kontinuerlig övervakning innebär att distributions-pipelines kan införliva övervakningsdata från Application Insights och andra Azure-resurser. När en avisering för Application Insights identifieras distributionen kan vara skyddad eller återställas tillbaka förrän aviseringen har lösts. Om alla kontroller skickar kan distributioner fortsätta automatiskt från test ända till produktion utan att behöva ingripa manuellt. 

## <a name="configure-continuous-monitoring"></a>Konfigurera en löpande övervakning

1. Välj en befintlig Azure DevOps-tjänster.

2. Hovra över **Build and Release** > Välj **versioner** > klickar du på den **plustecknet** > **skapa versionsdefinition** > Sök efter **övervakning** > **Azure App Service-distribution med kontinuerlig övervakning.**

   ![Nya Releasepipeline för Azure DevOps-tjänster](media/app-insights-vsts-continuous-monitoring/001.png)

3. Klicka på **gäller.**

4. Bredvid rött utropstecken markerar du texten i blått till **visa miljöuppgifter.**

   ![Visa miljöuppgifter](media/app-insights-vsts-continuous-monitoring/002.png)

   En konfigurationsruta visas, Använd följande tabell för att fylla i indatafälten.

    | Parameter        | Värde |
   | ------------- |:-----|
   | **Miljönamn**      | Namn som beskriver den version pipeline-miljön |
   | **Azure-prenumeration** | Listrutan fylls med Azure-prenumerationer som länkade till organisationen Azure DevOps-tjänsterna|
   | **Namn på App Service** | Manuell inmatning av ett nytt värde kan krävas för det här fältet för andra alternativ |
   | **Resursgrupp**    | Listrutan fylls med tillgängliga resursgrupper |
   | **Application Insights-resursnamn** | Listrutan fylls i med alla Application Insights-resurser som motsvarar den tidigare valda resursgruppen.

5. Välj **konfigurera Application Insights-aviseringar**

6. Standard Varningsregler, Välj **spara** > Ange en kommentar > Klicka på **OK**

## <a name="modify-alert-rules"></a>Ändra notifieringsregler

1. Om du vill ändra de fördefinierade aviseringsinställningarna, klicka på rutan med **ellipserna...**  till höger om **Aviseringsregler.**

   (Out of box fyra Varningsregler finns: tillgänglighet, misslyckade begäranden, svarstid för servern, Server-undantag.)

2. Klicka på symbolen listrutan bredvid **tillgänglighet.**

3. Ändra tillgängligheten **tröskelvärdet** att uppfylla behoven för tjänsten.

   ![Ändra varning](media/app-insights-vsts-continuous-monitoring/003.png)

4. Välj **OK** > **spara** > Ange en kommentar > Klicka på **OK.**

## <a name="add-deployment-conditions"></a>Lägg till villkor för distribution

1. Klicka på **Pipeline** > Välj den **Pre** eller **efter distributionen villkor** beroende på scenen som kräver en kontinuerlig övervakning gate.

   ![Före villkor](media/app-insights-vsts-continuous-monitoring/004.png)

2. Ange **Gates** till **aktiverad** > **godkännande gates**> Klicka på **Lägg till.**

3. Välj **Azure Monitor** (det här alternativet ger dig möjlighet att åtkomst aviseringar både från Azure Monitor och Application Insights)

    ![Azure Monitor](media/app-insights-vsts-continuous-monitoring/005.png)

4. Ange en **Gates timeout** värde.

5. Ange en **samplingsintervall.**

## <a name="deployment-gate-status-logs"></a>Distributionsloggar för gate-status

När du lägger till distribution gates skyddar en avisering i Application Insights som överskrider tröskeln för ditt tidigare definierade, distributionen från oönskad versionen befordran. När aviseringen har lösts fortsätta distributionen automatiskt.

Om du vill se det här beteendet, Välj **versioner** > Högerklicka på versionen namn **öppna** > **loggar.**

![Logs](media/app-insights-vsts-continuous-monitoring/006.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Pipelines försök med dessa [snabbstarter.](https://docs.microsoft.com/azure/devops/pipelines)
