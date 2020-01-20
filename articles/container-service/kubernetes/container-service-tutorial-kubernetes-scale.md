---
title: (INAKTUELL) Självstudie för Azure Container Service – Skala ut ett program
description: Självstudie för Azure Container Service – Skala ut ett program
author: dlepow
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b0aa78a519567a8e1ffd76e26f1d9ea3ca701fca
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274180"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>(INAKTUELL) Skala ut Kubernetes-poddar och Kubernetes-infrastrukturen

> [!TIP]
> Den uppdaterade versionen av den här självstudien som använder Azure Kubernetes-tjänsten finns i [Självstudier: skala program i Azure Kubernetes service (AKS)](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Om du har följt självstudierna så har du ett fungerande Kubernetes-kluster i Azure Container Service och du har distribuerat appen Azure Voting. 

I den här självstudien, som är del fem av sju, skalar du ut poddarna i appen och provar autoskalning av poddar. Du får också lära dig hur du skalar ut antalet Azure VM-agentnoder så att du ändrar klustrets kapacitet som värd för arbetsbelastningar. Det här är några av uppgifterna:

> [!div class="checklist"]
> * Skala Kubernetes-poddar manuellt
> * Konfigurera autoskalning av poddarna som kör appens klientdel
> * Skala Kubernetes Azure-agentnoder

I efterföljande självstudier uppdaterar du Azure Vote-programmet, och konfigurerar Log Analytics för att övervaka Kubernetes-klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades. Programmet kördes därefter i Kubernetes-klustret. 

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa containeravbildningar](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Skala poddar manuellt

Hittills har vi distribuerat klientdelen av Azure Vote och Redisinstansen, var och en med en enda replik. Du kan kontrollera detta genom att köra programmet [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```azurecli-interactive
kubectl get pods
```

Resultat:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ändra antalet poddar i `azure-vote-front`-distributionen manuellt med kommandot [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale). I det här exemplet ökas antalet till 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Kör [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) om du vill verifiera att Kubernetes skapar poddarna. Efter ungefär en minut körs de nya poddarna:

```azurecli-interactive
kubectl get pods
```

Resultat:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatisk skalning av poddar

Kubernetes har stöd för [horisontell autoskalning av poddar](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) så att antalet poddar i en distribution justeras beroende på CPU-användningen eller något annat mått du väljer. 

Om du vill använda autoskalning måste poddarna ha definierade CPU-krav och CPU-gränser. I `azure-vote-front`-distributionen begär klientdelscontainern 0,25 CPU med maxgränsen 0,5 CPU. Inställningarna ser ut så här:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

I följande exempel används kommandot [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) till att automatiskt skala antalet poddar i `azure-vote-front`-distributionen. Om processoranvändningen överskrider 50 % ökar autoskalningen antalet poddar till högst 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Om du vill se status för autoskalningen kör du följande kommando:

```azurecli-interactive
kubectl get hpa
```

Resultat:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Efter några minuter med minimal belastning på Azure Vote-appen minskar antalet poddrepliker automatiskt till 3.

## <a name="scale-the-agents"></a>Skala agenterna

Om du har skapat Kubernetes-klustret med standardkommandona i föregående självstudier har det tre agentnoder. Du kan justera antalet agenter manuellt om du planerar att ha fler eller färre containerarbetsbelastningar i klustret. Använd kommandot [az acs scale](/cli/azure/acs#az-acs-scale) och ange antalet agenter med parametern `--new-agent-count`.

I följande exempel ökas antalet agentnoder till 4 i Kubernetes-klustret med namn *myK8sCluster*. Det tar några minuter att slutföra kommandot.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

Utdata från kommandot visar antalet agentnoder i värdet för `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använt olika skalningsfunktioner i Kubernetes-klustret. Här är några av uppgifterna:

> [!div class="checklist"]
> * Skala Kubernetes-poddar manuellt
> * Konfigurera autoskalning av poddarna som kör appens klientdel
> * Skala Kubernetes Azure-agentnoder

Gå vidare till nästa självstudie om du vill lära dig om att uppdatera program i Kubernetes.

> [!div class="nextstepaction"]
> [Uppdatera ett program i Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

