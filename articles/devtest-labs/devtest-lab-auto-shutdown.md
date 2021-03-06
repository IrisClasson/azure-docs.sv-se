---
title: Hantera principer för automatisk avstängning i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du ställer in en princip för automatisk avstängning för ett labb så att virtuella datorer automatiskt stängs av när de inte används.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a30070470f9a75ec5c56d448cd09ca82dd0cbce7
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287554"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurera automatisk avstängning för labb-och beräknings virtuella datorer i Azure DevTest Labs

Den här artikeln förklarar hur du konfigurerar inställningar för automatisk avstängning för virtuella labb datorer i DevTest Labs och beräkning av virtuella datorer. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurera automatisk avstängning för virtuella labb datorer (DevTest Labs)
Med Azure DevTest Labs kan du kontrol lera kostnaderna och minimera spill i labbet genom att hantera principer (inställningar) för varje labb. Den här artikeln visar hur du konfigurerar principen för automatisk avstängning för ett labb konto och konfigurerar inställningar för automatisk avstängning för ett labb i labb kontot. Information om hur du anger alla labb principer finns [i definiera labb principer i Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Ange en princip för automatisk avstängning för ett labb
Som labb ägare kan du konfigurera ett avslutnings schema för alla virtuella datorer i labbet. Genom att göra det kan du spara kostnader från att köra datorer som inte används (inaktiva). Du kan tillämpa en avslutnings princip på alla dina virtuella labb datorer centralt men även spara dina labb användare för att ställa in ett schema för sina enskilda datorer. Med den här funktionen kan du ange principen för ditt labb schema med början från att ge dig ingen kontroll över fullständig behörighet till dina labb användare. Som labb ägare kan du konfigurera den här principen genom att utföra följande steg:

1. På Start sidan för ditt labb väljer du **konfiguration och principer**.
2. Välj **princip för automatisk avstängning** i avsnittet **scheman** på den vänstra menyn.
3. Välj ett av alternativen. I följande avsnitt får du mer information om de här alternativen: set-principen gäller endast för nya virtuella datorer som skapats i labbet och inte till redan befintliga virtuella datorer. 

    ![Princip alternativ för automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurera inställningar för automatisk avstängning
Principen för automatisk avstängning hjälper till att minimera labb avfall genom att göra det möjligt att ange den tid som Labbets virtuella datorer stängs av.

Följ dessa steg om du vill visa (och ändra) principerna för ett labb:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.   
4. Välj **konfiguration och principer**.

    ![Fönstret princip inställningar](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. I rutan **konfiguration och principer** för labb väljer du **Automatisk avstängning** under **scheman**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Välj **på** för att aktivera den här principen och **Inaktivera för att** inaktivera den.
7. Om du aktiverar den här principen anger du tid (och tidszon) för att stänga av alla virtuella datorer i det aktuella labbet.
8. Ange **Ja** eller **Nej** för alternativet för att skicka ett meddelande 30 minuter före den angivna tiden för automatisk avstängning. Om du väljer **Ja**anger du en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelandet ska publiceras eller skickas. Användaren får ett meddelande och det ges möjlighet att fördröja avstängningen. Mer information finns i avsnittet om [meddelanden](#notifications) . 
9. Välj **Spara**.

    Som standard gäller den här principen för alla virtuella datorer i det aktuella labbet. Om du vill ta bort den här inställningen från en speciell virtuell dator öppnar du hanterings fönstret för den virtuella datorn och ändrar inställningen för automatisk **avstängning** .
    
> [!NOTE]
> Om du uppdaterar schemat för automatisk avstängning för ditt labb eller en speciell labb virtuell dator inom 30 minuter från den aktuella schemalagda tiden, kommer den uppdaterade stängnings tiden att gälla för nästa dags schema. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användare anger ett schema och kan välja
Om du ställer in labbet på den här principen kan labb användarna åsidosätta eller välja ur labb schema. Det här alternativet ger labb användare fullständig kontroll över schemat för automatisk avstängning av deras virtuella datorer. Labb användare ser ingen ändring på sidan schema för automatisk avstängning av virtuell dator.

![Princip alternativ för automatisk avstängning – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användare anger ett schema och kan inte avanmäla
Om du ställer in labbet på den här principen kan labb användare åsidosätta labb schema. De kan dock inte välja bort principen för automatisk avstängning. Det här alternativet ser till att varje dator i labbet är under ett schema för automatisk avstängning. Labb användare kan uppdatera schemat för de virtuella datorerna automatiskt och konfigurera avslutnings meddelanden.

![Princip alternativ för automatisk avstängning – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över det schema som angetts av labb administratören
Om du ställer in labbet på den här principen kan inte labb användare åsidosätta eller välja ut ur labb schemat. Det här alternativet ger labb administratören fullständig kontroll av schemat för varje dator i labbet. Labb användare kan bara konfigurera meddelanden för automatisk avstängning för sina virtuella datorer.

![Princip alternativ för automatisk avstängning – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Meddelanden
När automatisk avstängning har kon figurer ATS av labb ägaren skickas meddelanden till labb användarna 30 minuter innan automatisk avstängning utlöses om någon av deras virtuella datorer kommer att påverkas. Det här alternativet ger labb användare möjlighet att spara sitt arbete innan den stängs av. Meddelandet innehåller också länkar för varje virtuell dator för följande åtgärder:

- Hoppa över automatisk avstängning för den här tiden
- Använd automatisk avstängning för en timme eller två timmar så att de kan fortsätta att arbeta på den virtuella datorn.

Meddelandet skickas via den konfigurerade webhook-slutpunkten eller en e-postadress som anges av labb ägare i inställningarna för automatisk avstängning. Med Webhooks kan du skapa eller konfigurera integreringar som prenumererar på vissa händelser. När en av dessa händelser aktive ras skickar DevTest Labs en HTTP POST-nytto Last till webhookens konfigurerade URL. Mer information om Webhooks finns i [skapa en webhook eller API Azure-funktion](../azure-functions/functions-bindings-http-webhook.md). 

Vi rekommenderar att du använder Webhooks eftersom de stöds av olika appar (till exempel slack, Azure Logic Apps och så vidare) och gör att du kan använda ditt eget sätt för att skicka meddelanden. I den här artikeln får du ett exempel på hur du kan få meddelanden om automatisk avstängning från e-postmeddelanden med hjälp av Azure Logic Apps. Först ska vi gå igenom de grundläggande stegen för att aktivera meddelanden om automatisk avstängning i labbet.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Skapa en Logic-app som tar emot e-postmeddelanden
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) innehåller många färdiga kopplingar som gör det enkelt att integrera en tjänst med andra klienter, t. ex. Office 365 och Twitter. På den högsta nivån kan stegen för att ställa in en Logic app för e-postavisering delas upp i fyra faser: 

- Skapa en Logic-app. 
- Konfigurera den inbyggda mallen.
- Integrera med din e-postklient
- Hämta webhook-URL: en.

### <a name="create-a-logic-app"></a>Skapa en logikapp
Kom igång genom att skapa en Logic app i din Azure-prenumeration med hjälp av följande steg:

1. Välj **+ skapa en resurs** på den vänstra menyn, Välj **integration**och välj **Logic app**. 

    ![Menyn ny Logic app](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Följ dessa steg på sidan **Logic app-Create** : 
    1. Ange ett **namn** för Logic-appen.
    2. Välj din Azure- **prenumeration**.
    3. Skapa en ny **resurs grupp** eller Välj en befintlig resurs grupp. 
    4. Välj en **plats** för Logic app. 

        ![Ny Logic app – inställningar](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. I **aviseringarna**väljer **du gå till resurs** i meddelandet. 

    ![Gå till resurs](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Välj **Logic App Designer** under kategorin **distributions verktyg** .

    ![Välj HTTP-begäran/svar](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. På sidan **http-begäran-svar** väljer du **Använd den här mallen**. 

    ![Välj alternativet Använd den här mallen](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Kopiera följande JSON till avsnittet **JSON-schema för begär ande text** : 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Begär ande text JSON-schema](./media/devtest-lab-auto-shutdown/request-json.png)
7. Välj **+ nytt steg** i designern och följ de här stegen:
    1. Sök efter **Office 365 Outlook – skicka ett e-postmeddelande**. 
    2. Välj **Skicka ett e-postmeddelande** från **åtgärder**. 
    
        ![Alternativet för att skicka e-post](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Välj **Logga** in för att logga in på ditt e-postkonto. 
    4. Välj **till** fältet och välj ägare.
    5. Välj **ämne**och ange ett ämne för e-postmeddelandet. Till exempel: "shutdown of Machine vmName for Lab: labName".
    6. Välj **brödtext**och definiera innehållet i e-postmeddelandet. Exempel: "vmName är schemalagd att stängas av om 15 minuter. Hoppa över den här avstängningen genom att klicka på: URL. Fördröj avstängning i en timme: delayUrl60. Fördröj avstängning i 2 timmar: delayUrl120. "

        ![Begär ande text JSON-schema](./media/devtest-lab-auto-shutdown/email-options.png)
1. Välj **Spara** i verktygsfältet. Nu kan du kopiera **http post-URL: en**. Välj kopierings knappen för att kopiera webb adressen till Urklipp. 

    ![Webhook-URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurera automatisk avstängning för beräkning av virtuella datorer

1. På sidan **virtuell dator** väljer du **Automatisk avstängning** på den vänstra menyn i avsnittet **åtgärder** . 
2. På sidan **Automatisk avstängning** väljer du **på** för att aktivera den här principen och inaktivera **för att** inaktivera den.
3. Om du aktiverar den här principen anger du den **tid** ( **och tidszon**) som den virtuella datorn ska stängas av.
4. Ange **Ja** eller **Nej** för alternativet för att skicka ett meddelande 30 minuter före den angivna tiden för automatisk avstängning. Om du väljer **Ja**anger du en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelandet ska publiceras eller skickas. Användaren får ett meddelande och det ges möjlighet att fördröja avstängningen. Mer information finns i avsnittet om [meddelanden](#notifications) . 
9. Välj **Spara**.

    ![Konfigurera automatisk avstängning för en beräknings-VM](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Visa aktivitets loggar för automatiska avstängnings uppdateringar
När du uppdaterar inställningen för automatisk avstängning visas den aktivitet som loggas i aktivitets loggen för den virtuella datorn. 

1. Gå till start sidan för den virtuella datorn i [Azure Portal](https://portal.azure.com).
2. Välj **aktivitets logg** på den vänstra menyn. 
3. Ta bort **resurs: mycomputevm** från filter.
3. Bekräfta att du ser åtgärden **Lägg till eller ändra scheman** i aktivitets loggen. Om du inte ser det väntar du en stund och uppdaterar aktivitets loggen.

    ![Aktivitets logg post](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Välj åtgärden **Lägg till eller ändra scheman** om du vill se följande information på sidan **Sammanfattning** :

    - Åtgärds namn (Lägg till eller ändra scheman)
    - Datum och tid då inställningen för automatisk avstängning uppdaterades.
    - E-postadressen för den användare som uppdaterade inställningen. 

        ![Sammanfattning av aktivitets logg post](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Om du växlar till fliken **Ändra historik** på sidan **Lägg till eller ändra scheman** visas ändrings historiken för inställningen. I följande exempel ändrades avstängnings tiden från 7 till 6 till och med den 10 april 2020 vid 15:18:47 EST. Och inaktiverade inställningen vid 15:25:09 EST. 

    ![Aktivitets logg-ändrings historik](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Om du vill se mer information om åtgärden växlar du till fliken **JSON** på sidan **Lägg till eller ändra scheman** .

## <a name="next-steps"></a>Nästa steg
Information om hur du anger alla principer finns [i definiera labb principer i Azure DevTest Labs](devtest-lab-set-lab-policy.md).
