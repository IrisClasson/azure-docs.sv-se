---
title: Azure PowerShell-exempel – Zonredundant skalningsuppsättning | Microsoft Docs
description: Azure PowerShell-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6a5ebc823be38036082a9e0052e01ff46416c8d3
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888302"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Skapa en zonredundant VM-skalningsuppsättning med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016 över flera tillgänglighetszoner. När du kört skriptet har du åtkomst till den virtuella datorn över RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Skapar VM-skalningsuppsättningen och alla stödresurser, inklusive virtuellt nätverk, lastbalansering och NAT-regler. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Hämtar information om en VM-skalningsuppsättning. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Lägger till ett virtuellt datortillägg för att det anpassade skriptet ska installera ett grundläggande webbprogram. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Uppdaterar modellen för VM-skalningsuppsättningen för att använda det virtuella datortillägget. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Hämtar information om den offentliga IP-adress som används av lastbalanseraren. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |


## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för skalningsuppsättningar för virtuella Azure-datorer](../powershell-samples.md).