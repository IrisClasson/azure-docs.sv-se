---
title: 'Snabb start: Konfigurera en enhets etablerings tjänst med Azure CLI'
description: Azure Snabbstart – Konfigurera Azure IoT Hub Device Provisioning-tjänsten med Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ef40d0df630fc369705a1365aa8d95317aa54cb3
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904707"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Snabb start: Konfigurera IoT Hub Device Provisioning Service med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI för att skapa en IoT-hubb och en IoT Hub-enhetsetableringstjänst och hur du kopplar ihop de två tjänsterna. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Både IoT-hubben och etableringstjänsten som du skapar i den här snabbstarten kommer att vara offentligt identifierbara som DNS-slutpunkter. Undvik känslig information om du vill ändra de namn som används för dessa resurser.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *my-sample-resource-group* på platsen *usavästra*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en resursgrupp på platsen USA, västra. Du kan visa en lista över tillgängliga platser genom att köra kommandot `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Skapa en IoT-hubb med kommandot [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

I följande exempel skapas en IoT-hubb med namnet *my-sample-hub* på platsen *usavästra*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Skapa en etableringstjänst

Skapa en etableringstjänst med kommandot [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create). 

I följande exempel skapas en etableringstjänst med namnet *my-sample-dps* på platsen *usavästra*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> I exemplet skapas en etableringstjänst på platsen USA, västra. Du kan se en lista med tillgängliga platser genom att köra kommandot `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, eller genom att gå till sidan [Azure-status](https://azure.microsoft.com/status/) och söka efter ”enhetsetableringstjänst”. I kommandon kan platser anges antingen i ett ord eller med flera ord. exempel: väst, västra USA, västra USA osv. Värdet är inte Skift läges känsligt. Om du använder flera ord för att ange platsen skriver du värdet inom citattecken; till exempel `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

Du behöver IoT-hubbens anslutningssträng för att kunna länka den till enhetsetableringstjänsten. Använd kommandot [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) för att hämta anslutningssträngen. Använd sedan dess utdata för att ställa in en variabel som ska användas när du länkar de två resurserna. 

I följande exempel anges variabeln *hubConnectionString* till värdet för anslutningssträngen för den primära nyckeln i hubbens *iothubowner*-princip. Du kan ange en annan princip med parametern `--policy-name`. Kommandot använder Azure CLI-[frågan](/cli/azure/query-azure-cli) och [utdata](/cli/azure/format-output-azure-cli#tsv-output-format)alternativ till att extrahera anslutningssträngen från kommandots utdata.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Du kan använda kommandot `echo` om du vill se anslutningssträngen.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Dessa två kommandon är giltiga för en värd som körs under Bash. Om du använder ett lokalt Windows-/CMD-gränssnitt eller en PowerShell-värd, måste du ändra kommandona till korrekt syntax för den miljön.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Länka IoT-hubben och etableringstjänsten

Länka IoT-hubben och etableringstjänsten med kommandot [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

I följande exempel länkas en IoT-hubb med namnet *my-sample-hub* på platsen *westus* och en enhetsetableringstjänst med namnet *my-sample-dps*. Den använder anslutningssträngen för *my-sample-hub* som lagrades i variabeln *hubConnectionString* i föregående steg.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Kontrollera etableringstjänsten

Hämta information om etableringstjänsten med kommandot [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

I följande exempel hämtas information om en etableringstjänst med namnet *my-sample-dps*. Den länkade IoT-hubben visas i samlingen *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du vill fortsätta med efterföljande snabbstarter eller självstudier låter du bli att rensa resurserna som du har skapat i den här snabbstarten. Om du inte tänker fortsätta kan du använda följande kommandon för att ta bort etableringstjänsten, IoT-hubben eller resursgruppen och alla dess resurser.

Ta bort etableringstjänsten genom att köra kommandot [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Ta bort IoT-hubben genom att köra kommandot [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Ta bort en resursgrupp och alla dess resurser genom att köra kommandot [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en IoT-hubb och en instans av Device Provisioning-tjänsten och länkat de två resurserna. Om du vill lära dig hur du använder den här konfigurationen för att etablera en simulerad enhet fortsätter du till Snabbstart för att skapa en simulerad enhet.

> [!div class="nextstepaction"]
> [Snabbstart för att skapa en simulerad enhet](./quick-create-simulated-device.md)
