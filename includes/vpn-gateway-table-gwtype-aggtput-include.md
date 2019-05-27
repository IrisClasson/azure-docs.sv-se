---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 211935aac56dff8d6e524706c416c126b1a0c3b8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159312"
---
|**SKU**   | **S2S/VNet-till-VNet<br>tunnlar** | **P2S<br> SSTP-anslutningar** | **P2S<br> IKEv2/OpenVPN Connections** | **Prestandamått för<br>aggregerat datagenomflöde** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Basic** | Max. 10    | Max. 128  | Stöds inte  | 100 Mbit/s  | Stöds inte|
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbit/s  | Stöds |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbit/s    | Stöds |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbit/s | Stöds |


(*) Använd [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) om du behöver fler än 30 S2S VPN-tunnlar.

* Prestandamått för aggregerat dataflöde baseras på mätningar av flera tunnlar som aggregerats via en enda gateway. Prestandamåttet för aggregerat dataflöde för VPN Gateway är S2S + P2S kombinerat. **Om du har många P2S-anslutningar kan det påverka en S2S-anslutning negativt på grund av dataflödesbegränsningar.** Prestandamåttet för aggregerat datagenomflöde inte garanterat genomströmning på grund av villkor för Internet-trafik och dina program.

* Dessa anslutningsgränser är separata. Du kan exempelvis ha 128 SSTP-anslutningar och även 250 IKEv2-anslutningar på en VpnGw1-SKU.

* Information om priser finns på sidan [Priser](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Information om SLA (serviceavtal) finns på sidan [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1 VpnGw2 och VpnGw3 stöds endast för VPN-gatewayer som använder Resource Manager-distributionsmodellen.

* Basic SKU:n betraktas som en äldre SKU. Basic SKU:n har vissa funktionsbegränsningar. Du kan inte ändra storlek på en gateway som använder en Basic SKU till en av de nya gateway-SKU:erna. Du måste istället ändra till en ny SKU, vilket innebär att ta bort och återskapa din VPN-gateway. Kontrollera att funktionen du behöver stöds innan du använder Basic SKU:n.
