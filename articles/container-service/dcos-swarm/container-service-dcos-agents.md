---
title: (INAKTUELL) DC/OS-agentpooler för Azure Container Service
description: Hur offentliga och privata agentpooler fungerar med Azure Container Service DC/OS-kluster
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60478454"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(INAKTUELL) DC/OS-agentpooler för Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS-kluster i Azure Container Service innehåller agentnoder i två pooler, poolen offentliga och privata poolen. Ett program kan bara distribueras till antingen poolen, påverkar tillgängligheten mellan datorer i container service. Datorerna kan exponerade mot internet (offentlig) eller behöll intern (privat). Den här artikeln ger en kort översikt över varför det är offentliga och privata pooler.


* **Privata agenter**: Privata agentnoder kör via ett icke-dirigerbara nätverk. Det här nätverket är endast tillgänglig från zonen admin eller via offentlig zon edge-routern. Som standard startar DC/OS-appar på privata agentnoder. 

* **Offentliga agenter**: Offentliga agentnoder kör DC/OS-appar och tjänster via ett allmänt tillgängligt nätverk. 

Mer information om DC/OS-nätverkssäkerhet finns i den [DC/OS-dokumentationen](https://docs.mesosphere.com/).

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

