---
title: Skapa Docker-/Go-app i Linux – Azure App Service
description: Så här distribuerar du en Docker-avbildning som kör ett Go-program till Web App for Containers.
keywords: azure app service, web app, go, docker, container
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 3335163c15319e39858003032ae7113c9c26a3fd
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438588"
---
# <a name="deploy-a-dockergo-web-app-in-web-app-for-containers"></a>Distribuera en Docker-/Go-webbapp i Web App for Containers

[App Service Linux](app-service-linux-intro.md) tillhandahåller fördefinierade programstackar i Linux med stöd för språk som .NET, PHP, Node.js med flera. Du kan också använda en anpassad Docker-avbildning för att köra din webbapp på en programstack som inte redan har definierats i Azure. I den här snabbstarten får du lära dig hur man skapar en webbapp och distribuerar en Go-avbildning från Docker Hub. Du skapar webbappen med [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Exempelapp som körs i Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en [webbapp](../overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Glöm inte att ersätta `<app name>` med ett globalt unikt programnamn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

I det föregående kommandot pekar `--deployment-container-image-name` på den offentliga Docker Hub-avbildningen [microsoftazure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Bläddra till appen

```bash
http://<app_name>.azurewebsites.net/hello
```

![Exempelapp som körs i Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Grattis!** Du har distribuerat en anpassad Docker-avbildning som kör ett Go-program i Web App for Containers.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda anpassad Docker-avbildning](tutorial-custom-docker-image.md)