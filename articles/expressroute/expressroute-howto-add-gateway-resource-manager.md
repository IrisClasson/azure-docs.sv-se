---
title: 'Azure-ExpressRoute: Lägg till en gateway till ett VNet: PowerShell'
description: Den här artikeln hjälper dig att lägga till VNet Gateway till ett redan skapat Resource Manager VNet för ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 58d2949a18bfbf2800ae6ab4ac74b02b05b3eb07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736398"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurera en virtuell nätverksgateway för ExpressRoute med PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Den här artikeln hjälper dig att lägga till, ändra storlek på och ta bort en gateway för virtuella nätverk (VNet) för ett redan befintligt VNet. Stegen för den här konfigurationen gäller för virtuella nätverk som skapades med distributions modellen Resource Manager för en ExpressRoute-konfiguration. Mer information finns i [om virtuella Nätverksgatewayen för ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Innan du börjar

### <a name="working-with-powershell"></a>Arbeta med PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Konfigurations referens lista

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt VNet till en ExpressRoute-krets. Se [Länka en Virtual Network till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).
