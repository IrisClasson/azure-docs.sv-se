---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 5205ce86e7933b5c48df1e24e3714e988cedecc6
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246280"
---
Skapa en [webbapp](../articles/app-service/containers/app-service-linux-intro.md) i `myAppServicePlan` App Service-planen. 

I Cloud Shell kan du använda kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest). Ersätt `<app_name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. Körningen har angetts till `NODE|6.9`. Om du vill se alla körningar som stöds ska du köra [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Du har skapat en tom webbapp med git-distribution aktiverad.

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>
