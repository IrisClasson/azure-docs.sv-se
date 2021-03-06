---
title: Om Azure VPN Gateway design
description: Lär dig mer om hur du kan utforma en VPN gateway-topologi för att ansluta till virtuella Azure-nätverk.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/01/2020
ms.author: cherylmc
ms.openlocfilehash: d7b9077af50115e912415d784dc98ace081c0c88
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300325"
---
# <a name="vpn-gateway-design"></a>VPN Gateway design

Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Du måste bestämma vilken konfiguration som passar bäst för dina behov. I avsnitten nedan kan du Visa design information och Topology-diagram om följande VPN gateway-anslutningar. Använd diagrammen och beskrivningarna för att välja den anslutningstopologi som passar dina behov. Diagrammen visar huvud bas linje topologier, men det är möjligt att bygga mer komplexa konfigurationer med diagrammen som rikt linjer.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)

### <a name="site-to-site"></a><a name="S2S"></a>Plats-till-plats

En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer. En S2S-anslutning kräver en VPN-enhet som finns lokalt och som har tilldelats en offentlig IP-adress. Om du vill ha information om att välja en VPN-enhet kan du läsa [vanliga frågor och svar om VPN Gateway – VPN-enheter](vpn-gateway-vpn-faq.md#s2s).

![Exempel på Azure VPN Gateway-anslutningar för plats-till-plats](./media/design/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a><a name="Multi"></a>Flera platser

Den här typen av anslutning är en variant av plats-till-plats-anslutningen. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste du använda en RouteBased VPN-typ (kallas även en ”dynamisk gateway” för klassiska virtuella nätverk). Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden. Den här typen av anslutning kallas ofta för en ”anslutning till flera platser”.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Distributionsmodeller och metoder för plats-till-plats och flera platser

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Punkt-till-plats-VPN

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk.

Till skillnad från S2S-anslutningar, kräver P2S-anslutningar en lokal offentlig IP-adress eller en VPN-enhet. P2S-anslutningar kan användas tillsammans med S2S-anslutningar via samma VPN-gateway, under förutsättning att alla konfigurationskrav för båda anslutningarna är kompatibla. Mer information om punkt-till-plats-anslutningar finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).

![Exempel på Azure VPN Gateway-anslutningar från punkt till plats](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Distributionsmodeller och -metoder för P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Anslutningar mellan virtuella nätverk (IPsec/IKE VPN-tunnel)

Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan till och med kombinera VNet-till-VNet-kommunikation med anslutningskonfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

De virtuella nätverk som du ansluter kan finnas:

* i samma eller olika regioner
* i samma eller olika prenumerationer 
* i samma eller olika distributionsmodeller

![Exempel på VNet-till-VNet-anslutningar med Azure VPN Gateway](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Anslutningar mellan distributionsmodeller

Azure har för närvarande två distributionsmodeller: klassisk och Resource Manager. Om du har använt Azure ett tag har du förmodligen virtuella Azure-datorer och instansroller som kör i ett klassiskt VNet. Dina nyare virtuella datorer och rollinstanser kanske körs i ett VNet som skapats i Resource Manager. Du kan skapa en anslutning mellan virtuella nätverk så att resurserna i ett VNet kan kommunicera direkt med resurserna i ett annat.

### <a name="vnet-peering"></a>VNet-peering

Du kan använda VNet-peering för att skapa anslutningen, förutsatt att ditt virtuella nätverk uppfyller vissa krav. Ingen VNet-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Distributionsmodeller och metoder för VNet-till-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (privat anslutning)

Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som tillhandahålls av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett valfritt-till-alla (IP VPN)-nätverk, ett Ethernet-nätverk från punkt till punkt eller en virtuell kors anslutning via en anslutnings leverantör till en samplacering.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

För ExpressRoute-anslutningar används en virtuell nätverksgateway som en del i den obligatoriska konfigurationen. I en ExpressRoute-anslutning konfigureras den virtuella nätverksgatewayen med gatewaytypen ”ExpressRoute” istället för ”Vpn”. Även om trafiken i en ExpressRoute-krets inte är krypterad som standard så kan du skapa en lösning där du skickar krypterad trafik i en ExpressRoute-krets. Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Plats-till-plats- och samexisterande ExpressRoute-anslutningar

ExpressRoute är en direkt, privat anslutning från ditt WAN (inte över offentliga internet) till Microsoft-tjänster, däribland Azure. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Det finns flera fördelar med att kunna konfigurera VPN-anslutningar för plats-till-plats- och ExpressRoute-anslutningar för samma virtuella nätverk.

Du kan konfigurera ett VPN för plats-till-plats som en säker redundansväxlingsväg för ExpressRoute, eller använda VPN för plats-till-plats för att ansluta till platser som inte finns i ditt nätverk, men som ansluts via ExpressRoute. Den här konfigurationen kräver två virtuella nätverksgateways för samma virtuella nätverk, en med gatewaytypen Vpn och en annan med gatewaytypen ExpressRoute.

![Exempel på samtidiga ExpressRoute- och VPN Gateway-anslutningar](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Distributionsmodeller och -metoder för samtidig S2S och ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Anslutningar med hög tillgänglighet

För planering och design för anslutningar med hög tillgänglighet, se [anslutningar med hög](vpn-gateway-highlyavailable.md)tillgänglighet.

## <a name="next-steps"></a>Nästa steg

* Mer information finns i avsnittet [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md).

* Läs mer om [konfigurations inställningar för VPN gateway](vpn-gateway-about-vpn-gateway-settings.md).

* VPN Gateway BGP-överväganden finns i [om BGP](vpn-gateway-bgp-overview.md).

* Läs [Prenumerations- och tjänstbegränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
