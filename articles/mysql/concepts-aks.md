---
title: Ansluta till Azure Kubernetes service – Azure Database for MySQL
description: Lär dig mer om att ansluta Azure Kubernetes-tjänsten med Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 712bf702ba355ec3b2ca6184c33da8489f8a178e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519872"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Ansluta Azure Kubernetes-tjänsten och Azure Database for MySQL

Azure Kubernetes service (AKS) tillhandahåller ett hanterat Kubernetes-kluster som du kan använda i Azure. Nedan visas några alternativ att tänka på när du använder AKS och Azure Database for MySQL tillsammans för att skapa ett program.


## <a name="accelerated-networking"></a>Snabbare nätverk
Använd accelererade nätverk-aktiverade underliggande virtuella datorer i ditt AKS-kluster. När accelererat nätverk har Aktiver ATS på en virtuell dator finns det kortare svars tider, färre Darr och minskad processor användning på den virtuella datorn. Lär dig mer om hur påskyndat nätverk fungerar, vilka OS-versioner som stöds och vilka virtuella dator instanser som stöds för [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Från november 2018 har AKS stöd för accelererat nätverk på de virtuella dator instanser som stöds. Accelererat nätverk är aktiverat som standard på nya AKS-kluster som använder de virtuella datorerna.

Du kan kontrol lera om ditt AKS-kluster har accelererat nätverk:
1. Gå till Azure Portal och välj ditt AKS-kluster.
2. Välj fliken Egenskaper.
3. Kopiera namnet på **infrastruktur resurs gruppen**.
4. Använd Portal Sök fältet för att hitta och öppna infrastruktur resurs gruppen.
5. Välj en virtuell dator i resurs gruppen.
6. Gå till fliken **nätverk** på den virtuella datorn.
7. Bekräfta om det **accelererade nätverket** är aktiverat.

Eller via Azure CLI med följande två kommandon:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Utdata blir den genererade resurs gruppen som AKS skapar med nätverks gränssnittet. Ta namnet "nodeResourceGroup" och Använd det i nästa kommando. **EnableAcceleratedNetworking** är antingen sant eller falskt:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="next-steps"></a>Nästa steg
- [Skapa ett Azure Kubernetes Service-kluster](../aks/kubernetes-walkthrough.md)
- Lär dig hur du [installerar WordPress från ett Helm-diagram med OSBA och Azure Database for MySQL](../aks/integrate-azure.md)
