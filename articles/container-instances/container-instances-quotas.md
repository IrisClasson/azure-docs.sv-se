---
title: Kvoter och regional tillgänglighet för Azure Container Instances
description: Kvoter, begränsningar och regional tillgänglighet för tjänsten Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/15/2019
ms.author: danlep
ms.openlocfilehash: c676989b4b882f2b1887a1b6a5091b60027f61d0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328420"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Kvoter och regional tillgänglighet för Azure Container Instances

Alla Azure-tjänster har vissa standardgränser och kvoter för resurser och funktioner. Följande avsnitt beskriver standardresursgränserna för flera Azure Container Instances-resurser, samt tillgänglighet för tjänsten i Azure-regioner.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="feature-availability"></a>Funktionstillgänglighet

Azure Container Instances kan schemalägga både Windows- och Linux-behållare med samma API. Följande funktioner är dock för närvarande endast tillgängliga i grupper för Linux-containrar. Windows-stöd planeras.

* Flera containrar per containergrupp
* Volymmontering (Azure Files, emptyDir, GitRepo, hemlighet)
* Virtuellt nätverk (förhandsversion)
* GPU-resurser (förhandsversion)

## <a name="region-availability"></a>Regional tillgänglighet

Azure Container Instances är tillgängligt i följande regioner med angivna processor- och minnesgränser för varje containergrupp. Värden är aktuella vid tidpunkten för publiceringen. Använd API [Listfunktioner](/rest/api/container-instances/listcapabilities/listcapabilities) för uppdaterad information. 

Tillgänglighets- och resursbegränsningarna kan variera när du använder Azure Container Instances med ett [virtuellt nätverk](container-instances-vnet.md) (förhandsversion) eller med [GPU-resurser](container-instances-gpu.md) (förhandsversion).

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada, centrala; USA, centrala; USA, östra 2; USA, södra centrala | Linux | 4 | 16 |
| USA, östra; Europa, norra; Europa, västra; USA, västra; USA, västra 2 | Linux | 4 | 14 |
| Östra Japan | Linux | 2 | 8 |
| Australien, östra; Asien, sydöstra | Linux | 2 | 7 |
| Indien, centrala; Asien, östra; USA, norra centrala; Indien, södra | Linux | 2 | 3.5 |
| USA, östra; Europa, västra; USA, västra | Windows | 4 | 14 |
| Australien, östra; Kanada, centrala; Indien, centrala; USA, centrala; Asien, östra; USA, östra 2; Japan, östra; USA, norra centrala; Europa, centrala; USA, södra centrala; Indien, södra; Asien, sydöstra; USA, västra 2 | Windows | 2 | 3.5 |

Containerinstanser som har skapats inom dessa resursgränser finns i mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser. Du kan försöka lindra sådana distributionsfel genom att prova att distribuera instanser med lägre processor- och minnesinställningar. Du kan även prova att genomföra distributionen senare.

Informera teamet om ytterligare regioner som krävs eller ökade begränsningar för CPU/minne på [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Mer information om att felsöka distribution av behållarinstanser finns i [Troubleshoot deployment issues with Azure Container Instances](container-instances-troubleshooting.md) (Felsöka distributionsproblem med Azure Container Instances).

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om du vill begära en ökning av en eller flera resurser som kan ökas skickar du in en [Azure-supportbegäran][azure-support] (välj ”Kvot” för **Typ av problem**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
