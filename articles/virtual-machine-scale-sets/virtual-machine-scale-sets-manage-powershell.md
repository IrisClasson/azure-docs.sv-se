---
title: Hantera VM-Skalningsuppsättningar med Azure PowerShell | Microsoft Docs
description: Vanliga Azure PowerShell-cmdletar för att hantera Virtual Machine Scale Sets, till exempel att starta och stoppa en instans eller ändra skala Ange kapacitet.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: a6474320fd8b1545d61320cd43e155ab077ba310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683534"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Hantera en VM-skalningsuppsättning med Azure PowerShell

Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Den här artikeln beskriver några vanliga Azure PowerShell-cmdlets som låter dig utföra dessa uppgifter.

Om du vill skapa en skalningsuppsättning för virtuell dator kan du [skapa en skalningsuppsättning med Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Visa information om en skalningsuppsättning
Du kan visa den övergripande informationen om en skalningsuppsättning [Get-AzVmss](/powershell/module/az.compute/get-azvmss). I följande exempel hämtar information om skalningsuppsättningen *myScaleSet* i den *myResourceGroup* resursgrupp. Ange egna namn enligt följande:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Du kan visa en lista över VM-instans i en skalningsuppsättning [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). I följande exempel visar en lista över alla Virtuella datorinstanser i skalningsuppsättningen *myScaleSet* och i den *myResourceGroup* resursgrupp. Ange egna värden för dessa namn:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Om du vill visa mer information om en specifik VM-instans, lägger du till den `-InstanceId` parameter [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) och anger en instans för att visa. I följande exempel visar information om den Virtuella datorinstansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna namn enligt följande:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
I föregående kommandon som visar information om din skalningsuppsättning och VM-instanser. Om du vill öka eller minska antalet instanser i skalningsuppsättningen, kan du ändra kapaciteten. Skalningsuppsättning automatiskt skapar eller tar bort det begärda antalet virtuella datorer och sedan konfigurerar de virtuella datorerna för att ta emot trafik.

Skapa först ett skalningsuppsättningsobjekt med [Get-AzVmss](/powershell/module/az.compute/get-azvmss) och ange sedan ett nytt värde för `sku.capacity`. Om du vill tillämpa kapacitetsändringen, använder du [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Följande exempel uppdateringar *myScaleSet* i den *myResourceGroup* resursgrupp till en kapacitet på *5* instanser. Ange egna värden enligt följande:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skala ange de virtuella datorerna med den högsta instans-ID tas bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning, använder du [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Stoppa flera virtuella datorer genom att avgränsa varje instans-ID med kommatecken.

I följande exempel stoppar instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Som standard frigörs stoppade virtuella datorer och uppbär inga beräkningskostnader. Om du vill att den virtuella datorn är kvar i etablerat tillstånd när den stoppats, lägger du till parametern `-StayProvisioned` till det föregående kommandot. Stoppade virtuella datorer som fortsätter att vara etablerade, kostar vanliga beräkningsavgifter.


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalningsuppsättning
Om du vill starta en eller flera virtuella datorer i en skalningsuppsättning, använder du [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Starta flera virtuella datorer genom att avgränsa varje instans-ID med kommatecken.

Följande exempel startar instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalningsuppsättning, använder [omstart AzVmss](/powershell/module/az.compute/restart-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Avgränsa varje instans-ID för att starta om flera virtuella datorer med ett kommatecken.

I följande exempel startar om instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skalningsuppsättning
Ta bort en eller flera virtuella datorer i en skalningsuppsättning med [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). Den `-InstanceId` parametern kan du ange en eller flera virtuella datorer att ta bort. Om du inte anger ett instans-ID kommer alla virtuella datorer i skalningsuppsättningen tas bort. Avgränsa varje instans-ID för att ta bort flera virtuella datorer med ett kommatecken.

I följande exempel tar bort instansen *0* i skalningsuppsättningen *myScaleSet* och *myResourceGroup* resursgrupp. Ange egna värden enligt följande:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga aktiviteter för scale sets omfattar så [distribuera ett program](virtual-machine-scale-sets-deploy-app.md), och [uppgradera VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure PowerShell för att [konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
