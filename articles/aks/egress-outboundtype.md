---
title: Anpassa användardefinierade vägar (UDR) i Azure Kubernetes service (AKS)
description: Lär dig hur du definierar en anpassad utgående väg i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: 2ffe9d525e92fa2154889cea43f681a0f31a18ab
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214228"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Anpassa utgående kluster med en användardefinierad väg

Utgående från ett AKS-kluster kan anpassas så att de passar vissa scenarier. Som standard tillhandahåller AKS en standard-SKU Load Balancer som ska ställas in och användas för utgående trafik. Men standard installationen kanske inte uppfyller kraven i alla scenarier om offentliga IP-adresser inte tillåts eller om ytterligare hopp krävs för utgående trafik.

Den här artikeln beskriver hur du anpassar ett klusters utgående väg för att stödja anpassade nätverks scenarier, till exempel sådana som inte tillåter offentliga IP-adresser och kräver att klustret placeras bakom en virtuell nätverks installation (NVA).

## <a name="prerequisites"></a>Krav
* Azure CLI-version 2.0.81 eller senare
* API-version av `2020-01-01` eller större


## <a name="limitations"></a>Begränsningar
* OutboundType kan bara definieras i klustrets skapande tid och kan inte uppdateras efteråt.
* Inställningen `outboundType` kräver AKS-kluster med `vm-set-type` `VirtualMachineScaleSets` och `load-balancer-sku` `Standard` .
* Inställningen `outboundType` till värdet `UDR` kräver en användardefinierad väg med giltig utgående anslutning för klustret.
* Inställningen `outboundType` till värdet innebär att `UDR` den inkommande käll-IP-vägen till belastningsutjämnaren **inte matchar** klustrets utgående mål adress för utgående trafik.

## <a name="overview-of-outbound-types-in-aks"></a>Översikt över utgående typer i AKS

Ett AKS-kluster kan anpassas med en unik `outboundType` typ belastningsutjämnare eller användardefinierad routning.

> [!IMPORTANT]
> Utgående typ påverkar bara utgående trafik i klustret. Mer information finns i Konfigurera ingångs [styrenheter](ingress-basic.md).

> [!NOTE]
> Du kan använda en egen [routningstabell][byo-route-table] med UDR och Kubernetes-nätverk. Se till att kluster identiteten (tjänstens huvud namn eller hanterad identitet) har deltagar behörighet till den anpassade väg tabellen.

### <a name="outbound-type-of-loadbalancer"></a>Utgående typ av loadBalancer

Om `loadBalancer` är inställt, slutför AKS följande konfiguration automatiskt. Belastningsutjämnaren används för utgående trafik genom en AKS tilldelad offentlig IP. En utgående typ som `loadBalancer` stöder Kubernetes-tjänster av typen `loadBalancer` , som förväntar sig utgående från belastningsutjämnaren som skapats av AKS-resurs leverantören.

Följande konfiguration görs av AKS.
   * En offentlig IP-adress har allokerats för utgående kluster.
   * Den offentliga IP-adressen är tilldelad till belastnings Utjämnings resursen.
   * Backend-pooler för belastningsutjämnaren konfigureras för agent-noder i klustret.

Nedan är en nätverkstopologi som distribueras i AKS-kluster som standard, som använder `outboundType` sig av `loadBalancer` .

![outboundtype – lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Utgående typ av userDefinedRouting

> [!NOTE]
> Använd utgående typ är ett avancerat nätverks scenario och kräver rätt nätverks konfiguration.

Om `userDefinedRouting` är inställt konfigurerar AKS inte utgående sökvägar automatiskt. Den utgående installationen måste göras av dig.

AKS-klustret måste distribueras till ett befintligt virtuellt nätverk med ett undernät som tidigare har kon figurer ATS på grund av att det inte går att använda en SLB-arkitektur (standard Load Balancer). Därför kräver den här arkitekturen explicit sändning av utgående trafik till en enhet som en brand vägg, Gateway, proxy eller för att tillåta NAT (Network Address Translation) att utföras av en offentlig IP-adress som tilldelas till standard belastnings utjämningen eller enheten.

AKS Resource Provider kommer att distribuera en standard Load Balancer (SLB). Belastningsutjämnaren är inte konfigurerad med några regler och [debiteras inte förrän en regel har placerats](https://azure.microsoft.com/pricing/details/load-balancer/). AKS etablerar inte automatiskt en offentlig IP-adress för SLB-klient **organisationen** eller automatiskt konfigurerar backend-poolen för belastningsutjämnare.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuera ett kluster med utgående typ av UDR och Azure-brandvägg

För att illustrera programmet för ett kluster med utgående typ med en användardefinierad väg, kan ett kluster konfigureras i ett virtuellt nätverk med en Azure-brandvägg i sitt eget undernät. Se det här exemplet på [exemplet begränsa utgående trafik med Azure Firewall](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Den utgående typen av UDR kräver att det finns en väg för 0.0.0.0/0 och nästa hopp mål för NVA (virtuell nätverks installation) i routningstabellen.
> Routningstabellen har redan ett standardvärde 0.0.0.0/0 till Internet, utan en offentlig IP-adress till SNAT som du inte kommer att ange för att lägga till den här vägen. AKS validerar att du inte skapar en router med 0.0.0.0/0 som pekar på Internet utan i stället för NVA eller gateway osv.
> 
> När du använder en utgående typ av UDR, skapas ingen offentlig IP-adress för belastningsutjämnaren om inte en tjänst av typen *Loadbalancer* har kon figurer ATS.

## <a name="next-steps"></a>Nästa steg

Se [Översikt över Azure Networking-UDR](../virtual-network/virtual-networks-udr-overview.md).

Se [hur du skapar, ändrar eller tar bort en](../virtual-network/manage-route-table.md)routningstabell.

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
