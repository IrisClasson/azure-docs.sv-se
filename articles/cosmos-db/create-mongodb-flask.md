---
title: Skapa en Flask-webbapp med Azure Cosmos DB:s API för MongoDB och Python SDK
description: Presenterar ett Python Flask-kodexempel som du kan använda för att ansluta till och ställa frågor men hjälp av Azure Cosmos DB:s API för MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 2bd8fa81d0825e604c42c54c0f789b7939206804
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756944"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Snabb start: bygga en python-app med Azure Cosmos DB s API för MongoDB

> [!div class="op_single_selector"]
> * [NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.

Den här snabb starts guiden använder följande [mätkolv-exempel](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) och visar hur du skapar en enkel att göra-app med [Azure Cosmos DB emulatorn](local-emulator.md) och Azure Cosmos DBS API för MongoDB.

## <a name="prerequisites"></a>Krav

- Hämta [Azure Cosmos DB-emulatorn](local-emulator.md). Emulatorn stöds för tillfället endast på Windows. Exemplet visar hur du kan använda exemplet med en produktionsnyckel från Azure, vilket går att göra på valfri plattform.

- Om du inte redan har Visual Studio Code installerat kan du snabbt installera [VS-kod](https://code.visualstudio.com/Download) för din plattform (Windows, Mac, Linux).

- Tänk på att lägga till Python-språkstöd genom att installera ett av de populära Python-tilläggen.
  1. Välj ett tillägg.
  2. Installera tillägget genom att skriva in `ext install` i kommandopaletten `Ctrl+Shift+P`.

     Exemplen i det här dokumentet använder Don Jayamannes populära [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) med fullständiga funktioner.

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Flask-MongoDB-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt.

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Kör följande kommando för att installera python-modulerna.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Öppna mappen i Visual Studio Code.

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Kör webbappen](#run-the-web-app). 

Följande kodavsnitt hämtas från filen app.py och använder anslutningssträngen för den lokala Azure Cosmos DB-emulatorn. Lösenordet måste delas upp på det sätt som visas nedan för att möjliggöra de snedstreck som annars inte kan parsas.

* Initiera MongoDB-klienten, hämta databasen och autentisera.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Hämta samlingen eller skapa den om den inte redan finns.

    ```python
    todos = db.todo #Select the collection
    ```

* Skapa appen

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Kör webbappen

1. Kontrollera att Azure Cosmos DB-emulatorn körs.

2. Öppna ett terminalfönster och `cd` till den katalog där appen sparas.

3. Ange sedan miljövariabeln för kolv-appen med `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` för PowerShell-redigerare eller `export FLASK_APP=app.py` om du använder en Mac-dator. 

4. Kör appen med `flask run` och bläddra till [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Lägg till och ta bort uppgifter och se hur de läggs till och ändras i samlingen.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Om du vill testa koden mot ett aktivt Cosmos-konto går du till Azure Portal för att skapa ett konto och hämta information om anslutningssträngen. Kopiera den till appen.

1. Klicka på **Anslutningssträng** och därefter på **Läs- och skrivnycklar** i den vänstra navigeringen i ditt Cosmos-konto i [Azure Portal](https://portal.azure.com/). Använd kopieringsknapparna till höger på skärmen och kopiera Användarnamn, Lösenord och Värd till filen Dal.cs i nästa steg.

2. Öppna filen **app.py** i rotkatalogen.

3. Kopiera ditt **användarnamn** från portalen (med kopieringsknappen) och gör det till värdet för **name** i filen **app.py**.

4. Kopiera sedan **anslutningssträngen** från portalen och gör den till värdet för MongoClient i filen **app.py**.

5. Kopiera slutligen **lösenordet** från portalen och gör det till värdet för **password** i filen **app.py**.

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Cosmos DB. Du kan köra den på samma sätt som innan.

## <a name="deploy-to-azure"></a>Distribuera till Azure

Om du vill distribuera den här appen kan du skapa en ny webbapp i Azure och aktivera kontinuerlig distribution med en förgrening av den här GitHub-lagringsplatsen. Följ den här [självstudien](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) om du vill konfigurera kontinuerlig distribution med GitHub i Azure.

När du distribuerar till Azure bör du ta bort dina programnycklar och kontrollera att avsnittet nedan inte har kommenterats bort:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Du måste sedan lägga till MONGOURL, MONGO_PASSWORD och MONGO_USERNAME i programinställningarna. Följ den här [självstudien](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) om du vill veta mer om programinställningar i Azure Web Apps.

Om du inte vill skapa en förgrening av den här lagringsplatsen kan du även klicka på knappen nedan för att distribuera till Azure. Du bör sedan gå till Azure och konfigurera programinställningarna med dina uppgifter för Cosmos DB-kontot.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Om du planerar att lagra din kod i GitHub eller något annat alternativ för källåtkomstkontroll måste du tänka på att ta bort anslutningssträngarna från koden. De kan anges med programinställningar för webbappen i stället.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Cosmos-konto och kör en Flask-app. Du kan nu importera ytterligare data till din Cosmos-databas. 

> [!div class="nextstepaction"]
> [Importera MondoDB-data till Azure Cosmos DB](mongodb-migrate.md)
