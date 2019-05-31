---
title: Använda Azure Storage som en Terraform-serverdel
description: En introduktion till att lagra Terraform tillstånd i Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 89108f02957990ad1c7eb736f39de89537f06db5
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297922"
---
# <a name="store-terraform-state-in-azure-storage"></a>Tillstånd för Store-Terraform i Azure Storage

Terraform tillstånd används för att stämma av distribuerade resurser med Terraform-konfigurationer. Med hjälp av tillstånd, vet Terraform vilka Azure-resurser att lägga till, uppdatera eller ta bort. Som standard Terraform tillstånd lagras lokalt när du kör *Terraform gäller*. Den här konfigurationen är inte idealiskt för olika orsaker:

- Lokal state fungerar inte bra i ett team eller en samarbetsmiljö
- Terraform tillstånd kan innehålla känslig information
- Lagra tillstånd lokalt ökar risken för oavsiktlig borttagning

Terraform innehåller konceptet med en serverdel för tillstånd, vilket är Fjärrlagring för Terraform tillstånd. När du använder en tillstånd serverdel, lagras tillståndsfilen i ett datalager som Azure Storage. Det här dokumentet beskriver hur du konfigurerar och använder Azure Storage som Terraform tillstånd serverdel.

## <a name="configure-storage-account"></a>Konfigurera storage-konto

Ett storage-konto måste skapas innan du använder Azure Storage som serverdel. Storage-konto kan skapas med Azure portal, PowerShell, Azure CLI eller Terraform själva. Använd följande exempel för att konfigurera storage-konto med Azure CLI.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Anteckna det lagringskontonamn och behållarnamn lagringsåtkomstnyckeln. Dessa värden krävs när du konfigurerar fjärråtkomst tillstånd.

## <a name="configure-state-backend"></a>Konfigurera tillstånd serverdel

Terraform tillstånd serverdelen konfigureras när du kör *Terraform init*. Följande data krävs för att kunna konfigurera tillstånd serverdelen.

- storage_account_name - namnet på Azure Storage-kontot.
- container_name - namnet på blob-behållaren.
- nyckel – namnet på delstaten lagra filen som ska skapas.
- access_key - åtkomstnyckel för lagring.

Var och en av dessa värden kan anges i konfigurationsfilen Terraform eller på kommandoraden, men det rekommenderas att använda en miljövariabel för den `access_key`. Nyckeln med hjälp av en miljövariabel förhindrar skrivs till disk.

Skapa miljövariabeln `ARM_ACCESS_KEY` med värdet för Azure Storage-åtkomstnyckel.

```console
export ARM_ACCESS_KEY=<storage access key>
```

För att ytterligare skydda åtkomstnyckel för Azure Storage-konto kan du lagra den i Azure Key Vault. Miljövariabeln kan sedan anges med ett kommando som liknar följande. Mer information om Azure Key Vault finns i den [dokumentationen för Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Om du vill konfigurera Terraform för att använda serverdelen måste innehålla en *serverdel* med en typ av *azurerm* inuti Terraform-konfigurationen. Lägg till den *storage_account_name*, *container_name*, och *nyckel* värden till configuration-block.

I följande exempel konfigurerar en Terraform-serverdel och skapar en Azure-resursgrupp. Ersätt värdena med värden från din miljö.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Nu kan initiera konfigurationen med *Terraform init* och kör sedan konfigurationen med *Terraform gäller*. När du är klar, hittar du tillståndsfilen i Azure Storage Blob.

## <a name="state-locking"></a>Statliga låsning

När du använder en Azure Storage Blob för lagring av användartillstånd, låses automatiskt blob före en åtgärd som skriver tillstånd. Den här konfigurationen hindrar flera samtidiga åtgärder, vilket kan orsaka skador. Mer information finns i [tillstånd låsning] [ terraform-state-lock] om Terraform-dokumentationen.

Låset visas när du granskar bloben genom Azure portal eller andra Azure-hanteringsverktyg.

![Azure-blob med lås](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Vilande kryptering

Som standard krypteras data som lagras i en Azure Blob innan som sparats till lagringsinfrastrukturen. När Terraform behöver tillstånd, är det hämtas från serverdelen och lagras i minnet i utvecklingssystemet. I den här konfigurationen är tillståndet skyddas i Azure Storage och skrivs inte till den lokala hårddisken.

Läs mer om Azure Storage kryptering [Azure Storage Service Encryption för vilande data][azure-storage-encryption].

## <a name="next-steps"></a>Nästa steg

Mer information om Terraform backend-konfiguration på den [Terraform serverdel dokumentation][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
