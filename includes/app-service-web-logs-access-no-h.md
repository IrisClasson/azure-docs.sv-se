---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187819"
---
Du kan komma åt konsolen loggar som genereras från i behållaren. Först aktiverar loggning genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

När containerloggning har aktiverats kör du följande kommando för att visa loggströmmen:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan även Granska loggfilerna från webbläsaren `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Om du vill stoppa loggströmningen när som helst, Skriv `Ctrl` + `C`.
