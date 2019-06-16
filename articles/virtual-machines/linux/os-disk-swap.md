---
title: Växla OS-disk för en Azure-dator med CLI | Microsoft Docs
description: Ändra operativsystemets disk som används av virtuella Azure-datorer med hjälp av CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: b17647a09c88491e2486046b1ca99ee277f0cc28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473900"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Ändra OS-disken som används av en Azure-dator med hjälp av CLI


Om du har en befintlig virtuell dator, men du vill växla disken för en säkerhetskopiering disk eller en annan OS-disk, kan du använda Azure CLI för att växla OS-diskar. Du behöver ta bort och återskapa den virtuella datorn. Du kan även använda en hanterad disk i en annan resursgrupp, förutsatt att den inte redan används.

Den virtuella datorn måste vara stopped\deallocated och resurs-ID för hanterad disk kan ersättas med resurs-ID för en annan hanterad disk. 

Kontrollera att typ av virtuell dator och är kompatibla med den disk du vill bifoga. Till exempel om det är den disk du vill använda i Premium-lagring, måste den virtuella datorn kunna Premium Storage (t.ex. en storlek i DS-serien).

Den här artikeln kräver Azure CLI version 2.0.25 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 


Använd [az disk list](/cli/azure/disk) att hämta en lista över diskarna i resursgruppen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Använd [az vm stop](/cli/azure/vm) till stop\deallocate den virtuella datorn innan du växlar diskarna.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Använd [az vm update](/cli/azure/vm#az-vm-update) med fullständiga resurs-ID för den nya disken för den `--osdisk` parameter 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Starta om den virtuella datorn med hjälp av [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Nästa steg**

För att skapa en kopia av en disk, se [ögonblicksbild av en disk](snapshot-copy-managed-disk.md).
