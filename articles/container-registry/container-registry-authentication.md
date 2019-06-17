---
title: Autentisera med ett Azure container registry
description: Alternativ för autentisering för ett Azure container registry, inklusive logga in med en Azure Active Directory-identitet, med hjälp av tjänstens huvudnamn och med valfritt administratörsautentiseringsuppgifter.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9682b9b832a8fd7374cb84e8cc6faad69df15945
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333876"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autentisera med ett privat Docker-behållarregister

Det finns flera sätt att autentisera med ett Azure container registry, som gäller för en eller flera Användningsscenarier för registret.

Du kan logga in till ett register direkt via [enskilda inloggningen](#individual-login-with-azure-ad), eller program och behållarinitierare kan utföra obevakade eller ”fjärradministrering” autentisering med hjälp av en Azure Active Directory (Azure AD) [ tjänstens huvudnamn](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Enskilda inloggning med Azure AD

När du arbetar med registret direkt, till exempel hämtar avbildningar till och skickar bilder från en arbetsstation för utveckling, autentisera genom att använda den [docker login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) i den [Azure CLI](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

När du loggar in med `az acr login`, CLI använder token skapas när du körde [az-inloggning](/cli/azure/reference-index#az-login) att sömlöst autentisera din session med ditt register. När du har loggat in på så sätt kan dina autentiseringsuppgifter är cachelagrat och efterföljande `docker` kommandon i sessionen kräver inte något användarnamn eller lösenord. 

För åtkomst till behållarregistret, token som används av `az acr login` är giltig för 1 timme, så vi rekommenderar att du alltid logga in i registret innan du kör en `docker` kommando. Om din token upphör att gälla, kan du uppdatera den med hjälp av den `az acr login` kommandot igen för att autentiseras på nytt. 

Med hjälp av `az acr login` med Azure-identiteter tillhandahåller [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md). För vissa scenarier kan du logga in till ett register med din egen individuell identitet i Azure AD. För scenarier för cross-tjänst eller för att hantera behov i en arbetsgrupp där du inte vill hantera enskilda åtkomst, du kan också logga in med en [hanterad identitet för Azure-resurser](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Tjänstens huvudnamn

Om du tilldelar en [tjänstens huvudnamn](../active-directory/develop/app-objects-and-service-principals.md) till registret, programmet eller tjänsten kan använda den för fjärradministrerad autentisering. Tillåt att tjänstens huvudnamn [rollbaserad åtkomst](../role-based-access-control/role-assignments-portal.md) till ett register och du kan tilldela flera tjänstens huvudnamn till ett register. Flera tjänstens huvudnamn kan du definiera olika åtkomst till olika program.

Tillgängliga roller för ett behållarregister är:

* **AcrPull**: pull

* **AcrPush**: hämta och skicka

* **Ägare**: hämta, skicka och tilldela roller till andra användare

En fullständig lista över roller finns i [Azure Container Registry roller och behörigheter](container-registry-roles.md).

CLI-skript att skapa ett huvudnamn app-ID och lösenord för att autentisera med ett Azure container registry, eller att använda ett befintligt huvudnamn för tjänsten, se [Azure Container Registry-autentisering med tjänstens huvudnamn](container-registry-auth-service-principal.md).

Tjänstens huvudnamn aktivera fjärradministrerad anslutning till ett register i både pull och push-scenarier som följande:

  * *Pull*: Distribuera behållare från ett register till orchestration-system, inklusive Kubernetes, DC/OS och Docker Swarm. Du kan också hämta från behållarregister till relaterade Azure-tjänster som [Azure Kubernetes Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [Apptjänst](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), med mera.

  * *Push-* : Skapa behållaravbildningar och skicka dem till ett register med kontinuerlig integrering och distribution av lösningar som Azure Pipelines eller Jenkins.

Du kan också logga in direkt med ett huvudnamn för tjänsten. När du kör följande kommando interaktivt ange tjänstens huvudnamn appID (användarnamn) och lösenord när du uppmanas till detta. Metodtips att hantera autentiseringsuppgifter för inloggning, finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot referens:

```
docker login myregistry.azurecr.io
```

När du har loggat cachelagrar Docker autentiseringsuppgifter, så du inte behöver att komma ihåg app-ID.

> [!TIP]
> Du kan återskapa lösenordet för ett huvudnamn för tjänsten genom att köra den [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest) kommando.
>

## <a name="admin-account"></a>Administratörskonto

Varje behållarregister innehåller ett administratörskonto för användare som är inaktiverad som standard. Du kan aktivera administratörsanvändaren och hantera dess autentiseringsuppgifter i Azure portal eller med hjälp av Azure CLI eller andra Azure-verktyg.

> [!IMPORTANT]
> Administratörskontot som har utformats för en enskild användare åtkomst till registret, främst i testsyfte. Vi rekommenderar inte att dela autentiseringsuppgifterna som administratör-konto med flera användare. Alla användare som autentiseras med administratörskontot som visas som en enskild användare med push och pull åtkomst till registret. Ändra eller inaktivera det här kontot inaktiverar registeråtkomst för alla användare som använder autentiseringsuppgifterna. Individuell identitet rekommenderas för användare och tjänsthuvudnamn för fjärradministrerad scenarier.
>

Administratörskontot som har angetts med två lösenord som kan återskapas. Två lösenord kan du upprätthålla anslutning till registret genom att använda ett lösenord medan du återskapar den andra. Om administratörskontot som är aktiverad, du kan skicka användarnamn och antingen lösenord för att den `docker login` kommandot när du tillfrågas om grundläggande autentisering till registret. Exempel:

```
docker login myregistry.azurecr.io 
```


Om du vill aktivera administratörsanvändare för ett befintligt register, du kan använda den `--admin-enabled` -parametern för den [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) i Azure-CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Du kan aktivera administratörsanvändare på Azure portal genom att navigera ditt register att välja **åtkomstnycklar** under **inställningar**, sedan **aktivera** under **Admin användaren**.

![Aktivera administratörsanvändare Användargränssnittet på Azure portal][auth-portal-01]

## <a name="next-steps"></a>Nästa steg

* [Skicka din första avbildning med hjälp av Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
