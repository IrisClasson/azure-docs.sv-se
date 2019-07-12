---
title: Distribuera behållarinstanser till en Azure-nätverk
description: Lär dig hur du distribuerar behållargrupper till en ny eller befintlig Azure-nätverk.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: ba7eca6286a7de6a930819d89470fa9e069b8361
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839698"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuera behållarinstanser till en Azure-nätverk

[Azure-nätverk](../virtual-network/virtual-networks-overview.md) tillhandahåller säkra, privata nätverk för Azure och lokala resurser. Genom att distribuera behållargrupper i Azure-nätverk, kommunicera din behållare säkert med andra resurser i det virtuella nätverket.

Behållargrupper som distribuerats till en Azure-nätverk möjliggör scenarier som:

* Direkt kommunikation mellan behållargrupper i samma undernät
* Skicka [uppgiftsbaserade](container-instances-restart-policy.md) arbetsbelastning utdata från behållarinstanser till en databas i det virtuella nätverket
* Hämta innehåll för container instances från en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) i det virtuella nätverket
* Kommunikation mellan med virtuella datorer i det virtuella nätverket
* Kommunikation mellan med lokala resurser via en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Den här funktionen finns för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="virtual-network-deployment-limitations"></a>Begränsningar för distribution av virtuellt nätverk

Vissa begränsningar gäller när du distribuerar behållargrupper till ett virtuellt nätverk.

* För att distribuera grupper med behållare till ett undernät måste får inte undernätet innehålla några andra typer av resurser. Ta bort alla befintliga resurser från ett befintligt undernät innan du distribuerar behållargrupper till den, eller skapa ett nytt undernät.
* Du kan inte använda en [hanterad identitet](container-instances-managed-identity.md) i en behållargrupp som distribueras till ett virtuellt nätverk.
* På grund av de ytterligare nätverksresurser som ingår är distribuera en behållargrupp till ett virtuellt nätverk vanligtvis något långsammare än att distribuera en behållarinstans som standard.

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Den här funktionen är i förhandsversion, gäller följande begränsningar när du distribuerar behållargrupper till ett virtuellt nätverk. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Behållaren resursgränser kan skilja sig från gränser för icke-nätverksanslutna container instances i dessa regioner. För närvarande endast Linux-behållare har stöd för den här funktionen. Windows-stöd planeras.

### <a name="unsupported-networking-scenarios"></a>Nätverk scenarier som inte stöds 

* **Azure Load Balancer** -placera en Azure-belastningsutjämnare framför behållarinstanser på en nätverksansluten behållargrupp stöds inte
* **Virtuell nätverkspeering** – du kan inte peer-koppla ett virtuellt nätverk som innehåller ett undernät som delegerats till Azure Container Instances till ett annat virtuellt nätverk
* **Routningstabeller** -användardefinierade vägar kan inte ställas in i ett undernät som delegerats till Azure Container Instances
* **Nätverkssäkerhetsgrupper** -utgående säkerhetsregler i NSG: er som tillämpas för ett undernät som delegerats till Azure Container Instances som inte används 
* **Offentliga IP-Adressen eller DNS-etikett** -behållargrupper som distribueras till ett virtuellt nätverk stöder för närvarande inte vilket exponerade behållare direkt för internet med en offentlig IP-adress eller ett fullständigt kvalificerat domännamn
* **Intern namnmatchning** -namnmatchning för Azure-resurser i det virtuella nätverket via den interna Azure DNS stöds inte

**Nätverks-ta bort resursen** kräver [ytterligare steg](#delete-network-resources) när du har distribuerat behållargrupper till det virtuella nätverket.

## <a name="required-network-resources"></a>Resurser som krävs

Det finns tre Azure Virtual Network-resurser som krävs för att distribuera grupper med behållare till ett virtuellt nätverk: den [virtuellt nätverk](#virtual-network) , en [delegerad undernät](#subnet-delegated) inom det virtuella nätverket och en [network profil](#network-profile). 

### <a name="virtual-network"></a>Virtuellt nätverk

Ett virtuellt nätverk definierar det adressutrymme som du skapar ett eller flera undernät. Du sedan distribuera Azure-resurser (till exempel behållargrupper) till undernät i det virtuella nätverket.

### <a name="subnet-delegated"></a>Undernätet (delegerad)

Undernät segmentera det virtuella nätverket i separata adressutrymmen som kan användas av Azure-resurserna du placerar i dem. Du skapar ett eller flera undernät inom ett virtuellt nätverk.

Det undernät som du använder för behållargrupper får innehålla endast behållargrupper. Första gången du distribuerar en behållargrupp till ett undernät, delegerar Azure undernätet till Azure Container Instances. När delegeras kan undernätet användas endast för grupper med behållare. Om du försöker distribuera resurser än behållargrupper till en delegerad undernät, misslyckas åtgärden.

### <a name="network-profile"></a>Nätverksprofil

En nätverksprofil är en mall för konfiguration av nätverk för Azure-resurser. Den anger vissa Nätverksegenskaper för resurs, till exempel det undernät som den ska distribueras. När du använder den [az container skapa][az-container-create] kommandot för att distribuera en behållargrupp till ett undernät (och därmed ett virtuellt nätverk), Azure skapar en nätverksprofil åt dig. Du kan sedan använda den nätverksprofilen för framtida distributioner till undernätet. 

Om du vill använda en Resource Manager-mall, YAML-fil eller en programmatisk metod för att distribuera en behållargrupp till ett undernät, måste du ange den fullständiga resurs-ID för Resource Manager för en nätverksprofil. Du kan använda en profil som skapats tidigare med [az container skapa][az-container-create], eller skapa en profil med en Resource Manager-mall (se [mallexemplet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) och [referens](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Hämta ID för en tidigare skapad profil med den [az nätverket Profillista][az-network-profile-list] kommando. 

I följande diagram, har grupper med flera behållare distribuerats till ett undernät som delegerats till Azure Container Instances. När du har distribuerat en behållargrupp till ett undernät kan distribuera du ytterligare behållargrupper till den genom att ange samma nätverksprofil.

![Behållargrupper inom ett virtuellt nätverk][aci-vnet-01]

## <a name="deployment-scenarios"></a>Distributionsscenarier

Du kan använda [az container skapa][az-container-create] distribuera grupper med behållare till ett nytt virtuellt nätverk och låta Azure för att skapa de nödvändiga nätverksresurserna åt dig, eller distribuera till ett befintligt virtuellt nätverk. 

### <a name="new-virtual-network"></a>Nytt virtuellt nätverk

Om du vill distribuera till ett nytt virtuellt nätverk och Azure ska skapa nätverksresurser åt dig automatiskt, anger du följande när du kör [az container skapa][az-container-create]:

* Namn på virtuellt nätverk
* Vnet-adressprefix i CIDR-format
* Namn på undernät
* Adressprefix i undernät i CIDR-format

Det virtuella nätverket och undernätet adressprefix ange adressutrymmen för virtuellt nätverk och undernät, respektive. Dessa värden visas i Classless Inter-Domain Routing CIDR-notering, till exempel `10.0.0.0/16`. Mer information om hur du arbetar med undernät finns i [lägga till, ändra eller ta bort ett virtuellt nätverksundernät](../virtual-network/virtual-network-manage-subnet.md).

När du har distribuerat din första behållargrupp med den här metoden kan distribuera du i samma undernät genom att ange det virtuella nätverket och namn på undernät eller nätverksprofilen som Azure skapar automatiskt åt dig. Eftersom Azure delegerar undernätet till Azure Container Instances, du kan distribuera *endast* behållargrupper i undernätet.

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

Distribuera en behållargrupp till ett befintligt virtuellt nätverk:

1. Skapa ett undernät i det befintliga virtuella nätverket eller tömma ett befintligt undernät av *alla* andra resurser
1. Distribuera en behållargrupp med [az container skapa][az-container-create] och ange något av följande:
   * Namn på virtuellt nätverk och namn på undernät
   * Resurs-ID och undernät resurs-ID, vilket gör att använda ett virtuellt nätverk från en annan resursgrupp för virtuella nätverk
   * Profilnamn för nätverk eller -ID som du kan hämta med hjälp av [az nätverket Profillista][az-network-profile-list]

När du distribuerar din första behållargrupp till ett befintligt undernät, delegerar Azure undernätet till Azure Container Instances. Du kan inte längre distribuera resurser än behållargrupper till det undernätet.

## <a name="deployment-examples"></a>Exempel för distribution

I följande avsnitt beskrivs hur du distribuerar behållargrupper till ett virtuellt nätverk med Azure CLI. Dessa exempelkommandon formateras för den **Bash** shell. Om du föredrar en annan shell, till exempel PowerShell eller Kommandotolken, justerar du därefter rad fortsättning tecken.

### <a name="deploy-to-a-new-virtual-network"></a>Distribuera till ett nytt virtuellt nätverk

Först distribuera en behållargrupp och ange parametrar för ett nytt virtuellt nätverk och undernät. När du anger dessa parametrar kan Azure skapar det virtuella nätverk och undernät, delegerar undernätet till Azure Container instances och skapar även en nätverksprofil. När resurserna har skapats, har din behållargrupp distribuerats i undernätet.

Kör följande [az container skapa][az-container-create] kommando som definierar inställningar för ett nytt virtuellt nätverk och undernät. Du måste ange namnet på en resursgrupp som har skapats i en region som [stöder](#preview-limitations) behållargrupper i ett virtuellt nätverk. Det här kommandot distribuerar offentliga Microsoft [aci-helloworld][aci-helloworld] behållare som kör en liten Node.js-webbserver som betjänar en statisk webbsida. I nästa avsnitt ska du distribuera en andra behållargrupp i samma undernät och testa kommunikation mellan två behållarinstanserna.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

När du distribuerar till ett nytt virtuellt nätverk med hjälp av den här metoden kan distributionen ta några minuter medan nätverksresurserna skapas. Efter den första distributionen slutföra ytterligare grupp behållardistributioner snabbare.

### <a name="deploy-to-existing-virtual-network"></a>Distribuera till befintligt virtuellt nätverk

Nu när du har distribuerat en behållargrupp till ett nytt virtuellt nätverk kan distribuera en andra behållargrupp i samma undernät och kontrollera kommunikationen mellan två behållarinstanserna.

Hämta först IP-adressen för den första behållargrupp som du har distribuerat den *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

IP-adressen för behållargruppen ska visas i privat undernät:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Nu ska du ange `CONTAINER_GROUP_IP` till IP-adress som du hämtade med den `az container show` kommandot och kör följande `az container create` kommando. Den här andra behållaren *commchecker*, kör en Alpine Linux-baserad avbildning och kör `wget` mot IP-adressen för den första behållargruppen privat undernät.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

När den här andra behållardistribution har slutförts kan hämta loggar så att du kan se utdata från den `wget` den körde:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Om andra behållaren har kommunicerat med först, bör utdata se ut:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Till loggutdata ska visa som `wget` kunde ansluta och ladda ned indexfilen från den första behållaren med hjälp av dess privata IP-adress på det lokala undernätet. Nätverkstrafiken mellan de två behållargrupper som finns kvar i det virtuella nätverket.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Distribuera till befintligt virtuellt nätverk – YAML

Du kan också distribuera en behållargrupp till ett befintligt virtuellt nätverk med hjälp av en YAML-fil. Om du vill distribuera till ett undernät i ett virtuellt nätverk måste ange du flera ytterligare egenskaper i YAML:

* `ipAddress`: IP-adressinställningarna för behållargruppen.
  * `ports`: Portar som bör öppnas, om sådana.
  * `protocol`: Protokollet (TCP eller UDP) för porten som öppnade.
* `networkProfile`: Anger nätverksinställningar som det virtuella nätverk och undernät för en Azure-resurs.
  * `id`: Den fullständiga resurs-ID för Resource Manager för den `networkProfile`.

Om du vill distribuera en behållargrupp till ett virtuellt nätverk med en YAML-fil, måste du först hämta ID för nätverksprofilen. Kör den [az nätverket Profillista][az-network-profile-list] kommando och ange namnet på resursgruppen som innehåller ditt virtuella nätverk och delegerad undernät.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Kommandots utdata visar fullständigt resurs-ID för nätverksprofilen:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

När du har network profil-ID, kopiera följande YAML till en ny fil med namnet *vnet distribuera aci.yaml*. Under `networkProfile`, ersätter den `id` värdet med ID som du just hämtade, spara filen. Den här YAML skapar en behållargrupp med namnet *appcontaineryaml* i det virtuella nätverket.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Distribuera behållargrupp med den [az container skapa][az-container-create] kommando och ange namnet på YAML-filen för den `--file` parameter:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

När distributionen är klar, köra den [az container show][az-container-show] kommando för att visa dess status:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="delete-container-instances"></a>Ta bort behållarinstanser

När du är klar du arbetar med behållarinstanserna skapade, ta bort dem med följande kommandon:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Ta bort nätverksresurser

Den första förhandsversionen av den här funktionen kräver flera ytterligare kommandon för att ta bort resurserna du skapade tidigare. Om du använde de exempel på kommandona i föregående avsnitt i den här artikeln för att skapa ditt virtuella nätverk och undernät, kan du använda följande skript för att ta bort dessa nätverksresurser.

Innan du kör skriptet, ange den `RES_GROUP` variabeln med namnet på resursgruppen som innehåller det virtuella nätverk och undernät som ska tas bort. Uppdatera namnen på det virtuella nätverket och undernätet om du inte använde den `aci-vnet` och `aci-subnet` namn har föreslagits tidigare. Skriptet formateras för Bash-gränssnittet. Om du föredrar en annan shell, till exempel PowerShell eller Kommandotolken behöver du justeras variabeltilldelning och behöriga personer.

> [!WARNING]
> Det här skriptet tar bort resurser! Det tar bort det virtuella nätverket och alla undernät som den innehåller. Se till att du inte längre behöver *alla* resurser i det virtuella nätverket, inklusive alla undernät som den innehåller, innan du kör det här skriptet. En gång bort **dessa resurser är ett oåterkalleligt**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Nästa steg

Om du vill distribuera ett nytt virtuellt nätverk, undernät, nätverksprofil och behållargrupp med en Resource Manager-mall, [skapa en Azure container-grupp med virtuella nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Flera virtuella nätverksresurser och funktioner beskrivs i den här artikeln, även om en kort stund. Azure Virtual Network-dokumentationen beskriver hur de här ämnena stor utsträckning:

* [Virtuellt nätverk](../virtual-network/manage-virtual-network.md)
* [Undernät](../virtual-network/virtual-network-manage-subnet.md)
* [Serviceslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
