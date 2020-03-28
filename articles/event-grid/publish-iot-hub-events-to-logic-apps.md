---
title: Självstudiekurs - Använda IoT Hub-händelser för att utlösa Azure Logic Apps
description: Den här självstudien visar hur du använder händelseroutningstjänsten för Azure Event Grid, skapar automatiserade processer för att utföra Azure Logic Apps-åtgärder baserat på IoT Hub-händelser.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706440"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Självstudiekurs: Skicka e-postmeddelanden om Azure IoT Hub-händelser med Event Grid och Logic Apps

Med Azure Event Grid kan du reagera på händelser i IoT Hub genom att utlösa åtgärder i underordnade företagsprogram.

Den här artikeln går igenom en exempelkonfiguration som använder IoT Hub och Event Grid. I slutet har du en Azure logic app inställd för att skicka ett e-postmeddelande varje gång en enhet läggs till i din IoT-hubb. 

## <a name="prerequisites"></a>Krav

* Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps, t.ex. Office 365 Outlook, Outlook.com eller Gmail. Det här e-postkontot används för att skicka händelsemeddelandena. En fullständig lista över Logic App-anslutningsprogram som stöds finns i [Översikt över anslutningsappar](https://docs.microsoft.com/connectors/)
* Ett aktivt Azure-konto. Om du inte har ett kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Skapa en logikapp

Skapa först en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn. 

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I [Azure-portalen](https://portal.azure.com)väljer du **Skapa en resurs**och skriver sedan "logikapp" i sökrutan och väljer retur. Välj **Logic App** i resultaten.

   ![Skapa en logikapp](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. På nästa skärm väljer du **Skapa**. 

1. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb. 

   ![Fält för att skapa logikapp](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Välj **Skapa**.

1. När resursen har skapats går du till din logikapp. Det gör du genom att välja **Resursgrupper**och sedan välja den resursgrupp som du skapade för den här självstudien. Leta sedan reda på logikappen i listan över resurser och välj den. 

1. På logic apps Designer kan du gå nedåt för att se **Mallar**. Välj **Tom Logik app** så att du kan bygga din logik app från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.  

1. Skriv **HTTP** i sökfältet för anslutningsprogram och utlösare.

1. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Välj **Generera schemat genom att använda en exempelnyttolast**. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Klistra in följande JSON-exempelkod i textrutan och välj sedan **Klar**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt. 

### <a name="create-an-action"></a>Skapa en åtgärd

Åtgärder är alla steg som utförs när utlösaren har startat logikappens arbetsflöde. I den här självstudiekursen är åtgärden att skicka ett e-postmeddelande från din e-postleverantör. 

1. Välj **Nytt steg**. Då öppnas ett fönster för att **välja en åtgärd**.

1. Sök efter **E-post**.

1. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Den här självstudien använder **Office 365 Outlook**. Stegen för andra e-postleverantörer liknar dessa. 

   ![Välj anslutningsprogram för e-postleverantör](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Välj åtgärden **Skicka ett e-postmeddelande**. 

1. Logga in på ditt e-postkonto om du uppmanas att göra det. 

1. Skapa e-postmallen. 

   * **Till**: Ange e-postadressen som meddelandena ska skickas till. I den här självstudiekursen använder du ett e-postkonto som du kan komma åt för testning. 

   * **Ämne**: Fyll i texten för ämnet. När du klickar på textrutan Ämne kan du välja dynamiskt innehåll som ska inkluderas. Den här självstudien använder `IoT Hub alert: {event Type}`till exempel . Om du inte kan se dynamiskt innehåll väljer du hyperlänken **Lägg till dynamiskt innehåll** – det här växlar den på och av.

   * **Brödtext**: Skriv texten för din e-post. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata. Om du inte kan se det dynamiska innehållet markerar du hyperlänken **Lägg till dynamiskt innehåll** under textrutan **Brödtext.** Om du inte visar de fält du vill använda klickar du *mer* på skärmen Dynamiskt innehåll för att inkludera fälten från föregående åtgärd.

   Din e-postmall kanske liknar den i det här exemplet:

   ![Fyll i e-postinformation](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Spara din logikapp. 

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps-designern kopierar du URL:en som används av dina logikappar för att lyssna efter en utlösare. Du använder den här URL:en för att konfigurera Event Grid. 

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den. 

1. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det. 

   ![Kopiera HTTP POST-URL:en](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Spara den här URL:en så att du kan referera till den i nästa avsnitt. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar. 

1. Gå till din IoT-hubb på Azure Portal. Du kan göra detta genom att välja **Resursgrupper,** sedan välja resursgruppen för den här självstudien och sedan välja din IoT-hubb i listan över resurser.

2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Välj **Händelseprenumeration**. 

   ![Skapa ny händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Skapa händelseprenumerationen med följande värden: 

   * **Information om händelseprenumeration:** Ange ett beskrivande namn och välj **Schema för händelserutnät**.

   * **Händelsetyper:** Avmarkera alla alternativ utom **Enhet som skapats**i **filter till händelsetyper**.

       ![typer av prenumerationshändelse](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Slutpunktsinformation:** Välj Slutpunktstyp som **webbkrok** och *välj en slutpunkt* och klistra in url:en som du kopierade från logikappen och bekräfta valet.

     ![webbadress till vald slutpunkt](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   När du är klar ska fönstret se ut som följande exempel: 

    ![Exempelformulär för händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Du kan spara händelseprenumerationen här och få meddelanden för alla enheter som har skapats i IoT-hubben. I den här självstudien ska vi dock använda de valfria fälten för att filtrera efter specifika enheter. Välj **Filter** högst upp i fönstret.

6. Välj **Lägg till nytt filter**. Fyll i fälten med följande värden:

   * **Nyckel**: `Subject`Välj .

   * **Operator**: `String begins with`Välj .

   * **Värde**: `devices/Building1_` Ange för att filtrera efter enhetshändelser i byggnad 1.
  
   Lägg till ett annat filter med följande värden:

   * **Nyckel**: `Subject`Välj .

   * **Operator**: `String ends with`Välj .

   * **Värde**: `_Temperature` Ange för att filtrera efter enhetshändelser relaterade till temperatur.

   Fliken **Filter** för din händelseprenumeration bör nu se ut ungefär som den här bilden:

   ![Lägga till filter i händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Spara händelseprenumerationen genom att välja **Skapa**.

## <a name="create-a-new-device"></a>Skapa en ny enhet

Testa logikappen genom att skapa en ny enhet för att utlösa ett e-postmeddelande med en händelseavisering. 

1. Välj **IoT-enheter** från din IoT-hubb. 

2. Välj **Ny**.

3. Ange `Building1_Floor1_Room1_Light` för **Enhets-ID**.

4. Välj **Spara**. 

5. Du kan lägga till flera enheter med olika enhets-ID:n för att testa händelseprenumerationsfiltren. Prova de här exemplen: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Om du har lagt till de fyra exemplen bör listan över IoT-enheter se ut så här:

   ![Lista över IoT Hub-enheter](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. När du har lagt till några enheter till IoT-hubben öppnar du din e-post för att se vilka som utlöste logikappen. 

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Om du vill kan du utföra IoT Hub-stegen med hjälp av Azure CLI i stället för att använda Azure Portal. Mer information finns på Azure CLI-sidorna för [att skapa en händelseprenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och [skapa en IoT-enhet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med att prova självstudien och testa dina resultat inaktiverar eller tar du bort resurser som du inte vill behålla. 

Om du vill ta bort alla resurser som har skapats i den här självstudien tar du bort resursgruppen. 

1. Välj **Resursgrupper**och välj sedan den resursgrupp som du skapade för den här självstudien.

2. Välj **Ta bort resursgrupp**i fönstret Resursgrupp . Du uppmanas att ange resursgruppsnamnet och sedan ta bort det. Alla resurser som finns där tas också bort.

Om du inte vill ta bort alla resurser kan du hantera dem en efter en. 

Om du inte vill förlora det arbete du gjort i logikappen inaktiverar du den i stället för att ta bort den. 

1. Gå till logikappen.

2. Välj **Ta bort** eller **Inaktivera** på bladet **Översikt**. 

Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat en kostnadsfri hubb för den här självstudiekursen behöver du inte ta bort den för att undvika kostnader.

1. Gå till IoT-hubben. 

2. Välj **Ta bort** på bladet **Översikt**. 

Även om du behåller din IoT-hubb kanske du vill ta bort händelseprenumerationen som du skapade. 

1. Välj **Event Grid** i IoT-hubben.

2. Välj den händelseprenumeration som du vill ta bort. 

3. Välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du kan svara på IoT Hub-händelser med hjälp av Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md).
* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Lär dig mer om vad du kan göra med [Event Grid](overview.md).