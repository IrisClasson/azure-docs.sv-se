---
title: ASP.NET Core med SQL Database på Linux-Azure App Service | Microsoft Docs
description: Lär dig hur du får en ASP.NET Core app som arbetar i Azure App Service på Linux, med anslutning till en SQL Database.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 532c6a45351f872260ea9383adaacacd486b9d9a
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532715"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Bygg en ASP.NET Core-och SQL Database-app i Azure App Service på Linux

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Linux. Om du vill distribuera en app till App Service i _Windows_ kan du läsa [Skapa en .NET Core- och SQL Database-app i Azure App Service](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

Med [App Service i Linux](app-service-linux-intro.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst som utgår från operativsystemet Linux. Den här självstudien visar hur du skapar en .NET Core-app och ansluter den till en SQL Database. När du är färdig har du en .NET Core MVC-app som körs i App Service i Linux.

![app som körs i App Service i Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa en SQL Database i Azure
> * ansluta en .NET Core-app till SQL Database
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * strömma diagnostikloggar från Azure
> * hantera appen i Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

* [Installera Git](https://git-scm.com/)
* [Installera .NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

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

![ansluter till SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Du kan när som helst stoppa .NET Core genom att trycka på `Ctrl+C` i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Skapa SQL Database för produktion

I det här steget skapar du en SQL Database i Azure. När appen har distribuerats till Azure används den här molndatabasen.

För SQL Database används [Azure SQL Database](/azure/sql-database/) i den här självstudien.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Skapa en logisk SQL Database-server

Skapa en logisk SQL Database-server i Cloud Shell med kommandot [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Ersätt plats hållaren *\<server namn >* med ett unikt SQL Database namn. Det här namnet används som en del av SQL Database-slutpunkten `<server-name>.database.windows.net`, så namnet måste vara unikt för alla logiska servrar i Azure. Namnet får endast innehålla gemener, siffror och bindestreck och måste vara mellan 3 och 50 tecken långt. Ersätt också *\<db-username >* och *\<db-Password >* med ett användar namn och lösen ord som du själv väljer. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

När den logiska SQL Database-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel på servernivå för Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) via kommandot[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Skapa en databas

Skapa en databas med en [S0-prestandanivå](../../sql-database/sql-database-service-tiers-dtu.md) på servern med kommandot [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Skapa anslutningssträng

Ersätt följande sträng med *\<server-namn >* , *\<db-username >* och *\<db-Password >* som du tidigare använde tidigare.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

Detta är anslutningssträngen för .NET Core-appen. Kopiera den för senare bruk.

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

I det här steget distribuerar du din SQL Database-anslutna .NET Core-app till App Service i Linux.

### <a name="configure-local-git-deployment"></a>Konfigurera lokal git-distribution

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa ett webbprogram

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-connection-string"></a>Konfigurera anslutnings sträng

Ange anslutningssträngar för din Azure-app med hjälp av kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. I följande kommando ersätter du *\<app-name >* , samt *\<connection-sträng >* parameter med den anslutnings sträng som du skapade tidigare.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

I ASP.NET Core kan du använda den här namngivna anslutnings strängen (`MyDbConnection`) med standard mönstret, till exempel vilken anslutnings sträng som anges i *appSettings. JSON*. I det här fallet har `MyDbConnection` också definierats i *appSettings. JSON*. När du kör i App Service prioriteras den anslutnings sträng som definieras i App Service över anslutnings strängen som definierats i *appSettings. JSON*. Koden använder *appSettings. JSON* -värdet under lokal utveckling och samma kod använder App Service-värdet när det distribueras.

Om du vill se hur anslutnings strängen refereras i din kod, se [Anslut till SQL Database i produktion](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Konfigurera miljö variabel

Därefter anger du appinställningen `ASPNETCORE_ENVIRONMENT` till _Produktion_. Med den här inställningen kan du se om du kör i Azure, eftersom du använder SQLite för din lokala utvecklings miljö och SQL Database för din Azure-miljö.

I följande exempel konfigureras appinställningen `ASPNETCORE_ENVIRONMENT` i Azure-appen. Ersätt plats hållaren *\<app-name >* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
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
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Om den här koden upptäcker att den körs i produktion (som anger Azure-miljön) använder den anslutnings strängen som du konfigurerade för att ansluta till SQL Database. Information om hur du använder appinställningar i App Service finns i [Access Environment-variabler](configure-language-dotnetcore.md#access-environment-variables).

@No__t_0-anropet hjälper dig när det körs i Azure, eftersom det automatiskt skapar de databaser som din .NET Core-app behöver, baserat på dess migrerings konfiguration.

Spara dina ändringar och genomför den på Git-lagringsplatsen.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen i webbläsaren.

```bash
http://<app-name>.azurewebsites.net
```

Lägg till några att-göra-uppgifter.

![app som körs i App Service i Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Grattis!** Du kör en datadriven .NET Core-app i App Service i Linux.

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och omdistribuera

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

Leta rätt på metoden `Create()` och lägg till `Done` i listan med egenskaper för attributet `Bind`. När du är klar ser signaturen för metoden `Create()` ut som följande kod:

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

När `git push` har slutförts kan du gå till Azure-appen och prova att använda de nya funktionerna.

![Azure-app efter Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Alla befintliga att-göra-uppgifter visas fortfarande. När du publicerar om .NET Core-appen går inte befintliga data i SQL Database förlorade. Med Entity Framework Core Migrations ändras endast dataschemat, så att befintliga data lämnas intakta.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

Exempelprojektet följer redan riktlinjerna i [ASP.NET Core-loggning i Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) med två konfigurationsändringar:

- Innehåller en referens till `Microsoft.Extensions.Logging.AzureAppServices` i *DotNetCoreSqlDb.csproj*.
- Anropar `loggerFactory.AddAzureWebAppDiagnostics()` i *Startup.cs*.

> [!NOTE]
> Projektets loggnivå är inställd på `Information` i *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Mer information om att anpassa ASP.NET Core-loggar finns i [Loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Hantera din Azure-app

Gå till [Azure-portalen](https://portal.azure.com) för att se den app du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på din Azure-apps namn.

![Portalnavigering till Azure-app](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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
> [Självstudie: mappa ett anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera ASP.NET Core app](configure-language-dotnetcore.md)
