---
title: Mått och aviseringar för Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Förstå Azure Monitor mått och aviseringar som är tillgängliga för Virtual Network NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: e3c47a60a6cda074eba7b5c3292577c29f50c2ab
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424059"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-mått

Azure Virtual Network NAT-gateway-resurser innehåller flerdimensionella mått. Du kan använda dessa mått för att se hur det fungerar och vid [fel sökning](troubleshoot-nat.md).  Aviseringar kan konfigureras för kritiska problem som SNAT-belastningar.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*

## <a name="metrics"></a>Mått

NAT-gateway-resurser ger följande flerdimensionella mått i Azure Monitor:

| Mått | Beskrivning | Rekommenderad agg regering | Dimensioner |
|---|---|---|---|
| Byte | Antal byte som bearbetats inkommande och utgående | Summa | Riktning (i; Ut), protokoll (6 TCP; 17 UDP) |
| Skickas | Paket som har bearbetats inkommande och utgående | Summa | Riktning (i; Ut), protokoll (6 TCP; 17 UDP) |
| Ignorerade paket | Paket som släppts av NAT-gatewayen | Summa | / |
| Antal SNAT-anslutningar | Tillstånds över gångar per intervall | Summa | Anslutnings tillstånd, protokoll (6 TCP; 17 UDP) |
| Totalt antal SNAT-anslutningar | Aktuella aktiva SNAT-anslutningar (~ SNAT-portar som används) | Summa | Protokoll (6 TCP; 17-tums UDP) |


## <a name="alerts"></a>Aviseringar

Aviseringar för mått kan konfigureras i Azure Monitor för vart och ett av föregående [mått](#metrics).

## <a name="limitations"></a>Begränsningar

Resource Health stöds inte.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Virtual Network NAT](nat-overview.md)
* Lär dig om [NAT gateway-resurs](nat-gateway-resource.md)
* Läs mer om [Azure Monitor](../azure-monitor/overview.md)
* Lär dig mer om att [Felsöka resurser för NAT-gateway](troubleshoot-nat.md).
* [Berätta för oss vad du ska bygga härnäst för Virtual Network NAT i UserVoice](https://aka.ms/natuservoice).


