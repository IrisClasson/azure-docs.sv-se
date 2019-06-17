---
title: Skapa behållaravbildning för Azure teknisk tillgångar | Azure Marketplace
description: Skapa tekniska resurser för en Azure-behållare.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794347"
---
# <a name="prepare-your-container-technical-assets"></a>Förbereda din behållare tekniska resurser

Den här artikeln beskriver stegen och krav för att konfigurera ett erbjudande med behållare på Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Granska den [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) dokumentationen som innehåller Snabbstarter, självstudier och exempel.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Utforma, bygga och testa dessa tillgångar ta tid och kräver teknisk kunskap av både Azure-plattformen och de tekniker som används för att skapa erbjudandet.
 
Förutom att din lösning domän, bör ingenjörsteamet ha kunskaper om följande Microsoft-tekniker:

-   Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/) 
-   Så här [utforma och skapa Azure-program](https://azure.microsoft.com/solutions/architecture/)
-   Kunskaper om [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
-   Kunskaper om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Föreslaget verktyg

Välj en eller båda av följande skript miljöer för att hantera din behållaravbildning:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar dessutom att lägga till följande verktyg i utvecklingsmiljön:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Tillägg: [Verktyget Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Tillägg: [Tjärgropar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Tillägg: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också granska de tillgängliga verktyg i den [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/) sidan och, om du använder Visual Studio i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållaravbildningen

Se följande för mer information:

* [Självstudie: Skapa en behållaravbildning för distribution till Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Självstudie: Skapa och distribuera behållaravbildningar i molnet med Azure Container Registry uppgifter](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Nästa steg

[Skapa ditt erbjudande för behållare](./cpp-create-offer.md)
