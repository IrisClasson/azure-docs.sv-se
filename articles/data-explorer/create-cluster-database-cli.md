---
title: Skapa ett Azure Data Explorer-kluster och en databas med hjälp av Azure CLI
description: Lär dig hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Azure CLI
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e771def95db00b5de8c27011641a628560952970
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494790"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Skapa ett Azure Data Explorer-kluster och en databas med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här artikeln skapar du ett kluster och en databas med hjälp av Azure CLI.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure-prenumeration för att slutföra den här artikeln. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt måste du ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Konfigurera CLI-parametrarna

Följande steg krävs inte om du kör kommandon i Azure Cloud Shell. Om du kör CLI lokalt följer du de här stegen för att logga in i Azure och ange din aktuella prenumeration:

1. Kör följande kommandon för att logga in på Azure:

    ```azurecli-interactive
    az login
    ```

1. Konfigurera den prenumeration där du vill att klustret ska skapas. Ersätt `MyAzureSub` med namnet på den Azure-prenumeration som du vill använda:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Skapa Azure Data Explorer-klustret

1. Skapa klustret med hjälp av följande kommando:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | name | *azureclitest* | Önskat namn på klustret.|
   | sku | *D13_v2* | Den SKU som ska användas för klustret. |
   | resource-group | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |

    Det finns ytterligare parametrar som du kan använda, till exempel kapaciteten för klustret.

1. Kör följande kommando för att kontrollera om klustret har skapats:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Om resultatet innehåller `provisioningState` med värdet `Succeeded` har klustret skapats.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Skapa databasen i Azure Data Explorer-klustret

1. Skapa databasen med hjälp av följande kommando:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Inställning** | **Föreslaget värde** | **Fältbeskrivning**|
   |---|---|---|
   | cluster-name | *azureclitest* | Namnet på det kluster där databasen ska skapas.|
   | name | *clidatabase* | Namn på databasen.|
   | resource-group | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | soft-delete-period | *P365D* | Anger hur lång tid som data förblir tillgängliga för frågor. Se [bevarandeprincip](/azure/kusto/concepts/retentionpolicy) för mer information. |
   | hot-cache-period | *P31D* | Anger hur lång tid som data sparas i cacheminnet. Se [cachelagra princip](/azure/kusto/concepts/cachepolicy) för mer information. |

1. Kör följande kommando för att se den databas som du skapade:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Om du planerar att följa våra andra artiklar, bevara alla resurser som du skapade.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Nästa steg

* [Mata in data med hjälp av Azure Data Explorer Python-bibliotek](python-ingest-data.md)
