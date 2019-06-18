---
title: Azure Container Registry - roller och behörigheter
description: Använda Azure rollbaserad åtkomstkontroll (RBAC) och identitets- och åtkomsthantering (IAM) för att tillhandahålla detaljerade behörigheter till resurser i ett Azure container registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: d62dd6c65975d63a0127bb5dd1c62cd741b59ac6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068006"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller och behörigheter

Azure Container Registry-tjänsten stöder en uppsättning Azure-roller som kan ger olika nivåer av behörigheter till ett Azure container registry. Använd Azure [rollbaserad åtkomstkontroll](../role-based-access-control/index.yml) (RBAC) för att tilldela specifika behörigheter till användare eller tjänsthuvudnamn som måste interagera med ett register.

| Rollbehörighet /       | [Access Resource Manager](#access-resource-manager) | [Skapa/ta bort registret](#create-and-delete-registry) | [Push-överför avbildningen](#push-image) | [Hämta avbildning](#pull-image) | [Ta bort avbildningsdata](#delete-image-data) | [Ändra principer](#change-policies) |   [Logga avbildningar](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Ägare | X | X | X | X | X | X |  |  
| Deltagare | X | X | X |  X | X | X |  |  
| Läsare | X |  |  |  |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Skilja mellan användare och tjänster

Alla time-behörigheter tillämpas, bästa praxis är att tillhandahålla den mest begränsad uppsättningen behörigheter för en person eller tjänst, att utföra en uppgift. Följande behörighetsuppsättningar representerar en uppsättning funktioner som kan användas av människor och fjärradministrerad tjänster.

### <a name="cicd-solutions"></a>CI/CD-lösningar

Vid automatisering av `docker build` kommandon från CI/CD-lösningar, du behöver `docker push` funktioner. Dessa scenarier med fjärradministrering service föreslår vi att tilldela den **AcrPush** roll. Den här rollen, till skillnad från den bredare **deltagare** roll, förhindrar kontot från utför andra åtgärder i registret eller åtkomst till Azure Resource Manager.

### <a name="container-host-nodes"></a>Behållarnoder för värd

På samma sätt noder som kör dina behållare måste den **AcrPull** roll, men får inte kräva **läsare** funktioner.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker-tillägg

För verktyg som Visual Studio Code [Docker-tillägg](https://code.visualstudio.com/docs/azure/docker), ytterligare resource provider åtkomst krävs för att visa de tillgängliga Azure-behållarregister. I det här fallet ger användarna åtkomst till den **läsare** eller **deltagare** roll. Dessa roller Tillåt `docker pull`, `docker push`, `az acr list`, `az acr build`, och andra funktioner. 

## <a name="access-resource-manager"></a>Access Resource Manager

Azure Resource Manager-åtkomst krävs för den Azure-portalen och registret med den [Azure CLI](/cli/azure/). Till exempel för att hämta en lista över register med hjälp av den `az acr list` kommandot, du behöver den här behörigheten ange. 

## <a name="create-and-delete-registry"></a>Skapa och ta bort registret

Möjligheten att skapa och ta bort Azure-behållarregister.

## <a name="push-image"></a>Push-överför avbildningen

Möjligheten att `docker push` en avbildning, eller skicka en annan [stöds artefakt](container-registry-image-formats.md) , till exempel ett Helm-diagram till ett register. Kräver [autentisering](container-registry-authentication.md) med registret med behöriga identitet. 

## <a name="pull-image"></a>Hämta avbildning

Möjligheten att `docker pull` en icke-i karantän bild eller för att hämta en annan [stöds artefakt](container-registry-image-formats.md) , till exempel ett Helm-diagram från ett register. Kräver [autentisering](container-registry-authentication.md) med registret med behöriga identitet.

## <a name="delete-image-data"></a>Ta bort avbildningsdata

Möjligheten att [ta bort behållaravbildningar](container-registry-delete.md), eller ta bort andra [stöds artefakter](container-registry-image-formats.md) , till exempel Helm-diagram från ett register.

## <a name="change-policies"></a>Ändra principer

Möjligheten att konfigurera principer på ett register. Principer omfattar rensning av avbildningen, aktivera karantän och avbildning signering.

## <a name="sign-images"></a>Logga avbildningar

Möjligheten att logga avbildningar, oftast i en automatiserad process som använder ett huvudnamn för tjänsten. Den här behörigheten vanligtvis kombineras med [push bild](#push-image) att tillåta push-överföra en avbildning av en betrodd till ett register. Mer information finns i [innehåll förtroende i Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att tilldela RBAC-roller till en Azure-identitet med hjälp av den [Azure-portalen](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md), eller andra Azure-verktyg.

* Lär dig mer om [autentiseringsalternativ](container-registry-authentication.md) för Azure Container Registry.
