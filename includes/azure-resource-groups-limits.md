---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: cdebdf7258e99457191754cd73513fdb3744f8e9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56323450"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Resurser per [resursgrupp](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (per resurstyp) |800 |Varierar per resurstyp |
| Distributioner per resursgrupp i distributionshistoriken |800 |800 |
| Resurser per distribution |800 |800 |
| Hanteringslås (per unika omfattning) |20 |20 |
| Antal taggar (per resurs eller resursgrupp) |15 |15 |
| Taggen nyckellängd |512 |512 |
| Taggen värdets längd |256 |256 |


#### <a name="template-limits"></a>Mall för gränser

| Värde | Standardgräns | Övre gräns |
| --- | --- | --- |
| Parametrar |256 |256 |
| Variabler |256 |256 |
| Resurser (inklusive antal kopior) |800 |800 |
| Utdata |64 |64 |
| Malluttryck |24,576 tecken |24,576 tecken |
| Resurser i exporterade mallar |200 |200 | 
| Mallstorlek |1 MB |1 MB |
| Parametern filstorlek |64 kB |64 kB |

Du kan överskrida vissa begränsningar för mallen med hjälp av en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/resource-group-linked-templates.md). Du kan kombinera flera värden i ett objekt för att minska antalet parametrar, variabler eller utdata. Mer information finns i [objekt som parametrar](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från historiken som inte längre behövs. Du kan ta bort poster från historiken över med [az group deployment ta bort](/cli/azure/group/deployment) för Azure CLI eller [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) i PowerShell. Distribuera resurser påverkas inte om du tar bort en post från distributionshistoriken. 