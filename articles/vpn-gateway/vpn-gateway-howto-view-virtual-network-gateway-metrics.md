---
title: Visa Azure VPN Gateway mått
description: Steg för att Visa VPN Gateway mått
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532147"
---
# <a name="view-vpn-gateway-metrics"></a>Visa VPN Gateway mått

Du kan övervaka Azure VPN-gatewayer med Azure Monitor. Den här artikeln beskriver mått som är tillgängliga via portalen. Måtten är lätta att använda för att ge stöd för scenarier i nästan real tid, vilket gör dem användbara för avisering och snabb problem identifiering.


|**Mått**   | **Enhet** | **Precision** | **Beskrivning** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/s  | 5 minuter| Genomsnittlig användning av kombinerad bandbredd för alla plats-till-plats-anslutningar på gatewayen.     |
|**P2SBandwidth**| Byte/s  | 1 minut  | Genomsnittlig användning av kombinerad bandbredd för alla punkt-till-plats-anslutningar på gatewayen.    |
|**P2SConnectionCount**| Count  | 1 minut  | Antal punkt-till-plats-anslutningar på gatewayen.   |
|**TunnelAverageBandwidth** | Byte/s    | 5 minuter  | Genomsnittlig bandbredds användning av tunnlar som skapats på gatewayen. |
|**TunnelEgressBytes** | Byte | 5 minuter | Utgående trafik på tunnlar som skapats på gatewayen.   |
|**TunnelEgressPackets** | Count | 5 minuter | Antal utgående paket för tunnlar som skapats på gatewayen.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minuter | Antal utgående paket som utelämnats i tunnlar som orsakas av trafik matchnings fel. |
|**TunnelIngressBytes** | Byte | 5 minuter | Inkommande trafik på tunnlar som skapats på gatewayen.   |
|**TunnelIngressPackets** | Count | 5 minuter | Antal inkommande paket för tunnlar som skapats på gatewayen.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minuter | Antal inkommande paket som har släppts i tunnlar som orsakas av trafik matchnings fel. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Följande steg hjälper dig att hitta och visa mått:

1. Navigera till resursen för den virtuella Nätverksgatewayen i portalen
2. Välj **Översikt** för att se de totala ingångs-och utgångs måtten för tunnel.

   ![Val för att skapa en varnings regel](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Vy")

3. För att visa alla andra mått som anges ovan. Klicka på avsnittet **mått** under din virtuella nätverksgateway-resurs och välj måttet i den nedrullningsbara listan.

   ![Välj-knappen och VPN-gatewayen i listan över resurser](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Välj")

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar aviseringar för tunnel mått finns i [Konfigurera aviseringar på VPN Gateway mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Information om hur du konfigurerar aviseringar för tunnel resurs loggar finns i [Konfigurera aviseringar på VPN gateway resurs loggar](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
