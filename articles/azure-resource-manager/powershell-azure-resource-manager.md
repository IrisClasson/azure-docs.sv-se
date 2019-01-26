---
title: Hantera Azure-lösningar med PowerShell | Microsoft Docs
description: Använda Azure PowerShell och Resource Manager för att hantera dina resurser.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 05dd112c3e2ac2ffde56dd7e355e1fe695968ed0
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078176"
---
# <a name="manage-resources-with-azure-powershell"></a>Hantera resurser med Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt läser du [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

I den här artikeln får tillämpa du alla inställningar till en resursgrupp så du kan enkelt ta bort dessa inställningar när du är klar.

Nu ska vi skapa resursgruppen.

```azurepowershell-interactive
Set-AzContext -Subscription <subscription-name>
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Resursgruppen är tom för närvarande.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Tilldela en roll

I den här artikeln får distribuera du en virtuell dator och dess relaterade virtuella nätverk. För hanteringen av VM-lösningar finns det tre resursspecifika roller som beviljar åtkomst på lämplig nivå:

* [Virtuell datordeltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor)
* [Lagringskontodeltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare är det ofta lättare att [skapa en Azure Active Directory-grupp](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) för användare som behöver utföra liknande åtgärder. Därefter tilldelar du gruppen lämplig roll. För att förenkla informationen i den här artikeln skapar vi en Azure Active Directory-grupp utan medlemmar. Du kan fortfarande tilldela den här gruppen en roll för ett omfång.

I följande exempel skapar en grupp och tilldelar den till rollen virtuell Datordeltagare för resursgruppen. Att köra den `New-AzureAdGroup` kommandot, måste du antingen genom att använda den [Azure Cloud Shell](/azure/cloud-shell/overview) eller [ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Normalt upprepar du processen för **Nätverksdeltagare** och **Lagringskontodeltagare** för att se till att hanteringen av alla distribuerade resurser tilldelas till användare. Du kan hoppa över dessa steg i den här artikeln.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../azure-policy/azure-policy-introduction.md) hjälper dig se till att alla resurser i prenumerationen uppfyller företagets standarder. Din prenumeration har redan flera principdefinitioner. Om du vill se tillgängliga principdefinitioner, använder du:

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

De befintliga principdefinitionerna visas. Principtypen är antingen **BuiltIn** eller **Custom**. Titta igenom definitionerna och leta efter sådana som beskriver ett tillstånd som du vill tilldela. I den här artikeln tilldelar du principer som:

* begränsa platserna för alla resurser
* begränsa SKU: er för virtuella datorer
* Granska virtuella datorer som inte använder hanterade diskar

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Distribuera den virtuella datorn

Nu när du har tilldelat roller och principer är det dags att distribuera lösningen. Standardstorleken är Standard_DS1_v2, som är en av dina tillåtna SKU:er. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

När distributionen är klar kan du lägga till fler hanteringsinställningar för lösningen.

## <a name="lock-resources"></a>Lås resurser

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Låsa en resurs

Om du vill låsa den virtuella datorn och en nätverkssäkerhetsgrupp, använder du:

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Den virtuella datorn kan bara tas bort om du uttryckligen tar bort låset. Det steget beskrivs i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Tagga resurser

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Tagga resurser

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Om du vill lägga till taggar till en virtuell dator, använder du:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Hitta resurser efter tagg

Om du vill söka efter resurser med en taggnamnet och Taggvärdet, använder du:

```azurepowershell-interactive
(Find-AzResource -TagName Environment -TagValue Test).Name
```

Du kan använda de returnerade värdena för olika hanteringsuppgifter, t.ex. för att stoppa alla virtuella datorer med ett visst taggvärde.

```azurepowershell-interactive
Find-AzResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader efter taggvärden

När du har applicerat taggar på resurser kan du visa kostnaderna för resurser med de taggarna. Det tar en stund innan kostnadsanalysen visar den senaste användningen, och därför ser du kanske inte kostnaderna ännu. När kostnaderna är tillgängliga kan du visa kostnaderna för resurser över olika resursgrupper i prenumerationen. Användarna måste ha [åtkomst på prenumerationsnivå till faktureringsinformation](../billing/billing-manage-access.md) för att se kostnaderna.

Om du vill visa kostnader efter taggar i portalen väljer du din prenumeration och sedan **Kostnadsanalys**.

![Kostnadsanalys](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Filtrera sedan efter taggvärdet och välj **Applicera**.

![Visa kostnad efter tagg](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Du kan även använda [API:er för Azure-fakturering](../billing/billing-usage-rate-card-overview.md) för att programmässigt visa kostnaderna.

## <a name="clean-up-resources"></a>Rensa resurser

Den låsta nätverkssäkerhetsgruppen kan inte tas bort förrän låset tagits bort. Ta bort låset med:

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

När du inte längre behövs kan du använda den [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) att ta bort resursgruppen, den virtuella datorn, och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

* Läs om hur du övervakar dina virtuella datorer i [övervaka och uppdatera en Windows-dator med Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Läs om hur du använder Azure Security Center för att implementera rekommenderade säkerhetsmetoder [övervaka säkerhet för virtuella datorer med hjälp av Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Du kan flytta befintliga resurser till en ny resursgrupp. Exempel finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
