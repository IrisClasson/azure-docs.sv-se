---
title: CLI exempel – Skapa en DNS-zon och -post för ett domännamn – Azure | Microsoft Docs
description: I det här Azure CLI-skriptexemplet visas hur du skapar en DNS-zon och -post för ett domännamn
services: load-balancer
documentationcenter: traffic-manager
author: vhorne
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 59ec8d4f93b18469818c9ead2e965679e41360ae
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694660"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Exempel för Azure CLI-skript: Skapa en DNS-zon och -post

I det här Azure CLI-skriptexemplet skapas en DNS-zon och -post för ett domännamn. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive

# Create a resource group.
az group create \
  -n myResourceGroup \
  -l eastus

# Create a DNS zone. Substitute zone name "contoso.com" with the values for your own.

az network dns zone create \
  -g MyResourceGroup \
  -n contoso.com

# Create a DNS record. Substitute zone name "contoso.com" and IP address "1.2.3.4* with the values for your own.

az network dns record-set a add-record \
  -g MyResourceGroup \
  -z contoso.com \
  -n www \
  -a 1.2.3.4

# Get a list the DNS records in your zone
az network dns record-set list \
  -g MyResourceGroup \ 
  -z contoso.com
```

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, DNS-zonen och alla relaterade resurser.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Skapar en Azure DNS-zon. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Lägger till en *A*-post för en DNS-zon. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Visar alla *A*-postuppsättningar i en DNS-zon. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

