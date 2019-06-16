---
title: Automatisera Azure Application Insights processer med hjälp av Logic Apps.
description: Lär dig hur du snabbt kan automatisera upprepade processer genom att lägga till Application Insights-anslutningsappen i logikappen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mbullwin
ms.openlocfilehash: 61215adc2aee5cef3693d119bf0efb36526d748b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60904838"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatisera processer för Application Insights med hjälp av Logic Apps

Hittar du själv upprepade gånger köra samma frågor på dina telemetridata för att kontrollera om din tjänst fungerar korrekt? Vill du automatisera de här frågorna för att hitta trender och avvikelser och sedan skapa dina egna arbetsflöden runt dem? Azure Application Insights-anslutningsapp för Logic Apps är rätt verktyg för detta ändamål.

Med den här integrationen kan du automatisera många processer utan att behöva skriva en enda rad kod. Du kan skapa en logikapp med Application Insights-anslutningsprogrammet för att automatisera snabbt en Application Insights-process. 

Du kan lägga till ytterligare åtgärder samt. Logic Apps-funktionen i Azure App Service tillgängliggör hundratals åtgärder. Till exempel med hjälp av en logikapp, kan du automatiskt skicka ett e-postmeddelande eller skapa en bugg i Azure DevOps. Du kan också använda en av många tillgängliga [mallar](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) för att påskynda processen för att skapa din logikapp. 

## <a name="create-a-logic-app-for-application-insights"></a>Skapa en logikapp för Application Insights

I den här självstudien får du lära dig hur du skapar en logikapp som använder algoritmen Analytics autocluster grupp attribut i data för ett webbprogram. Flödet skickar automatiskt resultaten via e-post, bara ett exempel på hur du kan använda Application Insights Analytics och Logic Apps tillsammans. 

### <a name="step-1-create-a-logic-app"></a>Steg 1: Skapa en logikapp
1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på **skapa en resurs**väljer **webb + mobilt**, och välj sedan **Logikapp**.

    ![Nytt logic app-fönster](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Steg 2: Skapa en utlösare för din logikapp
1. I den **Logic App Designer** fönstret under **börja med en vanlig utlösare**väljer **upprepning**.

    ![Logic App Designer-fönstret](./media/automate-with-logic-apps/2logicappdesigner.png)

1. I den **intervall** skriver **1** och sedan,**frekvens** väljer **dag**.

    ![Logic App Designer ”återkommande” fönster](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Steg 3: Lägg till en Application Insights-åtgärd
1. Klicka på **nytt steg**.

1. I den **Välj en åtgärd** Sök-rutan, skriver du in **Azure Application Insights**.

1. Under **åtgärder**, klickar du på **Azure Application Insights - visualisera analysfråga**.

    ![Fönstret ”Välj en åtgärd” på Logic App Designer](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Steg 4: Anslut till en Application Insights-resurs

För att slutföra det här steget behöver du ett program-ID och en API-nyckel för din resurs. Du kan hämta dem från Azure-portalen, enligt följande diagram:

![Program-ID i Azure portal](./media/automate-with-logic-apps/5apiaccess.png)

![Program-ID i Azure portal](./media/automate-with-logic-apps/6apikey.png)

Ange ett namn för anslutningen, program-ID och API-nyckeln.

![Fönstret för Logic App Designer flow anslutning](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Steg 5: Ange Analytics-fråga och diagram
I följande exempel frågan väljer misslyckade förfrågningar under den senaste dagen och kopplar dem med undantag som inträffat som en del av åtgärden. Analytics kopplat till misslyckade förfrågningar, baserat på operation_Id identifierare. Frågan segment sedan resultatet med hjälp av algoritmen autocluster. 

När du skapar egna frågor kan du kontrollera att de fungerar korrekt i Analytics innan du lägger till den i ditt flöde.

1. I den **fråga** lägger du till följande Analytics-frågan:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. I den **diagramtyp** väljer **Html-tabell**.

    ![Konfigurationsfönstret i Analytics-fråga](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Steg 6: Konfigurera logikapp för att skicka e-post

1. Klicka på **nytt steg**.

1. I sökrutan skriver **Office 365 Outlook**.

1. Klicka på **Office 365 Outlook – skicka ett e-postmeddelande**.

    ![Office 365 Outlook val](./media/automate-with-logic-apps/9sendemail.png)

1. I den **skicka ett e-postmeddelande** fönstret gör du följande:

   a. Ange e-postadressen till mottagaren.

   b. Ange ett ämne för e-postmeddelandet.

   c. Klicka på den **brödtext** rutan och sedan på dynamiskt innehåll-menyn som öppnas till höger väljer **brödtext**.
    
   d. Klicka på den **Lägg till ny parameter** listrutan och välj bilagor och är HTML.

      ![Office 365 Outlook-konfiguration](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook-konfiguration](./media/automate-with-logic-apps/11emailparameter.png)

1. På menyn om dynamiskt innehåll, gör du följande:

    a. Välj **Bilagenamn**.

    b. Välj **innehåll i bifogad fil**.
    
    c. I den **är HTML** väljer **Ja**.

      ![Skärm för konfiguration av e-post i Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Steg 7: Spara och testa din logikapp
* Klicka på **spara** att spara dina ändringar.

Du kan vänta på att utlösaren ska köras logikappen eller du kan köra logikappen omedelbart genom att välja **kör**.

![Skapa Logic app-skärmen](./media/automate-with-logic-apps/13save.png)

När logikappen körs får de mottagare som du angav i e-postlistan ett e-postmeddelande som ser ut som följande:

![Logic app e-postmeddelande](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skapar [analysfrågor](../../azure-monitor/log-query/get-started-queries.md).
- Läs mer om [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





