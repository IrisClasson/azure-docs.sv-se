---
title: Skapa en princip för att identifiera icke-kompatibla resurser med Azure CLI
description: Använd Azure CLI för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 99e8b782f3f52ed89b5188de19d70cb276a0eb84
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315850"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Skapa en principtilldelning för att identifiera icke-kompatibla resurser med Azure CLI

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här guiden används Azure CLI för att skapa en principtilldelning och för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

För den här snabbstarten måste du köra Azure CLI version 2.0.4 eller senare för att installera och använda CLI:t lokalt. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Nödvändiga komponenter

Registrera resursprovidern Policy Insights med hjälp av Azure CLI. När du registrerar resursprovidern säkerställer du att din prenumeration fungerar med den. Om du vill registrera en resursprovider måste du ha behörighet att utföra åtgärden att registrera resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

```azurecli-interactive
az provider register --namespace 'Microsoft.PolicyInsights'
```

Läs mer om att registrera och visa resursprovidrar i [(Resursprovidrar och typer)](../../azure-resource-manager/resource-manager-supported-services.md)

Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar**. Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Kör följande kommando för att skapa en ny principtilldelning:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Föregående kommando använder följande information:

- **Namn** – det faktiska namnet på tilldelningen.  I det här exemplet användes *audit-vm-manageddisks*.
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du *tilldelningen Granska virtuella datorer utan Managed Disks*.
- **Policy** – Principdefinitions-ID:t som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionens ID *Granska virtuella datorer som inte använder hanterade diskar*. För att hämta principdefinitionens ID kör du det här kommandot: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Om du vill visa de resurser som inte är kompatibla under den nya tilldelningen hämtar du principtilldelnings-ID:t genom att köra följande kommandon:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Mer information om principtilldelnings-ID finns i [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Kör sedan följande kommando för att hämta resurs-ID:n för de icke-kompatibla resurserna som matas ut till en JSON-fil:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Resultatet är jämförbart med det du vanligtvis skulle se under **Icke-kompatibla resurser** i Azure-portalens vy.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)