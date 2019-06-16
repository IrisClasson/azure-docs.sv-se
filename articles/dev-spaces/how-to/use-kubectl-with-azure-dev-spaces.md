---
title: Använda kubectl med Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60686332"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett adressutrymme för Azure-utveckling

Du kan komma åt Kubernetes-kluster i ett adressutrymme för utveckling av Azure och Använd befintliga Kubernetes-verktyg som `kubectl`.

Kör `az aks use-dev-spaces` kommandot att automatiskt lägga till en `kubectl` configuration kontext för dig, så kubectl redan är ansluten till din Azure Dev blanksteg Kubernetes-kluster. Exempel:
- Kontrollera den aktuella kontexten: `kubectl config current-context`
- Lista över alla tillgängliga sammanhang: `kubectl config get-contexts`. 
- Kontext: `kubectl config use-context <context-name>`
- Visa instrumentpanelen för Kubernetes: kör `kubectl proxy`, sedan öppna din webbläsare med den adress som det här kommandot genererar (Lägg till `/ui` i URL: en för att navigera till Kubernetes-instrumentpanelen).
- Lista löpande services i området standard Azure Dev blanksteg med namnet *standard*: `kubectl get services --namespace=default`

