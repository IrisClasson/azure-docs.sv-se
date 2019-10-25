---
title: Självstudie – strömma loggar till en Azure Event Hub | Microsoft Docs
description: Lär dig hur du konfigurerar Azure-diagnostik att skicka Azure Active Directory loggar till en Event Hub
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5a790358b0b5c5f301cd53f0cd564a36f66049b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817500"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Självstudie: strömma Azure Active Directory loggar till en Azure Event Hub

I den här självstudien får du lära dig hur du konfigurerar Azure Monitor-diagnostikinställningar för att strömma Azure Active Directory-loggar (Azure AD) till en Azure-händelsehubb. Använd den här mekanismen för att integrera dina loggar med SIEM-verktyg (Säkerhetsinformation och händelsehantering) från tredje part, till exempel Splunk och QRadar.

## <a name="prerequisites"></a>Krav 

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.
* En Event Hubs-namnrymd och en händelsehubb i din Azure-prenumeration. Lär dig att [skapa en händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="stream-logs-to-an-event-hub"></a>Strömma loggar till en händelsehubb

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Azure Active Directory** > **övervakning** > **gransknings loggar**. 

3. Välj **Exportinställningar**.  
    
4. I fönsterrutan **Diagnostikinställningar** gör du något av följande:
    * Om du vill ändra befintliga inställningar väljer du **Redigera inställning**.
    * Om du vill lägga till nya inställningar väljer du **Add diagnostics setting** (Lägg till diagnostikinställning).  
      Du kan ha upp till tre inställningar.

      ![Exportinställningar](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Markera kryssrutan **Strömma till en händelsehubb** och välj sedan **Event Hub/Configure** (Händelsehubb/Konfigurera).

6. Välj Azure-prenumerationen och den Event Hubs-namnrymd som du vill dirigera loggarna till.  
    Både prenumerationen och Event Hubs-namnrymden måste vara associerade med den Azure AD-klientorganisation som loggarna strömmar från. Du kan även ange en händelsehubb i Event Hubs-namnrymden som loggar ska skickas till. Om ingen händelsehubb anges skapas en händelsehubb i namnrymden med standardnamnet **insights-logs-audit**.

7. Välj **OK** för att avsluta händelsehubbkonfigurationen.

8. Gör endera eller båda av följande:
    * För att skicka spårningsloggar till lagringskontot markerar du kryssrutan **AuditLogs** (Spårningsloggar). 
    * För att skicka inloggningsloggar till lagringskontot markerar du kryssrutan **SignInLogs** (Inloggningsloggar).

9. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Efter ungefär 15 minuter kontrollerar du att händelserna visas i din händelsehubb. För att göra detta går du till händelsehubben från portalen och kontrollerar att antalet **inkommande meddelanden** är större än noll. 

    ![Granskningsloggar](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Komma åt data från din händelsehubb

När data visas i händelsehubben kan du komma åt och läsa data på två sätt:

* **Konfigurera ett SIEM-stödverktyg**. För att läsa data från händelsehubben kräver de flesta verktyg händelsehubbens anslutningssträng och vissa behörigheter till din Azure-prenumeration. Verktyg från tredje part med Azure Monitor-integrering inkluderar men är inte begränsade till:
    
    * **ArcSight**: Mer information om hur du integrerar Azure AD-loggar med Splunk finns i [integrera Azure Active Directory loggar med ArcSight med hjälp av Azure Monitor](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk**: Information om att integrera Azure AD-loggar med Splunk finns på sidan om att [integrera Azure AD-loggar med Splunk med hjälp av Azure Monitor](tutorial-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: DSM- och Azure Event Hub-protokollet är tillgängligt för nedladdning vid [IBM-supporten](https://www.ibm.com/support). Mer information om integrering med Azure finns på sidan [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic**: Om du vill konfigurera Sumo Logic för att använda data från en händelsehubb läser du [Install the Azure AD app and view the dashboards](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) (Installera Azure AD-appen och visa instrumentpanelerna). 

* **Konfigurera anpassade verktyg**. Om din aktuella SIEM inte stöds i Azure Monitor-diagnostiken ännu kan du konfigurera anpassade verktyg genom att använda Event Hub-API. Mer information finns på sidan om att [komma igång med att ta emot meddelanden från en händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Nästa steg

* [Integrera Azure Active Directory-loggar med ArcSight med Azure Monitor](howto-integrate-activity-logs-with-arcsight.md)
* [Integrera Azure AD-loggar med Splunk med hjälp av Azure Monitor](tutorial-integrate-activity-logs-with-splunk.md)
* [Integrera Azure AD-loggar med SumoLogic med hjälp av Azure Monitor](howto-integrate-activity-logs-with-sumologic.md)
* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
