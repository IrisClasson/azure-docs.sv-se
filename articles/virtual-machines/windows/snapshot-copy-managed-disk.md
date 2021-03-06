---
title: Skapa en ögonblicks bild av en virtuell hård disk i Azure
description: Lär dig hur du skapar en kopia av en virtuell Azure-dator som ska användas som säkerhets kopiering eller vid fel sökning av problem.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ecb99c7f64d81d57c5d6d2cb25967913a752b4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074128"
---
# <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk (VHD). Du kan ta en ögonblicks bild av en OS-eller datadisk-VHD som ska användas som säkerhets kopia, eller för att felsöka problem med virtuella datorer (VM).

Om du vill använda ögonblicks bilden för att skapa en ny virtuell dator, rekommenderar vi att du stänger av den virtuella datorn i klartext innan du tar en ögonblicks bild, för att ta bort alla processer som pågår.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 

Utför följande steg för att skapa en ögonblicks bild: 
1.  På [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**.
2. Sök efter och välj **ögonblicks bild**.
3. I fönstret **ögonblicks bild** väljer du **skapa**. Fönstret **skapa ögonblicks bild** visas.
4. Ange ett **namn** för ögonblicks bilden.
5. Välj en befintlig [resurs grupp](../../azure-resource-manager/management/overview.md#resource-groups) eller ange namnet på en ny resurs grupp. 
6. Välj en **Plats** för ett Azure-datacenter.  
7. För **käll disk**väljer du den hanterade disk som ska avbildas.
8. Välj den **Kontotyp** som ska användas för att lagra ögonblicks bilden. Välj **Standard_HDD**, om du inte behöver att ögonblicks bilden lagras på en hög presterande disk.
9. Välj **Skapa**.

## <a name="use-powershell"></a>Använd PowerShell

Följande steg visar hur du kopierar VHD-disken och skapar ögonblicks bilds konfigurationen. Du kan sedan ta en ögonblicks bild av disken med hjälp av cmdleten [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

 

1. Ange några parametrar: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Hämta den virtuella datorn:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Skapa ögonblicks bilds konfigurationen. I det här exemplet är ögonblicks bilden av OS-disken:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Om du vill lagra din ögonblicks bild i en zon fri lagring skapar du den i en region som stöder [tillgänglighets zoner](../../availability-zones/az-overview.md) och inkluderar `-SkuName Standard_ZRS` parametern.   
   
4. Ta ögonblicks bilden:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en ögonblicks bild genom att skapa en hanterad disk från en ögonblicks bild och sedan ansluta den nya hanterade disken som operativ system disk. Mer information finns i exemplet i [skapa en virtuell dator från en ögonblicks bild med PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
