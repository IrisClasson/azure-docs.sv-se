---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/10/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b864e5374dcb0bab321109e7b12ee8946adf0c05
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849521"
---
Skapa en [webbapp](../articles/app-service/containers/app-service-linux-intro.md) i `myAppServicePlan` App Service-planen. 

I Cloud Shell kan du använda kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest). Ersätt `<app-name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. Körningen har angetts till `RUBY|2.3`. Om du vill se alla körningar som stöds ska du köra [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Du har skapat en tom ny webbapp med git-distribution aktiverad.

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>
