---
title: Återställa autentiseringsuppgifterna för ett kluster i Azure Kubernetes Service (AKS)
description: Lär dig hur uppdateringen eller återställa tjänstens huvudnamn-autentiseringsuppgifter för ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 5aac941133296d2040d5dd670155b80f5807e1e9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614133"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Uppdatera eller rotera autentiseringsuppgifter för tjänstens huvudnamn i Azure Kubernetes Service (AKS)

Som standard skapas AKS-kluster med ett huvudnamn för tjänsten som har en förfallotid för ett år. Du kan återställa autentiseringsuppgifter för att utöka tjänstens huvudnamn under en ytterligare tid som du nära förfallodatum. Du kanske också vill uppdatera eller rotera autentiseringsuppgifter som en del av en definierad säkerhetsprincip. Den här artikeln beskriver hur du uppdaterar de här autentiseringsuppgifterna för ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.65 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Välja att uppdatera eller skapa ett huvudnamn för tjänsten

När du vill uppdatera autentiseringsuppgifterna för ett AKS-kluster kan du välja att:

* Uppdatera autentiseringsuppgifter för befintliga tjänstens huvudnamn som används av klustret, eller
* Skapa ett huvudnamn för tjänsten och uppdatera klustret så att de här nya autentiseringsuppgifter.

Om du vill skapa ett huvudnamn för tjänsten och sedan uppdatera AKS-klustret, hoppa över resten av stegen i det här avsnittet och fortsätta att [skapa ett huvudnamn för tjänsten](#create-a-service-principal). Om du vill uppdatera autentiseringsuppgifterna för den befintliga tjänsten huvudnamn som används av AKS-klustret, fortsätter du med stegen i det här avsnittet.

### <a name="get-the-service-principal-id"></a>Hämta ID för tjänstens huvudnamn

Om du vill uppdatera autentiseringsuppgifterna för den befintliga tjänsten huvudnamn, Hämta ID för tjänstens huvudnamn för ditt kluster med hjälp av den [az aks show][az-aks-show] kommando. I följande exempel hämtar ID för kluster med namnet *myAKSCluster* i den *myResourceGroup* resursgrupp. ID för tjänstens huvudnamn har angetts som en variabel med namnet *SP_ID* för användning i ytterligare kommando.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Uppdatera autentiseringsuppgifter för tjänstens huvudnamn

Med en variabel som innehåller ID för tjänstens huvudnamn, Återställ nu autentiseringsuppgifterna med [az ad sp credential återställa][az-ad-sp-credential-reset]. I följande exempel kan Azure-plattformen Generera en ny säker hemlighet för tjänstens huvudnamn. Den här nya säker hemligheten lagras också som en variabel.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Nu fortsätta till [uppdatering AKS-kluster med nya autentiseringsuppgifter](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Hoppa över det här steget om du har valt att uppdatera de befintliga autentiseringsuppgifterna för tjänstens huvudnamn i föregående avsnitt. Fortsätta att [uppdatering AKS-kluster med nya autentiseringsuppgifter](#update-aks-cluster-with-new-credentials).

Om du vill skapa ett huvudnamn för tjänsten och sedan uppdaterar AKS-kluster för att använda dessa nya autentiseringsuppgifter, Använd den [az ad sp create-for-rbac][az-ad-sp-create] kommando. I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De utdata som genereras påminner om de i följande exempel. Anteckna dina egna `appId` och `password`. De här värdena används i nästa steg.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definiera variabler för de service principal-ID och klienthemlighet med utdata från din egen nu [az ad sp create-for-rbac][az-ad-sp-create] kommandot, som visas i följande exempel. Den *SP_ID* är din *appId*, och *SP_SECRET* är din *lösenord*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Uppdatera AKS-kluster med nya autentiseringsuppgifter

Oavsett om du väljer att uppdatera autentiseringsuppgifterna för den befintliga tjänsten huvudnamn eller skapa ett huvudnamn för tjänsten, nu du uppdatera AKS-kluster med dina nya autentiseringsuppgifter med hjälp av den [aaz aks update-credentials][az-aks-update-credentials] kommando. Variablerna för den *--huvudnamn för tjänsten* och *--klienthemlighet* används:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Det tar en liten stund för autentiseringsuppgifter för tjänstens huvudnamn som ska uppdateras i AKS.

## <a name="next-steps"></a>Nästa steg

Tjänstens huvudnamn för själva AKS-klustret har uppdaterats i den här artikeln. Mer information om hur du hanterar identitet för arbetsbelastningar inom ett kluster finns i [bästa praxis för autentisering och auktorisering i AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
