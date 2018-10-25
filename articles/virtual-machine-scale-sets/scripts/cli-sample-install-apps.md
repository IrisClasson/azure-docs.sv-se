---
title: Azure CLI-exempel – Installera appar | Microsoft Docs
description: Azure CLI-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: a551856d5be1df63f4376c76c50ed53564afc175
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465040"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Installera program till en skalningsuppsättning för en virtuell dator med Azure CLI
Det här skriptet skapar en VM-skalningsuppsättning som kör Ubuntu och använder det anpassade skripttillägget för att installera ett grundläggande webbprogram. När skriptet har körts kan du komma åt webbappen via en webbläsare.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa en resursgrupp, en VM-skalningsuppsättning och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Skapar VM-skalningsuppsättningen och ansluter den till det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen. En lastbalanserare skapas även för att distribuera trafik till flera virtuella datorinstanser. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Installerar det anpassade Azure-skripttillägget för att köra ett skript som förbereder datadiskarna på varje virtuell datorinstans. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Skapar en regel för lastbalanseraren för att distribuera trafik på TCP-port 80 till virtuella datorinstanser i skalningsuppsättningen. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Hämtar information om den offentliga IP-adress som används av lastbalanseraren. |
| [az group delete](/cli/azure/ad/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure CLI-skriptexempel för skalningsuppsättningar för virtuella datorer finns i [dokumentationen för Azure-skalningsuppsättningar för virtuella datorer](../cli-samples.md).
