---
title: Exempel – Framtvinga matchningsmönster
description: Den här exempel princip definitionen kräver att resurser uppfyller mönstret som definieras i en parameter för namngivnings konventioner.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: df72db59e0370e82ca9f4aff199498ffdf06d834
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463604"
---
# <a name="sample---enforce-like-pattern-for-naming-conventions"></a>Exempel – Framtvinga like-mönster för namngivningskonventioner

Kräv resursnamn uppfyller ett like-mönster för namngivningskonventioner. Ange det tillåtna like-mönstret som en parameter.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exempelmall

[!code-json[main](../../../../policy-templates/samples/TextPatterns/enforce-like-pattern/azurepolicy.json "enforce like pattern")]

Du kan distribuera den här mallen med hjälp av [Azure Portal](#deploy-with-the-portal) eller med [PowerShell](#deploy-with-powershell) eller [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Distribuera med portalen

[![distribuera princip exemplet till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FTextPatterns%2Fenforce-like-pattern%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "enforce-like-pattern" -DisplayName "Ensure resource names meet the like condition for a pattern." -description "Ensure resource names meet the like condition for a pattern." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-like-pattern/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-like-pattern/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
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
az policy definition create --name 'enforce-like-pattern' --display-name 'Ensure resource names meet the like condition for a pattern.' --description 'Ensure resource names meet the like condition for a pattern.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-like-pattern/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/TextPatterns/enforce-like-pattern/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-like-pattern" 
```

### <a name="clean-up-azure-cli-deployment"></a>Rensa Azure CLI-distributionen

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Granska fler exempel under [Azure Policy-exempel](index.md)