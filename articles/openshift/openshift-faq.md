---
title: Vanliga frågor om Azure Red Hat OpenShift | Microsoft Docs
description: Här är några svar på vanliga frågor om Microsoft Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 2001b849e9c43d552889475ca237c52b141f3f04
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306269"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift vanliga frågor och svar

Den här artikeln tar upp vanliga frågor och svar (FAQ) om Microsoft Azure Red Hat OpenShift.

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Innan du kan använda Azure Red Hat OpenShift, behöver du köpa minst 4 noder för reserverade Azure Red Hat OpenShift-programmet.

Om du är Azure-kund[köpa Azure Red Hat OpenShift reserverade instanser](https://aka.ms/openshift/buy) via Azure portal. När du har köpt, aktiveras din prenumeration inom 24 timmar, varefter kommer du att kunna etablera kluster.

Om du inte är Azure-kund [försäljningskontakt](https://aka.ms/openshift/contact-sales) och Fyll i formuläret försäljning längst ned på sidan för att starta processen.

Referera till den [Azure Red Hat OpenShift sidan med priser](https://aka.ms/openshift/pricing) för mer information.

## <a name="which-azure-regions-are-supported"></a>Vilka Azure-regioner stöds?

Se [de resurser som stöds](supported-resources.md#azure-regions) en lista över globala regioner där Azure Red Hat OpenShift stöds.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kan jag distribuera ett kluster till ett befintligt virtuellt nätverk?

Nej. Men du kan ansluta en Azure Red Hat OpenShift-kluster till ett befintligt VNET via peering. Se [ansluta virtuella nätverk i ett kluster till ett befintligt virtuellt nätverk ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) mer information.

## <a name="what-cluster-operations-are-available"></a>Vilka klusteråtgärder är tillgängliga?

Du kan bara skala upp eller ned antalet beräkningsnoder. Inga andra ändringar tillåts den `Microsoft.ContainerService/openShiftManagedClusters` resurs när du har skapat. Det maximala antalet beräkningsnoder är begränsad till 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Vilka storlekar för virtuella datorer kan jag använda?

Se [storlekar för virtuella datorer i Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) för en lista med storlekar för virtuella datorer som du kan använda med ett Azure Red Hat OpenShift-kluster.

## <a name="is-data-on-my-cluster-encrypted"></a>Är data i mitt kluster krypteras?

Som standard finns kryptering i vila. Plattformen Azure Storage krypterar automatiskt data före beständig lagring och dekrypterar data innan hämtning. Se [Azure Storage Service Encryption för vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) mer information.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Kan jag använda Prometheus/Grafana för att övervaka behållare och hantera kapacitet?

Nej, inte på den aktuella tiden.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Är Docker-registret externt så att jag kan använda verktyg som Jenkins?

Docker-registret är tillgänglig från `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` dock garanterad tillförlitlighet stark lagring har inte angetts. Du kan också använda [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Det finns stöd för cross-namespace-nätverk

Kund- och enskilt projekt-administratörer kan anpassa cross-namespace-nätverk (inklusive nekas) på en per projekt med hjälp av `NetworkPolicy` objekt.

## <a name="can-an-admin-manage-users-and-quotas"></a>En administratör kan hantera användare och kvoter?

Ja. En Azure Red Hat OpenShift-administratör kan hantera användare och kvoter förutom att komma åt alla användare som skapade projekt.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kan jag begränsa ett kluster för att endast vissa Azure AD-användare?

Ja. Du kan begränsa vilka Azure AD-användare kan logga in till ett kluster genom att konfigurera Azure AD-programmet. Mer information finns i [så här: Begränsa din app till en uppsättning användare](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kan ett kluster har compute-noder i flera Azure-regioner?

Nej. Alla noder i ett kluster i Azure Red Hat OpenShift måste komma från samma Azure-region.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Huvud- och infrastruktur noder abstraheras bort eftersom de är med Azure Kubernetes Service (AKS)?

Nej. Alla resurser, inklusive kluster-huvudservern kör i en kundprenumeration. Dessa typer av resurser placeras i en skrivskyddad resursgrupp.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Är Open Service Broker for Azure (OSBA) stöds?

Ja. Du kan använda OSBA med Azure Red Hat OpenShift. Se [Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) för mer information.
