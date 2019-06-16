---
title: Montera en Azure Files-volym i Azure Container Instances
description: Lär dig hur du monterar en Azure Files-volym för att bevara tillstånd med Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564029"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresurs i Azure Container Instances

Som standard är Azure Container Instances tillståndslösa. Om behållaren kraschar eller stoppas, förloras alla dess tillstånd. För att bevara tillstånd bortom livslängden för behållaren, måste du ansluta en volym från en extern lagring. Den här artikeln visar hur du monterar en Azure-filresurs som skapats med [Azure Files](../storage/files/storage-files-introduction.md) för användning med Azure Container Instances. Azure Files erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Med hjälp av en Azure-filresurs med Azure Container Instances innehåller fildelning funktioner ungefär som att använda en Azure-filresurs med Azure virtual machines.

> [!NOTE]
> Montera en Azure Files-resurs är för närvarande begränsade till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresurs med Azure Container Instances, måste du skapa den. Kör följande skript för att skapa ett lagringskonto för att vara värd för filresursen och dela själva. Lagringskontonamnet måste vara globalt unikt, så skriptet lägger till ett slumpmässigt värde till strängen som bas.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Hämta autentiseringsuppgifter för lagring

Om du vill montera en Azure-filresurs som en volym i Azure Container Instances, du behöver tre värden: namnet på lagringskontot, resursnamnet och åtkomstnyckel för lagring.

Om du använder skriptet ovan lagrades lagringskontonamnet i variabeln $ACI_PERS_STORAGE_ACCOUNT_NAME. Om du vill se namnet på kontot, skriver du:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Resursnamnet redan är känd (definieras som *acishare* i skriptet ovan), så att alla som återstår är nyckeln till lagringskontot, som finns med följande kommando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Distribuera behållare och montera volymen

Ange om du vill montera en Azure-filresurs som en volym i en behållare, resurs och volym monteringspunkt när du skapar behållaren med [az container skapa][az-container-create]. Om du har följt de föregående stegen, kan du montera filresursen du skapade tidigare med hjälp av följande kommando för att skapa en behållare:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Värdet `--dns-name-label` måste vara unikt i den Azure-region där du skapar containerinstansen. Uppdatera värdet i föregående kommando om du får en **DNS-Namnetiketten** felmeddelande när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer i monterad volym

När behållaren startas, kan du använda enkla webbappen distribueras via Microsoft [aci hellofiles] [ aci-hellofiles] bilden för att skapa små textfiler i Azure-filresursen på monteringssökväg som du har angett. Hämta webbappens fullständigt kvalificerade domännamnet (FQDN) med den [az container show] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Du kan använda den [Azure-portalen] [ portal] eller ett verktyg som de [Microsoft Azure Lagringsutforskaren] [ storage-explorer] att hämta och granska filen skrivs till filresursen.

## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en behållarinstans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups) eller en YAML-fil.

Om du vill använda en mall, ange resursinformationen och definiera volymer genom att fylla i `volumes` matrisen i den `properties` avsnitt i mallen. Exempel: Om du har skapat två Azure-filresurser med namnet *share1* och *share2* i storage-konto *myStorageAccount*, `volumes` matris skulle visas liknar följande:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Därefter för varje behållare i behållargruppen där du vill montera volymerna fylla det `volumeMounts` matrisen i den `properties` avsnitt i behållardefinitionen. Till exempel detta monterar två volymer *myvolume1* och *myvolume2*, tidigare definierad:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Ett exempel på distribution av behållarinstanser med en Azure Resource Manager-mall finns i [distribuera en behållargrupp](container-instances-multi-container-group.md). Ett exempel med hjälp av en YAML-fil, finns i [distribuera en grupp med flera behållare med YAML](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show