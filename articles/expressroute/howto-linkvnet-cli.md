---
title: 'Azure-ExpressRoute: länka ett virtuellt nätverk till en krets: CLI'
description: Den här artikeln visar hur du länka virtuella nätverk (Vnet) till ExpressRoute-kretsar med hjälp av distributionsmodellen resurshanteraren och CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: a8814030e6c4345227ec05ea1554104e0b21efbc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076541"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av CLI

Den här artikeln får du länka virtuella nätverk (Vnet) till Azure ExpressRoute-kretsar med hjälp av CLI. Om du vill länka med Azure CLI, måste de virtuella nätverken skapas med hjälp av Resource Manager-distributionsmodellen. De kan antingen vara i samma prenumeration eller en del av en annan prenumeration. Om du vill använda en annan metod för att ansluta ditt VNet till en ExpressRoute-krets, kan du välja en artikel i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Du behöver den senaste versionen av kommandoradsgränssnittet (CLI). Mer information finns i [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Du behöver gå igenom den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern. 
  * Kontrollera att du har Azure privat peering har konfigurerats för din krets. Se den [konfigurera routning](howto-routing-cli.md) artikeln routning anvisningar. 
  * Kontrollera att Azures privata peering har konfigurerats. BGP-peering mellan ditt nätverk och Microsoft måste vara upp så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Kontrollera att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ anvisningarna för att [konfigurera en virtuell nätverksgateway för ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Se till att använda `--gateway-type ExpressRoute`.

* Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets som standard. Alla virtuella nätverken måste finnas i samma geopolitiska region när du använder en standard ExpressRoute-krets. 

* Ett enskilt virtuellt nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd de här processerna för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsar kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Om du aktiverar ExpressRoute premium-tillägget kan du länka ett virtuellt nätverk utanför den geopolitiska regionen för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-kretsen. Mer information om premium-tillägget finns i den [vanliga frågor och svar](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets

Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av exemplet. Se till att den virtuella nätverksgatewayen har skapats och är redo för att länka innan du kör kommandot.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets

Du kan dela en ExpressRoute-krets över flera prenumerationer. Bilden nedan visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av de mindre moln inom det stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation. Var och en av avdelningarna i organisationen kan använda sina egna prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Avgifter för anslutning och bandbredd för dedikerade kretsen tillämpas till ExpressRoute-krets ägare. Alla virtuella nätverk delar samma bandbredden.
> 
> 

![Anslutningen mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - krets ägare och Kretsanvändare

Kretsen ägare är en behörig Power-användare i ExpressRoute-krets resursen. Kretsägaren kan skapa auktoriseringar som kan lösas genom att ”Kretsanvändare”. Kretsanvändare är ägare till virtuella nätverksgatewayer som inte är inom samma prenumeration som ExpressRoute-kretsen. Kretsanvändare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Kretsägaren har rätt att ändra och återkalla auktoriseringar när som helst. När en auktorisering har återkallats, tas alla anslutningar bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Skapa en auktorisering**

Kretsägaren skapar tillstånd, vilket skapar en auktoriseringsnyckeln som kan användas av en Kretsanvändare för att ansluta sina virtuella nätverksgatewayer för ExpressRoute-kretsen. En auktorisering är giltig för endast en anslutning.

I följande exempel visar hur du skapar en auktorisering:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Svaret innehåller auktoriseringsnyckeln och status:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Granska auktoriseringar**

Kretsägaren kan granska alla auktoriseringar som utfärdas på en viss krets genom att köra följande exempel:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Att lägga till auktoriseringar**

Kretsägaren kan lägga till tillstånd med hjälp av följande exempel:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Att ta bort auktoriseringar**

Kretsägaren kan återkalla/ta bort auktoriseringar för användaren genom att köra följande exempel:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Kretsen användaråtgärder

Kretsen användaren måste peer-ID och en auktoriseringsnyckeln från Kretsägaren. Auktoriseringsnyckeln är ett GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Att kunna lösa in en anslutningsauktorisering**

Kretsen-användare kan köra i följande exempel för att lösa in en länk-auktorisering:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Att släppa en anslutningsauktorisering**

Du kan släppa en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverksanslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverksanslutning. 

**Uppdatera anslutningsvikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från mer än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skicka trafik till det här prefixet, du kan ändra *RoutingWeight* för en anslutning. Trafik skickas på anslutningen med den högsta *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Intervallet för *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurera ExpressRoute-FastPath 
Du kan aktivera [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) om ExpressRoute-kretsen finns på [ExpressRoute Direct](expressroute-erdirect-about.md) och den virtuella nätverk-gatewayen är Ultra Performance eller ErGw3AZ. FastPath förbättrar data Sök vägs förformingen, till exempel paket per sekund och anslutningar per sekund mellan ditt lokala nätverk och ditt virtuella nätverk. 

> [!NOTE] 
> Om du redan har en virtuell nätverks anslutning men inte har aktiverat FastPath måste du ta bort den virtuella nätverks anslutningen och skapa en ny. 
> 
>  

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```


## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
