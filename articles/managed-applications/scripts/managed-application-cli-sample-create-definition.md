---
title: Skriptexempel för Azure CLI – Skapa en definition för ett hanterat program | Microsoft Docs
description: Skriptexempel för Azure CLI – Skapa en definition för ett hanterat program
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 79af2918c2342d1a0cc3b11763c30b35ab5e9f72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043908"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Skapa en definition för ett hanterat program med Azure CLI

Det här skriptet publicerar en definition för hanterade program i en tjänstkatalog. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommando för att skapa definitionen för det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | Skapa en definition för det hanterade programmet. Ange det paket som innehåller filerna som krävs. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [översikten över Azure Managed Application](../overview.md).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).
