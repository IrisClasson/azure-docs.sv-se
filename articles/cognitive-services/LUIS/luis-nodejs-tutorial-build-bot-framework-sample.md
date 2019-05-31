---
title: Bot - Node.js - v3
titleSuffix: Azure Cognitive Services
description: Skapa en robot som är integrerad med en LUIS-program med Bot Framework 3.x och Azure Web app-robot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 8bd6a9df9518cb1b2312b525e8876629a2189bd2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399606"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>LUIS-bot i Node.js med Bot Framework 3.x och Azure Web app-robot

Använd Node.js och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattrobot använder fördefinierade HomeAutomation domänen för att snabbt implementera en bot-lösning. Roboten har byggts med Bot Framework 3.x och Azure Web app-robot.

En [Bot Framework 4.x med LUIS självstudiekursen](luis-nodejs-tutorial-bf-v4.md) är också tillgänglig.

## <a name="prerequisite"></a>Krav

Innan du skapar roboten följer du stegen i [skapa en app](./luis-get-started-create-app.md) att skapa LUIS-app som används.

Roboten besvarar avsikter från HomeAutomation domänen som finns i LUIS-app. För var och en av dessa avsikter ger LUIS-app ett intent som mappar till den. Roboten visar en dialogruta som hanterar avsikten som identifierar LUIS.

| Avsikt | Exempel på yttrande | Bot-funktioner |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Tända lampan. | Roboten anropar den `TurnOnDialog` när den `HomeAutomation.TurnOn` har identifierats. Den här dialogrutan är där du vill anropa en IoT-tjänst för att aktivera en enhet och meddela användaren som enheten har slagits på. |
| HomeAutomation.TurnOff | Inaktivera sovrum lamporna. | Roboten anropar den `TurnOffDialog` när den `HomeAutomation.TurnOff` har identifierats. Den här dialogrutan där du vill anropa en IoT-tjänst för att stänga av en enhet och meddela användaren som enheten har inaktiverats. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Skapa en Luis-bot med Bot Service

1. I den [Azure-portalen](https://portal.azure.com)väljer **Skapa ny resurs** i menyn bladet och välj **se alla**.

    ![Visa alla resurser i Azure portal](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App-robot**. 

    ![Välj web app-robot att starta processen att skapa resurs](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. I den **Robottjänst** bladet anger du nödvändig information och väljer **skapa**. Detta skapar och distribuerar bot service och LUIS-app till Azure. Om du vill använda [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), granska [region krav](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming) innan du skapar din robot. 
   * Ange **appnamn** till din robot namn. Namnet används som underdomänen när din robot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * Välj prenumerationen [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), App service-plan och [plats](https://azure.microsoft.com/regions/).
   * För **Bot mallen**väljer:
       * **SDK v3**
       * **Node.js**
       * **Språkförståelse**
   * Välj den **LUIS platsen**. Det här är redigeringen [region] [ LUIS] appen skapas i.
   * Markera kryssrutan bekräftelse för juridiskt meddelande. Villkoren i juridiskt meddelande är lägre än kryssrutan.

     ![Bot Service-bladet](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Bekräfta att bot-tjänsten har distribuerats.
    * Välj meddelanden (klockikonen finns längs överkanten av Azure portal). Meddelandet kommer att ändras från **distributionen har påbörjats** till **distributionen lyckades**.
    * När ändras meddelandet och **distributionen lyckades**väljer **gå till resurs** på som meddelanden.

## <a name="try-the-default-bot"></a>Prova standard bot

Bekräfta att roboten har distribuerats genom att markera den **meddelanden**. Meddelanden kommer att ändras från **distribution pågår...**  till **distributionen lyckades**. Välj **gå till resurs** knappen för att öppna bladet för den bot-resurser.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Installera NPM-resurser
Installera NPM-paket med följande steg:

1. Välj **skapa** från den **Bot Management** delen av Web App-robot. 

2. En ny, andra webbläsare öppnas. Välj **öppna online Kodredigerare**.

3. I det övre navigeringsfältet väljer du namnet på webbappen bot `homeautomationluisbot`. 

4. I listrutan, väljer **öppna Kudu-konsolen**.

5. Ett nytt webbläsarfönster öppnas. Ange följande kommando i konsolen:

    ```console
    cd site\wwwroot && npm install
    ```

    Vänta tills installationen ska slutföras. Gå tillbaka till det första webbläsarfönstret. 

## <a name="test-in-web-chat"></a>Testa i webbchatt
När roboten är registrerad väljer **Test i Web Chat** att öppna fönstret Web Chat. Skriv ”hello” i Web Chat.

  ![Testa roboten i Web Chat](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Roboten svarar genom att säga ”du har nått hälsning. Du SA: hello ”. Detta bekräftar att roboten har tagit emot ditt meddelande och skickas till en standard LUIS-app som skapats. Det här standardvärdet LUIS-app har upptäckt en hälsning avsikt. I nästa steg ansluter du roboten till LUIS-app som du skapade tidigare i stället för standard LUIS-app.

## <a name="connect-your-luis-app-to-the-bot"></a>Anslut din LUIS-app till roboten

Öppna **programinställningar** i den första webbläsarfönster och redigera den **LuisAppId** fält som innehåller program-ID för LUIS-appen.

  ![Uppdatera LUIS-app-ID i Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Om du inte har LUIS-app-ID kan logga in på den [LUIS](luis-reference-regions.md) webbplats som använder samma konto som du använder för att logga in på Azure. Välj på **Mina appar**. 

1. Hitta LUIS-appen du skapade tidigare, som innehåller avsikter och entiteter från HomeAutomation-domän.

2. I den **inställningar** för LUIS-app, hitta och kopiera app-ID.

3. Om du inte har tränats appen, väljer du den **träna** knappen i det övre högra hörnet för att träna din app.

4. Om du inte har publicerat appen, Välj **publicera** i det övre navigeringsfältet för att öppna den **publicera** sidan. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

## <a name="modify-the-bot-code"></a>Ändra bot-koden

Gå till andra webbläsarfönstret om det är fortfarande öppna eller i det första webbläsarfönstret väljer **skapa** och välj sedan **öppna online Kodredigerare**.

   ![Öppna online Kodredigerare](./media/luis-tutorial-node-bot/bot-service-build.png)

Öppna i kodredigeraren `app.js`. Det innehåller följande kod:

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Befintliga avsikter i app.js ignoreras. Du kan lämna dem. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Lägg till en dialogruta som matchar HomeAutomation.TurnOn

Kopiera följande kod och lägga till den i `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

Den [matchar] [ matches] alternativet på den [triggerAction] [ triggerAction] kopplad till dialogen anger namnet på avsikten. Identifieraren körs varje gång roboten tar emot ett uttryck från användaren. Om högsta bedömnings avsikten vidtas matchar en `triggerAction` bunden till en dialogruta, roboten anropar dialogrutan.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Lägg till en dialogruta som matchar HomeAutomation.TurnOff

Kopiera följande kod och lägga till den i `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testa roboten

I Azure-portalen väljer du på **testa i Web Chat** att testa roboten. Försök skriva meddelanden like ”tänd lamporna” och ”inaktivera min heater” anropa avsikter som du lade till den.
   ![Testa HomeAutomation bot i Web Chat](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Om du upptäcker att din robot inte alltid kan identifiera rätt avsikt eller entiteter, förbättra LUIS appens prestanda genom att ge den mer exempel yttranden träna upp tjänsten. Du kan kvarhålla LUIS-app utan några ändringar till din robot kod. Se [lägger du till exempel yttranden](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) och [träna och testa LUIS-appen](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>Läs mer om Bot Framework
Läs mer om [Bot Framework](https://dev.botframework.com/) och [3.x](https://github.com/Microsoft/BotBuilder) och [4.x](https://github.com/Microsoft/botbuilder-js) SDK: er.

## <a name="next-steps"></a>Nästa steg

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.-->
Du kan försöka att lägga till andra avsikter som hjälp, Avbryt och hälsning, LUIS-app. Sedan lägger du till dialogrutor för nya avsikter och testa dem med hjälp av roboten. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Lägg till avsikter](./luis-how-to-add-intents.md)
> [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches


[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

