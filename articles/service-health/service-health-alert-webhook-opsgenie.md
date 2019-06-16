---
title: Skicka Azure service health-aviseringar med OpsGenie med webhookar
description: Få personligt anpassade meddelanden om service health-händelser till din OpsGenie-instans.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: fab99b7093ac3f18f6313273d21905e0a3ed7e5b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067172"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Skicka Azure service health-aviseringar med OpsGenie med webhookar

Den här artikeln visar hur du ställer in Azure service health-aviseringar med OpsGenie med en webhook. Med hjälp av [OpsGenie](https://www.opsgenie.com/)'s Azure Service Health-integrering, kan du vidarebefordra Azure Service Health-aviseringar till OpsGenie. OpsGenie kan fastställa rätt personer att Avisera baserat på-samtal scheman med hjälp av e-post, textmeddelanden (SMS), telefonsamtal, iOS och Android push-meddelanden och den aviseringar tills aviseringen bekräftas eller stängd.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Skapa en service health integration URL i OpsGenie
1.  Kontrollera att du har registrerat dig för och är inloggad på ditt [OpsGenie](https://www.opsgenie.com/) konto.

1.  Navigera till den **integreringar** avsnittet i OpsGenie.

    ![Avsnittet ”integreringar” i OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Välj den **Azure Service Health** integration-knappen.

    ![”Azure Service Health knappen” i OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Namnet** din avisering och anger den **tilldelats Team** fält.

1.  Fyll i andra fält som **mottagare**, **aktiverad**, och **utelämna meddelanden**.

1.  Kopiera och spara den **integrering URL**, som redan ska innehålla din `apiKey` läggas till i slutet.

    ![”Integreringen URL” i OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Välj **spara Integration**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Skapa en avisering med OpsGenie i Azure-portalen
### <a name="for-a-new-action-group"></a>För en ny åtgärdsgrupp:
1. Följ steg 1 till och med 8 i [skapa en avisering på en avisering om tjänstens hälsa för en ny åtgärdsgrupp med hjälp av Azure portal](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definiera i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhook*

    b. **Information:** OpsGenie **integrering URL** du sparat tidigare.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **spara** när du är klar för att skapa aviseringen.

### <a name="for-an-existing-action-group"></a>För en befintlig åtgärdsgrupp:
1. I den [Azure-portalen](https://portal.azure.com/)väljer **övervakaren**.

1. I den **inställningar** väljer **åtgärdsgrupper**.

1. Hitta och välj åtgärdsgrupp som du vill redigera.

1. Lägg till i listan över **åtgärder**:

    a. **Åtgärdstyp:** *Webhook*

    b. **Information:** OpsGenie **integrering URL** du sparat tidigare.

    c. **Namn:** Webhooks namn, alias eller identifierare.

1. Välj **spara** när du är klar för att uppdatera åtgärdsgruppen.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa webhook-integrering via en HTTP POST-begäran
1. Skapa service health-nyttolasten som du vill skicka. Du hittar ett exempel service health webhook-nyttolasten på [Webhooks för Azure-aktivitetsloggar loggaviseringar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran enligt följande:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Du bör få ett `200 OK` svar med meddelandet av status ”lyckades”.

1. Gå till [OpsGenie](https://www.opsgenie.com/) att bekräfta att din integrering har har ställts in.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurera webhook-aviseringar för befintliga problem system](service-health-alert-webhook-guide.md).
- Granska den [avisering webhook för aktivitetslogg](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Lär dig mer om [service health meddelanden](../azure-monitor/platform/service-notifications.md).
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).