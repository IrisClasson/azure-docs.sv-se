---
title: Kontrollera ditt Kubernetes-distributioner på Azure för implementering av bästa praxis
description: Lär dig att söka efter implementering av bästa praxis i dina distributioner på Azure Kubernetes Service med hjälp av kube-advisor
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 03c5eb2e32a0a8ec51844511276d9efba5651068
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "65073766"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Söker efter Kubernetes bästa praxis i ditt kluster

Det finns flera beprövade metoder som du bör följa på Kubernetes-distributioner för att säkerställa bästa prestanda och flexibilitet för dina program. Du kan använda kube-Analysverktyget för att söka efter distributioner som inte följer dessa förslag.

## <a name="about-kube-advisor"></a>Om kube-advisor

Den [kube-Analysverktyget för] [ kube-advisor-github] är en enskild behållare som är avsedd att köras i klustret. Den frågar Kubernetes API-server för information om dina distributioner och returnerar en mängd med förslag på förbättringar.

Verktyget kube-advisor kan rapportera om resursbegäran och begränsningar som saknas i PodSpecs för Windows-program samt Linux-program, men själva kube-advisor-verktyget måste planeras på en Linux-pod. Du kan schemalägga en pod ska köras på en nodpool med ett specifikt operativsystem med en [noden väljare] [ k8s-node-selector] i en pod-konfiguration.

> [!NOTE]
> Kube-Analysverktyget för stöds av Microsoft på basis av bästa prestanda. Problem och förslag bör lämnas in på GitHub.

## <a name="running-kube-advisor"></a>Kör kube-advisor

Att köra verktyget i ett kluster som har konfigurerats för [rollbaserad åtkomstkontroll (RBAC)](azure-ad-integration.md), med hjälp av följande kommandon. Det första kommandot skapar ett Kubernetes-tjänstkonto. Det andra kommandot Kör verktyget i en pod med detta tjänstkonto och konfigurerar pod för borttagning när avslutas. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Om du inte använder RBAC kan köra du kommandot på följande sätt:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Inom några sekunder bör du se en tabell som beskriver möjliga förbättringar av dina distributioner.

![Kube-advisor-utdata](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Kontroller som utförs

Verktyget verifierar flera Kubernetes bästa praxis, var och en med sin egen förslagna lösningar.

### <a name="resource-requests-and-limits"></a>Resursbegäranden och begränsningar

Kubernetes har stöd för definiera [resource begär och begränsar på pod-specifikationer][kube-cpumem]. Begäran definierar den minsta processor och minne som krävs för att köra behållaren. Gränsen definierar högsta CPU och minne som ska tillåtas.

Inga begäranden eller gränser är som standard på pod-specifikationer. Detta kan leda till noder som overscheduled och behållare som har för få. Kube-Analysverktyget för visar poddar utan begäranden och set-begränsningarna.

## <a name="cleaning-up"></a>Rensa

Om klustret har RBAC aktiverad, du kan rensa den `ClusterRoleBinding` när du har kört verktyget med följande kommando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Om du kör verktyget mot ett kluster som inte är RBAC-aktiverad, krävs ingen rensning.

## <a name="next-steps"></a>Nästa steg

- [Felsöka problem med Azure Kubernetes Service](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors