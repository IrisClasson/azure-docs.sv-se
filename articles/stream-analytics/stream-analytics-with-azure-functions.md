---
title: 'Självstudie: Kör Azure Functions med Azure Stream Analytics-jobb | Microsoft Docs'
description: I den här självstudien får du lära dig att konfigurera Azure Functions som kanalmottagare för Stream Analytics-jobb.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.workload: data-services
ms.date: 04/09/2018
ms.author: jasonh
ms.reviewer: jasonh
ms.openlocfilehash: 50ea4dafe7edfdeb851ad6d9cc42a7bca262e970
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985819"
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Köra Azure Functions från Azure Stream Analytics-jobb 

Du kan köra Azure Functions från Azure Stream Analytics genom att konfigurera Functions som en av kanalmottagarna för Stream Analytics-jobbet. Functions är en händelsedriven upplevelse med beräkning på begäran där du kan implementera kod som utlöses av händelser i Azure eller tjänster från tredje part. Azure Functions kapacitet att reagera på utlösare gör det till ett naturligt utdatamål för Stream Analytics-jobb.

Stream Analytics anropar Functions med HTTP-utlösare. Med utdataadaptern i Functions kan användarna ansluta Functions till Stream Analytics, så att händelserna kan utlösas baserat på Stream Analytics-frågor. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Stream Analytics-jobb
> * Skapa en Azure-funktion
> * Konfigurera Azure Function som utdata för dina jobb

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurera ett Stream Analytics-jobb att köra en funktion 

Det här avsnittet visar hur du konfigurerar ett Stream Analytics-jobb att köra en funktion som skriver data till Azure Redis Cache. Stream Analytics -jobbet läser händelser från Azure Event Hubs och kör en fråga som anropar funktionen. Funktionen läser data från Stream Analytics-jobbet och skriver dessa data till Azure Redis Cache.

![Diagram som visar relationer mellan Azure-tjänster](./media/stream-analytics-with-azure-functions/image1.png)

Följande steg krävs för detta:
* [Skapa ett Stream Analytics-jobb med Event Hubs som indata](#create-a-stream-analytics-job-with-event-hubs-as-input)  
* [Skapa en Azure Redis Cache-instans](#create-an-azure-redis-cache-instance)  
* [Skapa en funktion i Azure Functions som kan skriva data till Azure Redis Cache](#create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache)    
* [Uppdatera Stream Analytics-jobbet med funktionen som utdata](#update-the-stream-analytics-job-with-the-function-as-output)  
* [Kontrollera resultatet i Azure Redis Cache](#check-azure-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Skapa ett Stream Analytics-jobb med Event Hubs som indata

Följ anvisningarna i självstudien [Upptäck bedrägerier i realtid](stream-analytics-real-time-fraud-detection.md) och skapa en händelsehubb. Starta händelsegeneratorprogrammet och skapa Stream Analytics-jobbet. (Hoppa över stegen för att skapa frågan och utdata. Se i stället följande avsnitt för att konfigurera Functions-utdata.)

## <a name="create-an-azure-redis-cache-instance"></a>Skapa en Azure Redis Cache-instans

1. Skapa en cache i Azure Redis Cache med anvisningarna i [Skapa en cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. När du har skapat cachen under **Inställningar** väljer du **Åtkomstnycklar**. Skriv ner **den primära anslutningssträngen**.

   ![Skärmbild av Azure Redis Cache-anslutningssträng](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Skapa en funktion i Azure Functions som kan skriva data till Azure Redis Cache

1. Läs avsnittet [Skapa en funktionsapp](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) i dokumentationen till Functions. Du får anvisningar för hur du skapar en funktionsapp och en [HTTP-utlöst funktion i Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function) med språket CSharp.  

2. Gå till funktionen **run.csx**. Uppdatera den med nedanstående kod. (Ersätt ”\<din anslutningssträng för redis cache\>” med den primära Azure Redis Cache-anslutningssträng som du hämtade i föregående avsnitt.)  

   ```csharp
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
    }    

   ```

   När Stream Analytics tar emot undantaget ”För stor HTTP-förfrågningsentitet” från funktionen, minskar storleken på batcharna som skickas till Functions. I din funktion kan du använda följande kod för att se till att Stream Analytics inte skickar för stora batchar. Se till att de värden för maximalt antal batchar och maximal batchstorlek som används i funktionen stämmer överens med de värden som angetts i Stream Analytics-portalen.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Skapa en JSON-fil med namnet **project.json** i valfri textredigerare. Använd följande kod och spara den lokalt på din dator. Den här filen innehåller de NuGet-paketberoenden som krävs av C#-funktionen.  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. Gå tillbaka till Azure-portalen. Från fliken **Plattformsfunktioner** bläddrar du till din funktion. Under **Utvecklingsverktyg** väljer du **App Service Editor**. 
 
   ![Skärmbild av App Service Editor](./media/stream-analytics-with-azure-functions/image3.png)

5. I App Service Editor högerklickar du på din rotkatalog och laddar upp filen **project.json**. Uppdatera sidan när uppladdningen är klar. Du bör nu se en automatiskt genererad fil med namnet **project.lock.json**. Den automatiskt genererade filen innehåller referenser till de DLL-filer som anges i filen project.json.  

   ![Skärmbild av App Service Editor](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Uppdatera Stream Analytics-jobbet med funktionen som utdata

1. Öppna ditt Stream Analytics-jobb på Azure-portalen.  

2. Bläddra till din funktion och välj **Översikt** > **Utdata** > **Lägg till**. Om du vill lägga till en ny utdatakanal väljer du **Azure-funktion** för kanalmottagaralternativet. Den nya utdataadaptern för Functions är tillgänglig med följande egenskaper:  

   |**Egenskapsnamn**|**Beskrivning**|
   |---|---|
   |Utdataalias| Ett användarvänligt namn som du använder i jobbets fråga för att referera till utdata. |
   |Importalternativ| Du kan använda funktionen från den aktuella prenumerationen eller ange inställningarna manuellt om funktionen finns i en annan prenumeration. |
   |Funktionsapp| Namnet på din Functions-app. |
   |Funktion| Namnet på funktionen i din Functions-app (namnet på din run.csx-funktion).|
   |Max batchstorlek|Anger den maximala storleken för varje utdatabatch som skickas till din funktion. Som standard är värdet är inställt på 256 kB.|
   |Max batchantal|Anger det maximala antalet händelser i varje batch som skickas till funktionen. Standardvärdet är 100. Den här egenskapen är valfri.|
   |Nyckel|Gör att du kan använda en funktion från en annan prenumeration. Ange nyckelvärdet för att få åtkomst till din funktion. Den här egenskapen är valfri.|

3. Ange ett namn för utdataaliaset. I den här självstudien namnet vi det **saop1** (du kan välja vilket namn du vill). Fyll i övrig information.  

4. Öppna Stream Analytics-jobbet och uppdatera frågan till följande. (Se till att byta ut texten ”saop1” om du har gett utdatamottagaren ett annat namn.)  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Starta programmet telcodatagen.exe genom att köra följande kommando på kommandoraden (använd formatet `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000.2 2**
    
6.  Starta Stream Analytics-jobbet.

## <a name="check-azure-redis-cache-for-results"></a>Kontrollera resultatet i Azure Redis Cache

1. Bläddra till Azure-portalen och leta rätt på din Azure Redis Cache. Välj **Konsol**.  

2. Använd [Redis-cachekommandon](https://redis.io/commands) för att kontrollera att dina data finns i Redis Cache. (Kommandot har formatet Get {nyckel}.) Exempel:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Det här kommandot bör skriva ut värdet för den angivna nyckeln:

   ![Skärmbild av Azure Redis Cache-utdata](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Kända problem

När du försöker återställa värdet för Max batchstorlek Max batchantal till tomt (standard) i Azure-portalen ändras värdet tillbaka till det tidigare angivna värdet när du spara. Ange standardvärdena för de här fälten manuellt i det här fallet.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömningsenheter som förbrukas av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade.  
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du skapat ett enkelt Stream Analytics-jobb som kör en Azure-funktion. Om du vill veta mer om Stream Analytics-jobb kan du fortsätta till nästa kurs:

> [!div class="nextstepaction"]
> [Köra användardefinierade funktioner i Stream Analytics JavaScript-jobb](stream-analytics-javascript-user-defined-functions.md)