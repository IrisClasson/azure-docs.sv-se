---
title: Exempel – Granska virtuell dator som inte använder hanterad disk
description: Den här json-exempeldefinitionen granskar när en virtuell dator skapas som inte använder hanterade diskar.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 20552ef0a56a0fc7136efa80561812ee4b5dad9b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980549"
---
# <a name="sample---audit-when-vm-does-not-use-managed-disk"></a>Exempel – Granska när VM inte använder hanterade diskar

Granskar när en virtuell dator skapas som inte använder hanterade diskar.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../../policy-templates/samples/compute/audit-non-managed-disk-vm/azurepolicy.json "Create VM using Managed Disk")]

Du kan distribuera den här mallen med hjälp av [Azure Portal](#deploy-with-the-portal) eller med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

[![Deploy till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Faudit-non-managed-disk-vm%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-non-managed-disk-vm" -DisplayName "Create VM using Managed Disk" -description "Create VM using Managed Disk" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-non-managed-disk-vm/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-non-managed-disk-vm/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Rensa PowerShell-distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-non-managed-disk-vm' --display-name 'Create VM using Managed Disk' --description 'Create VM using Managed Disk' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-non-managed-disk-vm/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-non-managed-disk-vm/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-non-managed-disk-vm"
```

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distributionen

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Granska fler exempel på [Azure-principexempel](index.md)