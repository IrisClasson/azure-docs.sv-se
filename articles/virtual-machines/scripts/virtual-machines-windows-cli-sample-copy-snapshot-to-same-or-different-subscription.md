---
title: Kopiera ögonblicks bild av en hanterad disk till ett exempel på en prenumerations-CLI, virtuell Windows-dator
description: Skript exempel för Azure CLI – kopiera (flytta) ögonblicks bild av en hanterad disk till samma eller en annan prenumeration med CLI på en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 05b73bf62bfce456e65fde153379690bc9823e6a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056745"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli-on-a-windows-vm"></a>Kopiera ögonblicks bild av en hanterad disk till samma eller en annan prenumeration med CLI på en virtuell Windows-dator

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för följande scenarier:

1. Migrera en ögonblicks bild i Premium Storage (Premium_LRS) till standard lagring (Standard_LRS eller Standard_ZRS) för att minska kostnaderna.
1. Migrera en ögonblicks bild från lokalt redundant lagring (Premium_LRS Standard_LRS) till zonens redundanta lagring (Standard_ZRS) för att dra nytta av ZRS-lagringens högre tillförlitlighet.
1. Flytta en ögonblicks bild till en annan prenumeration i samma region för längre kvarhållning.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Hämtar alla egenskaper för en ögonblicksbild med hjälp av ögonblicksbildens namn och resursgruppsegenskaper. ID-egenskapen används för att kopiera ögonblicksbilden till en annan prenumeration.  |
| [az snapshot create](/cli/azure/snapshot) | Kopierar en ögonblicksbild genom att skapa en ögonblicksbild i en annan prenumeration med ID och namn på den överordnade ögonblicksbilden.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Fler CLI-skript exempel för virtuella datorer och hanterade diskar finns i [Azures dokumentation](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)om virtuella Windows-datorer.
