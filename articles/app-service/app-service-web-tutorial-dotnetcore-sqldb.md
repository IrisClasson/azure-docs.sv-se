---
title: 'Självstudie: ASP.NET Core med SQL Database'
description: Lär dig hur du får igång en .NET Core-app som fungerar i Azure App Service med anslutning till en SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 3ad011529f8b4be90fc0c108a2049c30d1c69302
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897317"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Självstudie: Bygg en ASP.NET Core-och SQL Database-app i Azure App Service

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core- och SQL Database-app i Azure App Service i Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Med [App Service ](overview.md) får du en automatiskt uppdaterad webbvärdtjänst i Azure med hög skalbarhet. Den här självstudien visar hur du skapar en .NET Core-app och ansluter den till en SQL Database. När du är klar har du en .NET Core MVC-app som körs i App Service.

![app som körs i App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Du lär dig att:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-local-net-core-app"></a>Skapa en lokal .NET Core-app

I det här steget konfigurerar du det lokala .NET Core-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Använd kommandot `cd` för att komma till en arbetskatalog i terminalfönstret.

Kör följande kommandon för att klona exempellagringsplatsen och ändra dess rot.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Exempelprojektet innehåller en grundläggande CRUD-app (create-read-update-delete) med [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Köra programmet

Kör följande kommandon för att installera de nödvändiga paketen, köra databasmigreringar och starta programmet.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Gå till `http://localhost:5000` i en webbläsare. Välj länken **Skapa nytt** och skapa några _att-göra_-objekt.

![ansluter till SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Du kan när som helst stoppa .NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Skapa SQL Database för produktion

I det här steget skapar du en SQL Database i Azure. När appen har distribuerats till Azure används den här molndatabasen.

För SQL Database används [Azure SQL Database](/azure/sql-database/) i den här självstudien.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Skapa en logisk SQL Database-server

Skapa en logisk SQL Database-server i Cloud Shell med kommandot [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Ersätt platshållaren *\<server_name>* med ett unikt namn för SQL Database. Det här namnet används som en del av SQL Database-slutpunkten `<server_name>.database.windows.net`, så namnet måste vara unikt för alla logiska servrar i Azure. Namnet får endast innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken långt. Dessutom måste du ersätta *\<db_username>* och *\<db_password>* med ett giltigt användarnamn och lösenord. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

När den logiska SQL Database-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel på servernivå för Azure SQL Database](../sql-database/sql-database-firewall-configure.md) via kommandot[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Skapa en databas

Skapa en databas med en [S0-prestandanivå](../sql-database/sql-database-service-tiers-dtu.md) på servern med kommandot [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Skapa anslutningssträng

Ersätt följande sträng med det *\<server_name>* , *\<db_username>* och *\<db_password>* du använde tidigare.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Detta är anslutningssträngen för .NET Core-appen. Kopiera den för senare bruk.

## <a name="deploy-app-to-azure"></a>Distribuera appen till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Konfigurera anslutnings sträng

Ange anslutningssträngar för din Azure-app med hjälp av kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. I följande kommando ska du ersätta *\<app name>* och parametern *\<connection_string>* med den anslutningssträng du skapade tidigare.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

I ASP.NET Core kan du använda den här namngivna anslutnings strängen (`MyDbConnection`) med standard mönstret, till exempel vilken anslutnings sträng som anges i *appSettings. JSON*. I det här fallet har `MyDbConnection` också definierats i *appSettings. JSON*. När du kör i App Service prioriteras den anslutnings sträng som definieras i App Service över anslutnings strängen som definierats i *appSettings. JSON*. Koden använder *appSettings. JSON* -värdet under lokal utveckling och samma kod använder App Service-värdet när det distribueras.

Om du vill se hur anslutnings strängen refereras i din kod, se [Anslut till SQL Database i produktion](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Konfigurera miljö variabel

Därefter anger du appinställningen `ASPNETCORE_ENVIRONMENT` till _Produktion_. Med den här inställningen kan du se om du kör i Azure, eftersom du använder SQLite för din lokala utvecklings miljö och SQL Database för din Azure-miljö.

I följande exempel konfigureras appinställningen `ASPNETCORE_ENVIRONMENT` i Azure-appen. Ersätt platshållaren *\<app_name>* .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Om du vill se hur miljövariabeln refereras till i din kod, se [Anslut till SQL Database i produktion](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Ansluta till SQL Database i produktion

Öppna Startup.cs från din lokala lagringsplats och leta upp följande kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Ersätt den med följande kod, som använder de miljövariabler du konfigurerade tidigare.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Om den här koden upptäcker att den körs i produktion (som anger Azure-miljön) använder den anslutnings strängen som du konfigurerade för att ansluta till SQL Database.

`Database.Migrate()`-anropet hjälper dig när det körs i Azure, eftersom det automatiskt skapar de databaser som din .NET Core-app behöver, baserat på dess migrerings konfiguration. 

> [!IMPORTANT]
> För produktionsappar som behöver skala ut följer du bästa praxis i avsnittet om att [tillämpa migreringar i produktion](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

Spara dina ändringar och genomför den på Git-lagringsplatsen. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen i webbläsaren.

```bash
http://<app_name>.azurewebsites.net
```

Lägg till några att-göra-uppgifter.

![app som körs i App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Grattis!** Du kör en datadriven .NET Core-app i App Service.

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och distribuera om

I det här steget gör du en ändring i ditt databasschema och publicerar den till Azure.

### <a name="update-your-data-model"></a>Uppdatera datamodellen

Öppna _Models\Todo.cs_ i kodredigeraren. Lägg till följande egenskap i klassen `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Kör Code First Migrations lokalt

Kör några kommandon och gör uppdateringar i den lokala databasen.

```bash
dotnet ef migrations add AddProperty
```

Uppdatera den lokala databasen:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Använda den nya egenskapen

Gör några ändringar i koden så att du använder egenskapen `Done`. För att göra självstudien enklare ska du bara ändra vyerna `Index` och `Create` så att du ser hur egenskapen fungerar.

Öppna _Controllers\TodosController.cs_.

Leta rätt på metoden `Create([Bind("ID,Description,CreatedDate")] Todo todo)` och lägg till `Done` i listan med egenskaper för attributet `Bind`. När du är klar ser signaturen för metoden `Create()` ut som följande kod:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Öppna _Views\Todos\Create.cshtml_.

I Razor-koden bör du se ett `<div class="form-group">`-element för `Description` och sedan ett annat `<div class="form-group">`-element för `CreatedDate`. Direkt efter dessa två element ska du lägga till ett annat `<div class="form-group">`-element för `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Öppna _Views\Todos\Index.cshtml_.

Sök efter det tomma `<th></th>`-elementet. Lägg till följande Razor-kod direkt ovanför det här elementet:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Hitta elementet `<td>` som innehåller ”tag helpers” `asp-action`. Lägg till följande Razor-kod direkt ovanför det här elementet:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Det är allt som krävs för att du ska se ändringarna i vyerna `Index` och `Create`.

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Kör appen lokalt.

```bash
dotnet run
```

Öppna webbläsaren och navigera till `http://localhost:5000/`. Du kan nu lägga till en att-göra-uppgift och markera **Klart**. Den ska sedan visas på din startsida som en slutförd uppgift. Kom ihåg att vyn `Edit` inte innehåller fältet `Done` eftersom du inte ändrade vyn `Edit`.

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

När `git push` har slutförts går du till App Service-appen och försöker lägga till ett att göra-objekt och checken är **klar**.

![Azure-app efter Code First Migration](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Alla befintliga att-göra-uppgifter visas fortfarande. När du publicerar om .NET Core-appen går inte befintliga data i SQL Database förlorade. Med Entity Framework Core Migrations ändras endast dataschemat, så att befintliga data lämnas intakta.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

När ASP.NET Core-appen körs i Azure App Service kan du skicka konsolloggarna till Cloud Shell. På så sätt kan du få samma diagnostikmeddelanden för att felsöka programfel.

Exempelprojektet följer redan riktlinjerna i [ASP.NET Core-loggning i Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) med två konfigurationsändringar:

- Innehåller en referens till `Microsoft.Extensions.Logging.AzureAppServices` i *DotNetCoreSqlDb.csproj*.
- Anropar `loggerFactory.AddAzureWebAppDiagnostics()` i *program.cs*.

För att ange [loggnivå](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) för ASP.NET Core i App Service till `Information` från standardnivån `Error`använder du kommandot [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) i Cloud Shell.

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Projektets loggnivå är redan inställd på `Information` i *appsettings.json*.
> 

Starta loggströmningen genom att använda kommandot [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) i Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Uppdatera Azure-app i webbläsaren så hämtas webbtrafik när loggströmningen har startats. Du kan nu se konsolloggarna som skickas till terminalen. Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Om du vill stoppa logg strömningen när som helst, skriver du `Ctrl`+`C`.

Mer information om att anpassa ASP.NET Core-loggar finns i [Loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Hantera din Azure-app

Om du vill se den app som du skapade går du till [Azure Portal](https://portal.azure.com)och söker efter och väljer **app Services**.

![Välj App Services i Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

På sidan **app Services** väljer du namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. Sidans vänstra sida visar de olika konfigurations sidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma loggar från Azure till terminalen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie för att läsa hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)
