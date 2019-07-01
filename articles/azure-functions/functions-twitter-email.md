---
title: Skapa en funktion som kan integreras med Azure Logic Apps
description: Skapa en funktion som integrerar med Azure Logic Apps och Azure Cognitive Services för att kategorisera tweetsentiment och skicka meddelanden när sentimentet är svagt.
services: functions, logic-apps, cognitive-services
keywords: workflow, cloud apps, cloud services, business processes, system integration, enterprise application integration, EAI
author: craigshoemaker
manager: gwallace
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 11e0170464d8ed901253925eb62cce12d42c0008
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480022"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Skapa en funktion som kan integreras med Azure Logic Apps

Azure Functions kan integreras med Azure Logic Apps i Logikappdesignern. Via integrationen kan du använda Functions databearbetningskraft i orkestreringar med andra tjänster från Azure och tredje part. 

Den här självstudien visar hur du använder Functions med Logic Apps och Cognitive Services på Azure för att köra attitydanalyser från Twitter-inlägg. En HTTP-utlöst funktion kategoriserar tweets som gröna, gula eller röda baserat på attitydsresultatet. Ett e-postmeddelande skickas när svaga sentiment har identifierats. 

![bild på de första två stegen för en app i Logikappdesignern](media/functions-twitter-email/00-logic-app-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en API-resurs för Cognitive Services.
> * Skapa en funktion som kategoriserar tweetsentiment.
> * Skapa en logikapp som ansluter till Twitter.
> * Lägg till sentimentidetntifiering till logikappen. 
> * Anslut logikappen till funktionen.
> * Skicka ett e-postmeddelande baserat på svar från funktionen.

## <a name="prerequisites"></a>Förutsättningar

+ Ett aktivt [Twitter](https://twitter.com/)-konto. 
+ Ett [Outlook.com](https://outlook.com/)-konto (för att skicka meddelanden).
+ För den här artikeln förutsätts de resurser som skapades i avsnittet om hur du [skapar din första funktion i Azure-portalen](functions-create-first-azure-function.md).  
Om du inte redan har gjort detta måste du slutföra stegen för att skapa din funktionsapp.

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

API:erna för Cognitive Services är tillgängliga i Azure som enskilda resurser. Använd API för textanalys för att identifiera tweetsentimenten som övervakas.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

3. Klicka på **AI + Machine Learning** > **Textanalys**. Använd sedan inställningarna som anges i tabellen för att skapa resursen.

    ![Skapa Cognitive-resurssida](media/functions-twitter-email/01-create-text-analytics.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                                        |
    | --- | --- | --- |
    | **Namn** | MyCognitiveServicesAccnt | Välj ett unikt kontonamn. |
    | **Location** | Västra USA | Använd platsen som är närmast dig. |
    | **prisnivå** | F0 | Börja med den lägsta nivån. Om du får slut på anrop skalar du till en högre nivå.|
    | **Resursgrupp** | myResourceGroup | Använd samma resursgrupp för alla tjänster i självstudien.|

4. Klicka på **Skapa** för att skapa resursen. 

5. Klicka på **Översikt** och kopiera värdet av **slutpunkten** till en textredigerare. Det här används när du skapar en anslutning till Cognitive Services-API:et.

    ![Cognitive Services-inställningar](media/functions-twitter-email/02-cognitive-services.png)

6. I det vänstra navigeringsfönstret klickar du på **Nycklar** och kopierar sedan värdet för **nyckel 1** och har den tillgänglig i textredigeraren. Nyckeln använder du för att ansluta logikappen till ditt Cognitive Services-API. 
 
    ![Cognitive Services-nycklar](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Skapa funktionsappen

Med Functions får du ett bra sätt att avlasta pågående uppgifter i ett logikappsarbetsflöde. I den här självstudien används en HTTP-utlöst funktion för att bearbeta tweetattitydsresultat från Cognitive Services och returnera ett kategorivärde.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Skapa en HTTP-utlöst funktion  

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen**.

    ![Sidan snabbstart för funktioner i Azure Portal](media/functions-twitter-email/05-function-app-create-portal.png)

2. Välj sedan **Webhook + API** och klicka på **Skapa**. 

    ![Välj HTTP-utlösare](./media/functions-twitter-email/06-function-webhook.png)

3. Ersätt innehållet i filen `run.csx` med följande kod och klicka sedan på **Spara**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    Den här funktionskoden returnerar en färgkategori baserat på sentimentets poäng från begäran. 

4. Testa funktionen genom att klicka på **Test** längst till höger för att expandera fliken Test. Skriv värdet `0.2` för **Brödtext i förfrågan** och klicka sedan på **Run**. Värdet **RED** (Röd) returneras i brödtexten i svaret. 

    ![Testa funktionen i Azure Portal](./media/functions-twitter-email/07-function-test.png)

Nu har du en funktion som kategoriserar sentimentpoäng. Därefter skapar du en logikapp som integrerar din funktion med dina API:er för Twitter och Cognitive Services. 

## <a name="create-a-logic-app"></a>Skapa en logikapp   

1. Klicka på knappen **New** (Nytt) längst upp till vänster i Azure Portal.

2. Klicka på **Webb** > **Logikapp**.
 
3. Ange sedan ett värde för **Namn** som `TweetSentiment` och använd inställningarna som anges i tabellen.

    ![Skapa logikapp i Azure-portalen](./media/functions-twitter-email/08-logic-app-create.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                                        |
    | ----------------- | ------------ | ------------- |
    | **Namn** | TweetSentiment | Välj ett lämpligt namn för din app. |
    | **Resursgrupp** | myResourceGroup | Välj samma befintliga resursgrupp som tidigare. |
    | **Location** | Östra USA | Välj en plats i närheten av dig. |    

4. När du har angett rätt inställningsvärden klickar du på **Skapa** för att skapa logikappen. 

5. När appen har skapats klickar du på den nya logikappen som är fäst på instrumentpanelen. I Logikappsdesignern bläddrar du sedan ned och klickar på mallen **Tom logikapp**. 

    ![Tom Logic Apps-mall](media/functions-twitter-email/09-logic-app-create-blank.png)

Nu kan du använda Logikappdesignern för att lägga till tjänster och utlösare till appen.

## <a name="connect-to-twitter"></a>Ansluta till Twitter

Skapa först en anslutning till ditt Twitter-konto. Logikappen söker efter tweets, vilket utlöser körning av appen.

1. I designern klickar du på **Twitter**-tjänsten och på utlösaren **När en ny tweet publiceras**. Logga in på ditt Twitter-konto och auktorisera att Logic Apps får använda ditt konto.

2. Använd Twitter-utlösarinställningarna som anges i tabellen. 

    ![Twitter-anslutningsinställningar](media/functions-twitter-email/10-tweet-settings.png)

    | Inställning      |  Föreslaget värde   | Beskrivning                                        |
    | ----------------- | ------------ | ------------- |
    | **Genomsöka text** | #Azure | Använd en hashtagg som är tillräckligt populär för att generera nya tweets i det valda intervallet. När du använder den kostnadsfria nivån och din hashtagg är för populär kan du snabbt förbruka transaktionskvoten i din Cognitive Services-API. |
    | **Intervall** | 15 | Tiden som går mellan Twitter-begäran, i frekvensenheter. |
    | **Frekvens** | Minut | Frekvensenheten som används för att avsöka Twitter.  |

3.  Klicka på **Spara** för att ansluta till ditt Twitter-konto. 

Nu är appen ansluten till Twitter. Efter det ansluter du till textanalys för att identifiera sentimentet med insamlade tweets.

## <a name="add-sentiment-detection"></a>Lägga till identifiering av attityder

1. Välj **Nytt steg** och sedan **Lägg till en åtgärd**.

2. I **Välj en åtgärd** skriver du **Textanalys** och klickar sedan på åtgärden **Identifiera sentiment**.
    
    ![Nytt steg och sedan Lägg till en åtgärd](media/functions-twitter-email/11-detect-sentiment.png)

3. Ange ett anslutningsnamn som `MyCognitiveServicesConnection`, klistra in nyckeln för ditt Cognitive Services-API och den Cognitive Services-slutpunkt du har tillgänglig i en textredigerare, och klicka på **Skapa**.

    ![Nytt steg och sedan Lägg till en åtgärd](media/functions-twitter-email/12-connection-settings.png)

4. Ange sedan **Tweet-text** i textrutan och klicka på **Nytt steg**.

    ![Definiera text att analysera](media/functions-twitter-email/13-analyze-tweet-text.png)

Nu när sentimentidentifiering har konfigurerats kan du lägga till en anslutning till din funktion som förbrukar sentimentresultatet.

## <a name="connect-sentiment-output-to-your-function"></a>Anslut sentimentutdata till din funktion

1. I Logic Apps-designern klickar du på **Nytt steg** > **Lägg till en åtgärd**. Filtrera på **Azure Functions** och klicka på **Välj en Azure-funktion**.

    ![Identifiera sentiment](media/functions-twitter-email/14-azure-functions.png)
  
4. Välj den funktionsapp du skapade tidigare.

    ![Välj funktion](media/functions-twitter-email/15-select-function.png)

5. Välj den funktion du har skapat för den här kursen.

    ![Välj funktion](media/functions-twitter-email/16-select-function.png)

4. In **Begärandetext** klickar du på **Poäng** och sedan på **Spara**.

    ![Poäng](media/functions-twitter-email/17-function-input-score.png)

Nu utlöses din funktion när ett sentimentpoäng skickas från logikappen. En färgkodad kategori returneras till logikappen av funktionen. Lägg sedan till ett e-postmeddelande som är skickas när sentimentvärdet **RED** returneras från funktionen. 

## <a name="add-email-notifications"></a>Lägga till e-postmeddelanden

Den sista delen av arbetsflödet är att utlösa ett e-postmeddelande när sentimentet får poängen _RED_. I det här avsnittet används en Outlook.com-anslutning. Du kan utföra liknande steg för att använda en Gmail- eller Office 365 Outlook-anslutning.   

1. I Logikappdesignern klickar du på **Nytt steg** > **Lägg till ett villkor**. 

    ![Lägg till ett villkor i logikappen.](media/functions-twitter-email/18-add-condition.png)

2. Klicka på **Välj ett värde** och sedan på **Brödtext**. Välj **är lika med**, klicka på **Välj ett värde**, skriv `RED` och klicka på **Spara**. 

    ![Välj en åtgärd för villkoret.](media/functions-twitter-email/19-condition-settings.png)    

3. Vid **IF TRUE** klickar du på **Lägg till en åtgärd**, söker efter `outlook.com`, klickar på **Skicka ett e-postmeddelande** och loggar in på ditt Outlook.com-konto.

    ![Konfigurera e-postmeddelandet för åtgärden skicka ett e-postmeddelande.](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Om du inte har något Outlook.com-konto kan du välja en annan anslutning, som Gmail eller Office 365 Outlook

4. I åtgärden **Skicka ett e-postmeddelande** använder du e-postinställningarna som anges i tabellen. 

    ![Konfigurera e-postmeddelandet för åtgärden skicka ett e-postmeddelande.](media/functions-twitter-email/21-configure-email.png)
    
| Inställning      |  Föreslaget värde   | Beskrivning  |
| ----------------- | ------------ | ------------- |
| **Till** | Skriv din e-postadress | E-postadressen som tar emot ett meddelande. |
| **Ämne** | Negativt tweetsentiment identifierat  | E-postmeddelandets ämnesrad.  |
| **Brödtext** | Tweet-text, plats | Klicka på parametrarna **Tweet-text** och **Plats**. |

1. Klicka på **Spara**.

Nu när arbetsflödet är klart kan du aktivera logikappen och se när funktionen är igång.

## <a name="test-the-workflow"></a>Testa arbetsflödet

1. I Logikappdesignern klickar du på **Kör** för att starta logikappen.

2. I den vänstra kolumnen klickar du på **Översikt** för att visa logikappens status. 
 
    ![Körningsstatus för logikappen](media/functions-twitter-email/22-execution-history.png)

3. (Valfritt) Klicka på någon av körningarna för att visa information om körningen.

4. Gå till din funktion, visa loggarna och verifiera att sentimentvärdena har tagits emot och bearbetats.
 
    ![Visa funktionsloggar](media/functions-twitter-email/sent.png)

5. När ett potentiellt negativt sentiment identifieras får du ett e-postmeddelande. Om du inte har fått något e-postmeddelande kan du ändra funktionskoden till RED varje gång:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    När du har verifierat e-postmeddelandena ändrar du tillbaka till originalkoden:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > När du har slutfört självstudien bör du inaktivera logikappen. När du inaktiverar appen undviker du att debiteras för körningar och använda upp transaktionerna i Cognitive Services-API:n.

Nu har du sett hur enkelt det är att integrera Functions i ett Logic Apps-arbetsflöde.

## <a name="disable-the-logic-app"></a>Inaktivera logikappen

Om du vill inaktivera logikappen klickar du på **Översikt** och sedan på **Inaktivera** längst upp på skärmen. Om du inaktiverar appen slutar den köras och medföra avgifter utan att ta bort appen.

![Funktionsloggar](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en API-resurs för Cognitive Services.
> * Skapa en funktion som kategoriserar tweetsentiment.
> * Skapa en logikapp som ansluter till Twitter.
> * Lägg till sentimentidetntifiering till logikappen. 
> * Anslut logikappen till funktionen.
> * Skicka ett e-postmeddelande baserat på svar från funktionen.

Gå vidare till nästa självstudie där du får lära dig att skapa en API utan server för din funktion.

> [!div class="nextstepaction"] 
> [Skapa ett API utan server med Azure Functions](functions-create-serverless-api.md)

Om du vill veta mer om Logic Apps läser du [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

