---
title: Skapa din första funktion med Azure CLI
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning med Azure CLI och Azure Functions Core Tools.
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 147ad4bd20ee1c7ae8f1529e1b3bc0e4f3e7dbb0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230846"
---
# <a name="quickstart-create-your-first-function-from-the-command-line-using-azure-cli"></a>Snabb start: skapa din första funktion från kommando raden med hjälp av Azure CLI

I den här snabbstarten får du hjälp med att skapa din första funktion från kommandoraden eller terminalen. Du kan använda Azure CLI till att skapa en funktionsapp, som är den [serverfria](https://azure.microsoft.com/solutions/serverless/) infrastruktur som är värd för funktionen. Funktionskodprojektet genereras från en mall med hjälp av [Azure Functions Core Tools](functions-run-local.md) som också används för att distribuera funktionsapprojektet till Azure.

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator.

## <a name="prerequisites"></a>Krav

Innan du kör exemplet måste du ha följande:

+ Installera [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare.

+ Installera [Azure CLI](/cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2,0 eller senare. Kör `az --version` för att se vilken version du har. Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Funktionsappen är en miljö för serverfri körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<APP_NAME>` och lagringskontonamnet istället för `<STORAGE_NAME>`. `<APP_NAME>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Du bör även ange `<language>`-körningen för din funktionsapp från `dotnet` (C#) eller `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

Genom att konfigurera parametern _consumption-plan-location_ anger du att funktionsappen ska använda en förbrukningsplan som värd. I det här serverlösa prenumerationsavtalet läggs resurser till dynamiskt när funktionerna har behov av det och du betalar endast när funktionerna används. Mer information finns i [Välja rätt värdplan](functions-scale.md).

När funktionsappen har skapats visas information som liknar följande exempel i Azure CLI:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

