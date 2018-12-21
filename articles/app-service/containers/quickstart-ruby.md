---
title: Skapa en Ruby-webbapp i Linux – Azure App Service | Microsoft Docs
description: Lär dig att skapa en Ruby on Rails-app med App Service i Linux.
keywords: azure app service, linux, oss, ruby, rails
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: cfowler
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: cfowler
ms.custom: seodec18
ms.openlocfilehash: 13686951324a10c1de621f0fe507be062c9d2095
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252481"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Skapa en Ruby on Rails-app med App Service i Linux

Med [Azure App Service on Linux](app-service-linux-intro.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här snabbstarten visar hur du skapar en grundläggande [Ruby on Rails](https://rubyonrails.org/)-app som sedan kan distribueras till Azure som en Web App on Linux.

> [!NOTE]
> Ruby-utvecklingsstacken har bara stöd för Ruby on Rails just nu. Om du vill använda en annan plattform, som Sinatra, ska du gå igenom snabbstarten för [Web App for Containers](https://docs.microsoft.com/azure/app-service/containers/).

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installera Ruby 2.3 eller senare</a>
* <a href="https://git-scm.com/" target="_blank">Installera Git</a>

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando i ett terminalfönster för att klona databasen för exempelappen till den lokala datorn:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Kör programmet lokalt

Kör programmet lokalt så att du ser hur det ska se ut när du distribuerar det till Azure. Öppna ett terminalfönster, ändra till katalogen `hello-world` och använd kommandot `rails server` för att starta servern.

Första steget är att installera de gems du behöver. Det finns en `Gemfile` i exemplet, så du behöver inte ange vilka gems som ska installeras. Vi använder bundler till det här:

```
bundle install
```

När dina gems är installerade använder vi bundler till att starta appen:

```bash
bundle exec rails server
```

Navigera till `http://localhost:3000` i webbläsaren om du vill testa appen lokalt.

![Konfigurerad Hello World](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Gå till webbplatsen för att se din nya webbapp med den inbyggda avbildningen. Ersätt _&lt;app name>_ med namnet på din webbapp.

```bash
http://<app_name>.azurewebsites.net
```

Så här bör din nya webbapp se ut:

![Välkomstsida](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Distribuera appen

Kör följande kommandon för att distribuera den lokala appen till din Azure-webbplats:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Bekräfta att fjärrdistributionsåtgärderna lyckades. Kommandona producerar utdata som liknar följande:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

När distributionen är klar startar du om webbappen så att distributionen börjar gälla med hjälp av kommandot [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-restart):

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Gå till din webbplats och kontrollera resultatet.

```bash
http://<app name>.azurewebsites.net
```

![uppdaterad webbapp](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Försök att bläddra på webbplatsen medan appen startas om resulterar i HTTP-statuskoden `Error 503 Server unavailable`. Det kan ett par minuter att slutföra omstarten.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ruby on Rails med MySQL](tutorial-ruby-postgres-app.md)
