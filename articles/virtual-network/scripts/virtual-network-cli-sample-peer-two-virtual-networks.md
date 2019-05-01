---
title: Skriptexempel för Azure CLI – Peering i två virtuella nätverk | Microsoft Docs
description: Skriptexempel för Azure CLI – Peering i två virtuella nätverk.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 82f4f06cd4e615df38f2251be9aef9e8578b9df4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726795"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Skriptexempel för peering i två virtuella nätverk

Skriptexemplet skapar och ansluter två virtuella nätverk i samma region via Azures nätverk. När du har kört skriptet har du en peering mellan två virtuella nätverk.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/bash) eller från en lokal installation av Azure CLI. Om du använder CLI lokalt kräver skriptet att du kör version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | Skapar en peering mellan två virtuella nätverk.  |
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Du kan hitta fler skriptexempel om virtuella nätverk för CLI i [CLI-exempel för virtuella nätverk](../cli-samples.md).
