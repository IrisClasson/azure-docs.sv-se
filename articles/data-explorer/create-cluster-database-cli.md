---
title: Skapa ett Azure Datautforskaren-kluster & DB med Azure CLI
description: Lär dig hur du skapar ett Azure Data Explorer-kluster och en databas med hjälp av Azure CLI
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 6b8c2924e50da095c3bc5c7db2d2bf48ef5a27c2
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561960"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Skapa ett Azure Datautforskaren-kluster och-databas med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Portalen](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-mall](create-cluster-database-resource-manager.md)

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst för realtidsanalys av stora mängder data som strömmar från program, webbplatser, IoT-enheter med mera. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. I den här artikeln skapar du ett kluster och en databas med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna slutföra den här artikeln. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt, kräver den här artikeln Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

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
   | namn | *azureclitest* | Önskat namn på klustret.|
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
   | namn | *clidatabase* | Namn på databasen.|
   | resource-group | *testrg* | Namnet på resursgruppen där klustret kommer att skapas. |
   | soft-delete-period | *P365D* | Indikerar hur lång tid det tar för data att behållas för frågor. Se [bevarande princip](/azure/kusto/concepts/retentionpolicy) för mer information. |
   | hot-cache-period | *P31D* | Indikerar hur lång tid data lagras i cacheminnet. Mer information finns i [cache-principen](/azure/kusto/concepts/cachepolicy) . |

1. Kör följande kommando för att se den databas som du skapade:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Nu har du ett kluster och en databas.

## <a name="clean-up-resources"></a>Rensa resurser

* Behåll de resurser du har skapat om du planerar att följa våra andra artiklar.
* Ta bort klustret om du vill rensa resurser. När du tar bort ett kluster, raderas också alla databaser i den. Använd följande kommando för att ta bort klustret:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Nästa steg

* [Mata in data med Azure Datautforskaren python-biblioteket](python-ingest-data.md)
