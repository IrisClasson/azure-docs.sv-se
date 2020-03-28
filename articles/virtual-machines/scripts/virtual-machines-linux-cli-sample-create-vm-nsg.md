---
title: 'CLI-exempel: Skapa två virtuella datorer med en intern och extern NSG'
description: Skapa två virtuella datorer med intern och extern NSG för att skydda nätverkstrafik med Hjälp av Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e3d1fe3bf464892934198d06b602a5b8bcafb67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75458384"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Säker nätverkstrafik mellan virtuella datorer med hjälp av en NSG

Det här skriptet skapar två virtuella datorer och säkrar inkommande trafik till båda. En virtuell dator är tillgänglig på internet och har en nätverkssäkerhetsgrupp (NSG) som konfigurerats för att tillåta trafik på port 22 och port 80. Den andra virtuella datorn är inte tillgänglig på internet och har en NSG som konfigurerats för att endast tillåta trafik från den första virtuella datorn.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Skapar ett undernät. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Returnerar information om en regel för nätverkssäkerhetsgrupp. I det här exemplet lagras regelnamnet i en variabel för användning senare i skriptet. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Uppdaterar en NSG-regel. I det här exemplet uppdateras klientdelsregeln för att släppa igenom trafik från serverdelens undernät. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
