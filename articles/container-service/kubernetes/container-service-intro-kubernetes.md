---
title: (INAKTUELL) Introduktion till Azure Container Service för Kubernetes
description: Med Azure Container Service för Kubernetes kan du enkelt distribuera och hantera behållarbaserade program i Azure.
author: gabrtv
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: c0ef7255a087dd5dc26532316deab337f9eff715
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271572"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>(INAKTUELL) Introduktion till Azure Container Service för Kubernetes

> [!TIP]
> Den uppdaterade versionen av artikeln som använder Azure Kubernetes Service finns i [Översikt över Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Azure Container Service för Kubernetes gör det enkelt för dig att skapa, konfigurera och hantera av ett kluster på virtuella datorer som är förkonfigurerade för att köra program i behållare. På så sätt kan du använda dina befintliga kunskaper eller använda en stor och växande mängd communityexpertis för att distribuera och hantera containerbaserade program i Microsoft Azure.

Genom att använda Azure Container Service kan du dra nytta av Azures funktioner på företagsnivå samtidigt som programmen fortsätter att vara bärbara via Kubernetes och Docker-avbildningsformatet.

## <a name="using-azure-container-service-for-kubernetes"></a>Använda Azure Container Service för Kubernetes
Vårt mål med Azure Container Service är att tillhandahålla en behållarvärdmiljö genom att använda verktyg och teknik med öppen källkod som är populära bland kunderna idag. Därför tillgängliggör vi Kubernetes API-standardslutpunkter. Genom att använda dessa standardslutpunkter kan du använda all programvara som kan kommunicera till ett Kubernetes-kluster. Du kan till exempel välja [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) eller [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Skapa ett Kubernetes-kluster med Azure Container Service
Om du vill börja använda Azure Container Service distribuerar du ett Azure Container Service-kluster med [Azure CLI](container-service-kubernetes-walkthrough.md) eller via portalen (sök på Marketplace efter **Azure Container Service**). Om du är en avancerad användare som behöver mer kontroll över Azure Resource Manager-mallarna kan du öppna projektet [acs-engine](https://github.com/Azure/acs-engine) med öppen källkod för att bygga ett eget anpassat Kubernetes-kluster och distribuera det via `az` CLI.

### <a name="using-kubernetes"></a>Använda Kubernetes
Kubernetes automatiserar distributionen, skalningen och hanteringen av program som använder containrar. Det har en omfattande uppsättning funktioner. Till exempel:
* Automatisk paketering
* Självåterställning
* Horisontell skalning
* Tjänstidentifiering och belastningsutjämning
* Automatiserade distributioner och återställningar
* Hemlighets- och konfigurationshantering
* Storage-dirigering
* Batch-körning

Arkitekturdiagram över Kubernetes som distribuerats via Azure Container Service:

![Azure Container Service konfigurerat för Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Videoklipp

Stöd för Kubernetes i Azure Container Service (Azure fredag, januari 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Verktyg för utveckling och distribution av program på Kubernetes (Azure OpenDev, juni 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Nästa steg

Titta närmare på [Kubernetes-snabbstarten](container-service-kubernetes-walkthrough.md) för att börja utforska Azure Container Service idag.
