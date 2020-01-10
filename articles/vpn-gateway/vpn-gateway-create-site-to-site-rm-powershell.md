---
title: 'Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: plats-till-plats-VPN: PowerShell'
description: Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN Gateway med hjälp av PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: cherylmc
ms.openlocfilehash: 85ea3855b13350901d85701e9bca8d87ff6632c3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778812"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Skapa ett VNet med en VPN-anslutning för plats-till-plats med hjälp av PowerShell

Den här artikeln visar hur du kan använda PowerShell för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

![Diagram över plats-till-plats-anslutning med VPN Gateway](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="running-powershell-locally"></a>Köra PowerShell lokalt

Om du väljer att installera och använda PowerShell lokalt installerar du den senaste versionen av Azure Resource Managers PowerShell-cmdletar. PowerShell-cmdlets uppdateras ofta och vanligen måste du uppdatera dina PowerShell-cmdlets för att få den senaste funktionen. Om du inte uppdaterar dina PowerShell-cmdletar misslyckas värdena som har angetts. 

Kör "Get-module-ListAvailable AZ" för att hitta den version som du använder. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
Om du kör PowerShell lokalt måste du också köra "Connect-AzAccount" för att skapa en anslutning till Azure.


### <a name="example"></a>Exempelvärden

Vi använder följande värden i exemplen. Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="VNet"></a>1. skapa ett virtuellt nätverk och ett Gateway-undernät

Om du inte redan har ett virtuellt nätverk, skapa ett. När du skapar ett virtuellt nätverk ska du kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket. 

>[!NOTE]
>För att detta VNet ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras inte trafiken som du förväntar dig. Om du dessutom vill ansluta detta VNet till ett annat VNet kan inte adressutrymmet överlappa med andra VNet. Var noga med att planera din nätverkskonfiguration på lämpligt sätt.
>
>

### <a name="about-the-gateway-subnet"></a>Om gateway-undernätet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>Skapa ett virtuellt nätverk och ett Gateway-undernät

I det här exemplet skapas ett virtuellt nätverk och ett gateway-undernät. Om du redan har ett virtuellt nätverk som du behöver lägga till ett gateway-undernät för kan du läsa [Så här lägger du till ett gateway-undernät i ett virtuellt nätverk som du redan har skapat](#gatewaysubnet).

Skapa en resursgrupp:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Skapa ditt virtuella nätverk.

1. Ange variablerna.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. Skapa VNet.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="gatewaysubnet"></a>Så här lägger du till ett gateway-undernät i ett virtuellt nätverk som du redan har skapat

Använd stegen i det här avsnittet om du redan har ett virtuellt nätverk, men behöver lägga till ett gatewayundernät.

1. Ange variablerna.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Skapa gateway-undernätet.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Ange konfigurationen.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## 2. <a name="localnet"> </a>skapa den lokala Nätverksgatewayen

Den lokala Nätverksgatewayen (LNG) refererar vanligt vis till din lokala plats. Den är inte samma som en virtuell nätverksgateway. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras.

Ange följande värden:

* *GatewayIPAddress* är IP-adressen till den lokala VPN-enheten.
* *AddressPrefix* är ditt lokala adressutrymme.

Så här lägger du till en lokal nätverksgateway med ett enda adressprefix:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Så här lägger du till en lokal nätverksgateway med flera adressprefix:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Så här ändrar du IP-adressprefixen för en lokal nätverksgateway:

Ibland ändras prefixen för din lokala nätverksgateway. Vilka steg du utför för att ändra IP-adressprefixen beror på om du har skapat en VPN Gateway-anslutning. Se artikelavsnittet [Ändra IP-adressprefix för en lokal nätverksgateway](#modify).

## <a name="PublicIP"></a>3. begär en offentlig IP-adress

En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. 

VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Detta innebär dock inte att IP-adressen ändras när den har tilldelats till din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

Begär en offentlig IP-adress som ska tilldelas till din virtuella nätverks-VPN-gateway.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>4. skapa konfigurationen för Gateway-IP-adressering

Gateway-konfigurationen definierar under nätet ("GatewaySubnet") och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>5. Skapa VPN-gatewayen

Skapa den virtuella VPN-nätverksgatewayen.

Ange följande värden:

* *-GatewayType* för en plats-till-plats-konfiguration är *Vpn*. Gateway-typen gäller alltid den konfiguration som du implementerar. Andra gateway-konfigurationer kan kräva -GatewayType ExpressRoute.
* *-VpnType* kan vara *RouteBased* (kallas en dynamisk gateway i viss dokumentation) eller *PolicyBased* (kallas en statisk gateway i viss dokumentation). Mer information om VPN Gateway-typer finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).
* Markera den gateway SKU som du vill använda. Det finns konfigurationsbegränsningar för vissa SKU:er. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information. Om du får ett felmeddelande när du skapar VPN-gatewayen för GatewaySku, kontrollerar du att du har installerat den senaste versionen av PowerShell-cmdlets.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

När du har kört det här kommandot kan det ta upp till 45 minuter innan gatewaykonfigurationen har slutförts.

## <a name="ConfigureVPNDevice"></a>6. Konfigurera VPN-enheten

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar din VPN-enheten behöver du följande objekt:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Använd följande exempel för att hitta den offentliga IP-adressen för din virtuella nätverksgateway med hjälp av PowerShell. I det här exemplet är VNet1GWPIP namnet på den offentliga IP-adressresurs som du skapade i ett tidigare steg.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>7. Skapa VPN-anslutningen

Därefter skapar du VPN-anslutningen från plats till plats mellan din virtuella nätverksgateway och din VPN-enhet. Se till att ersätta värdena med dina egna. Den delade nyckeln måste överensstämma med värdet som du använde vid din konfiguration av VPN-enheten. Observera att '-ConnectionType' för plats-till-plats är **IPsec**.

1. Ange variablerna.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Skapa anslutningen.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

Efter en kort stund har anslutningen upprättats.

## <a name="toverify"></a>8. kontrol lera VPN-anslutningen

Det finns några olika metoder för att verifiera VPN-anslutningen.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Så här ändrar du IP-adressprefixen för en lokal nätverksgateway

Om IP-adressprefixen du vill ska dirigeras till din lokala plats ändras kan du ändra din lokala nätverksgateway. Det finns två uppsättningar med instruktioner. Vilken du väljer beror på om du redan har skapat din gateway-anslutning. När du använder dessa exempel ändrar du värdena så att de överensstämmer med din miljö.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Så här ändrar du gateway-IP-adressen för en lokal nätverksgateway

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Information om hur du skapar en VPN-anslutning från plats till plats med en Azure Resource Manager-mall finns i [Skapa en plats-till-plats-anslutning via VPN](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Information om hur du skapar en VPN-anslutning mellan två virtuella nätverk med en Azure Resource Manager-mall finns i [Distribuera HBase-georeplikering](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
