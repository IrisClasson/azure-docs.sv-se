---
title: Använda hanterade identiteter i Azure Kubernetes-tjänsten
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 8c5c4a6e5d8b2997d80c7263ba17a705d3846ed8
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987400"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes-tjänsten

För närvarande kräver ett Azure Kubernetes service (AKS)-kluster (specifikt Kubernetes Cloud Provider) en identitet för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure. Den här identiteten kan vara antingen en *hanterad identitet* eller ett *huvud namn för tjänsten*. Om du använder ett [huvud namn för tjänsten](kubernetes-service-principal.md)måste du antingen ange ett eller AKS skapar ett för din räkning. Om du använder hanterad identitet skapas detta automatiskt åt dig av AKS. Kluster som använder tjänstens huvud namn når till sist ett tillstånd där tjänstens huvud namn måste förnyas för att hålla klustret igång. Hantering av tjänstens huvud namn ökar komplexiteten, vilket innebär att det är lättare att använda hanterade identiteter i stället. Samma behörighets krav gäller för både tjänstens huvud namn och hanterade identiteter.

*Hanterade identiteter* är i grunden en omslutning av tjänstens huvud namn och gör hanteringen enklare. Rotation av autentiseringsuppgifter för MI sker automatiskt var 46: e dag enligt Azure Active Directory standard. AKS använder både systemtilldelade och användarspecifika hanterade identitets typer. Dessa identiteter är för närvarande oföränderliga. Läs mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

- Azure CLI, version 2.8.0 eller senare

## <a name="limitations"></a>Begränsningar

* AKS-kluster med hanterade identiteter kan bara aktive ras när klustret skapas.
* Befintliga AKS-kluster kan inte migreras till hanterade identiteter.
* Under kluster **uppgraderings** åtgärder är den hanterade identiteten tillfälligt otillgänglig.
* Klienterna flyttar/migrerar för hanterade identitets aktiverade kluster stöds inte.

## <a name="summary-of-managed-identities"></a>Sammanfattning av hanterade identiteter

AKS använder flera hanterade identiteter för inbyggda tjänster och tillägg.

| Identitet                       | Namn    | Användningsfall | Standard behörigheter | Ta med din egen identitet
|----------------------------|-----------|----------|
| Kontrollplan | inte synlig | Används av AKS för hanterade nätverks resurser inklusive ingångs utjämning och AKS offentliga IP-adresser | Deltagar roll för nod resurs grupp | Förhandsgranskning
| Kubelet | AKS-kluster namn – agentpoolegenskap | Autentisering med Azure Container Registry (ACR) | Läsar roll för nod resurs grupp | Stöds för närvarande inte
| Tillägg | AzureNPM | Ingen identitet krävs | NA | Inga
| Tillägg | AzureCNI nätverks övervakning | Ingen identitet krävs | NA | Inga
| Tillägg | azurepolicy (Gatekeeper) | Ingen identitet krävs | NA | Inga
| Tillägg | azurepolicy | Ingen identitet krävs | NA | Inga
| Tillägg | Calico | Ingen identitet krävs | NA | Inga
| Tillägg | Instrumentpanel | Ingen identitet krävs | NA | Inga
| Tillägg | HTTPApplicationRouting | Hanterar nödvändiga nätverks resurser | Läsar roll för nod resurs grupp, deltagar roll för DNS-zon | Inga
| Tillägg | Ingress Application Gateway | Hanterar nödvändiga nätverks resurser| Deltagar roll för nod resurs grupp | Inga
| Tillägg | omsagent | Används för att skicka AKS-mått till Azure Monitor | Övervaknings mått utgivar rollen | Inga
| Tillägg | Virtuell-nod (ACIConnector) | Hanterar nödvändiga nätverks resurser för Azure Container Instances (ACI) | Deltagar roll för nod resurs grupp | Inga


## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Nu kan du skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommandon.

Börja med att skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Ett lyckat kluster skapas med hanterade identiteter som innehåller den här tjänstens huvud princips profil information:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Använd följande kommando för att fråga ObjectID för din hanterade identitet för kontroll planet:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Resultatet bör se ut så här:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

När klustret har skapats kan du distribuera dina program arbets belastningar till det nya klustret och interagera med det precis som du har gjort med service-huvudbaserade AKS-kluster.

> [!NOTE]
> För att skapa och använda ditt eget VNet, en statisk IP-adress eller en ansluten Azure-disk där resurserna ligger utanför resurs gruppen för arbetsnoder, använder du PrincipalID för den tilldelade hanterade identiteten i kluster systemet för att utföra en roll tilldelning. Mer information om roll tilldelning finns i [Delegera åtkomst till andra Azure-resurser](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Behörighets bidrag till kluster hanterad identitet som används av Azure Cloud Provider kan ta upp till 60 minuter att fylla i.

Slutligen kan du hämta autentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Ta med ditt eget kontroll plan MI (för hands version)
En anpassad kontroll plan identitet ger åtkomst till den befintliga identiteten innan klustret skapas. Detta möjliggör scenarier som att använda en anpassad VNET eller outboundType av UDR med en hanterad identitet.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Du måste ha följande resurser installerade:
- Azure CLI, version 2.9.0 eller senare
- Tillägget AKS-Preview 0.4.57

Begränsningar för att ta med ditt eget kontroll plan MI (för hands version):
* Azure Government stöds inte för närvarande.
* Azure Kina 21Vianet stöds inte för närvarande.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar du registreringen av `Microsoft.ContainerService` resurs leverantören med hjälp av [AZ Provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Om du inte har en hanterad identitet ännu bör du gå vidare och skapa en till exempel genom att använda [AZ Identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Resultatet bör se ut så här:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Om din hanterade identitet är en del av din prenumeration kan du använda [AZ Identity CLI-kommandot][az-identity-list] för att fråga den.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Nu kan du använda följande kommando för att skapa ditt kluster med din befintliga identitet:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Ett lyckat kluster skapas med dina egna hanterade identiteter som innehåller den här userAssignedIdentities profil informationen:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Nästa steg
* Använd [Azure Resource Manager arm-mallar][aks-arm-template] för att skapa hanterade identitets aktiverade kluster.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list
