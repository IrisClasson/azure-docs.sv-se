---
title: Skapa en C# ASP.NET Core-webbapp – Azure App Service | Microsoft Docs
description: Lär dig hur du kör webbappar i Azure App Service genom att distribuera standard-C# ASP.NET Core-webbappen.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2762a81c9f366ad4a5d0d6d87cf04490fc10b23c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630592"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Skapa en ASP.NET Core-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en .NET Core-webbapp i App Service på Linux](./containers/quickstart-dotnetcore.md). 
>

Med [Azure App Service](overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet.  Den här snabbstarten visar hur du distribuerar din första ASP.NET Core-webbapp till Azure App Service. När du är klar har du en resursgrupp som består av en App Service-plan och en App Service-app med en distribuerad webbapp.

![](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra självstudien installerar du <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> med arbetsbelastningen **ASP.NET och webbutveckling**.

Om du redan har installerat Visual Studio 2017:

- Installera de senaste uppdateringarna i Visual Studio genom att klicka på **Hjälp** > **Sök efter uppdateringar**.
- Lägg till arbetsbelastningen genom att klicka på **Verktyg** > **Skaffa verktyg och funktioner**.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Skapa ett nytt projekt i Visual Studio genom att välja **Arkiv > Nytt > Projekt**. 

I dialogrutan **Nytt projekt** väljer du **Visual C# > Webb > ASP.NET Core-webbtillämpningsprogram**.

Ge programmet namnet _myFirstAzureWebApp_ och välj **OK**.
   
![Dialogrutan Nytt projekt](./media/app-service-web-get-started-dotnet/new-project.png)

Du kan distribuera alla typer av ASP.NET Core-webbappar till Azure. I den här snabbstarten väljer du mallen **Webbprogram** och ser till att autentiseringen är inställd på **Ingen autentisering** och att inget annat alternativ har valts.
      
Välj **OK**.

![Dialogrutan Nytt ASP.NET-projekt](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

På menyn väljer du **Felsöka > Starta utan felsökning** för att köra webbappen lokalt.

![Kör appen lokalt](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="launch-the-publish-wizard"></a>Starta publiceringsguiden

Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera**.

![Publicera från Solution Explorer](./media/app-service-web-get-started-dotnet/right-click-publish.png)

Publiceringsguiden startas automatiskt. Välj **App Service** > **Publicera** och öppna dialogrutan **Skapa App Service**.

![Publicera från projektöversiktssidan](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

I dialogrutan **Skapa App Service** klickar du på **Lägg till ett konto** och loggar in till din Azure-prenumeration. Välj det konto du vill ha i listrutan om du redan är inloggad.

> [!NOTE]
> Välj inte **Skapa** ännu om du redan är inloggad.
>
   
![Logga in på Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Ge resursgruppen namnet **myResourceGroup** och välj **OK**.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Bredvid **Värdplan** väljer du **Ny**. 

I dialogrutan **Konfigurera värdplan** använder du inställningarna i tabellen som följer skärmbilden.

![Skapa apptjänstplan](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| myAppServicePlan | Namnet på App Service-planen. |
| Plats | Västra Europa | Datacenter som är värd för webbappen. |
| Storlek | Kostnadsfri | [Prisnivån](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avgör tillgängliga värdfunktioner. |

Välj **OK**.

## <a name="create-and-publish-the-web-app"></a>Skapa och publicera webbappen

I **Appnamn** skriver du ett unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) eller accepterar det automatiskt genererade unika namnet. Webbadressen till webbappen är `http://<app_name>.azurewebsites.net`, där `<app_name>` är appens namn.

Välj **Skapa** för att börja skapa Azure-resurser.

![Konfigurera appnamn](./media/app-service-web-get-started-dotnet/web-app-name.png)

När guiden slutförs publiceras ASP.NET Core-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.

![Publicerad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Appnamnet som anges i steget [skapa och publicera](#create-and-publish-the-web-app) används som URL-prefixet i formatet `http://<app_name>.azurewebsites.net`.

Grattis, din ASP.NET Core-webbapp körs live i Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Uppdatera och distribuera om appen

Öppna _Pages/Index.cshtml_ från **Solution Explorer**.

Ersätt de två `<div>`-taggarna med följande kod:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Högerklicka på projektet **myFirstAzureWebApp** i **Solution Explorer** och välj **Publicera** för att distribuera om appen till Azure.

Välj **Publicera** på sammanfattningssidan för publicering.
![Sammanfattningssida för publicering i Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page.png)

När publiceringen är klar startar Visual Studio en webbläsare till webbappens URL.

![Uppdaterad ASP.NET-webbapp i Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera webbappen.

Klicka på **App Services** på menyn till vänster och välj sedan namnet på din Azure-webbapp.

![Portalnavigering till Azure-app](./media/app-service-web-get-started-dotnet/access-portal.png)

Nu visas sidan Översikt för din webbapp. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. 

![App Service-blad på Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Menyn till vänster innehåller olika sidor för att konfigurera appen. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [ASP.NET Core med SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
