---
title: DC/OS-agentpooler för Azure Container Service
description: Hur offentliga och privata agentpooler fungerar med Azure Container Service DC/OS-kluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9c614d18b96c182fa166a4bc43fb1bb2f8d5d6f5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976747"
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>DC/OS-agentpooler för Azure Container Service
DC/OS-kluster i Azure Container Service innehåller agentnoder i två pooler, poolen offentliga och privata poolen. Ett program kan bara distribueras till antingen poolen, påverkar tillgängligheten mellan datorer i container service. Datorerna kan exponerade mot internet (offentlig) eller behöll intern (privat). Den här artikeln ger en kort översikt över varför det är offentliga och privata pooler.


* **Privata agenter**: privata agentnoder kör via ett icke-dirigerbara nätverk. Det här nätverket är endast tillgänglig från zonen admin eller via offentlig zon edge-routern. Som standard startar DC/OS-appar på privata agentnoder. 

* **Offentliga agenter**: offentliga agentnoder kör DC/OS-appar och tjänster via ett allmänt tillgängligt nätverk. 

Mer information om DC/OS-nätverkssäkerhet finns i den [DC/OS-dokumentationen](https://dcos.io/docs/1.8/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Distribuera agentpooler

DC/OS-agentpooler i Azure Container Service skapas på följande sätt:

* Den **privata poolen** innehåller antalet agentnoder som du anger när du [distribuera DC/OS-kluster](container-service-deployment.md). 

* Den **offentliga pool** inledningsvis innehåller ett förinställt antal agentnoder. Den här poolen läggs automatiskt när DC/OS-klustret har etablerats.

Poolen privata och offentliga poolen är Azure VM-skalningsuppsättningar. Du kan ändra storlek på dessa pooler efter distributionen.

## <a name="use-agent-pools"></a>Använda agentpooler
Som standard **Marathon** distribuerar ett nytt program till den *privata* agentnoder. Du måste uttryckligen distribuera programmet till den *offentliga* noder under genereringen av programmet. Välj den **valfritt** fliken och ange **slave_public** för den **accepterade resursroller** värde. Den här processen beskrivs [här](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) och i den [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentation.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hantera DC/OS-behållare](container-service-mesos-marathon-ui.md).

* Lär dig hur du [öppna brandväggen](container-service-enable-public-access.md) tillhandahålls av Azure för att tillåta offentlig åtkomst till ditt DC/OS-behållare.

