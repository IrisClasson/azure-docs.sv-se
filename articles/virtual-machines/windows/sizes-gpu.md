---
title: Azure Windows VM-storlekar - GPU | Microsoft Docs
description: Visar olika GPU optimerad storlekar som är tillgängliga för Windows-datorer i Azure. Visar information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredd för storlekar i den här serien.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jonbeck
ms.openlocfilehash: 222f131e92a6018b4f70c65476cc2c47fb1d1dd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776344"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimerad storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

Om du vill dra nytta av GPU-funktionerna i Azure N-serien virtuella datorer som kör Windows, måste NVIDIA GPU-drivrutiner installeras. Den [NVIDIA GPU-drivrutinen tillägget](../extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA CUDA- eller NÄTVERKSBASERADE drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg, till exempel Azure PowerShell eller Azure Resource Manager-mallar. Se den [NVIDIA GPU-drivrutinen tillägget dokumentation](../extensions/hpccompute-gpu-windows.md) operativsystem som stöds och distributionsanvisningar för. Allmän information om VM-tillägg finns i [Azure virtuella datorer, tillägg och funktioner](../extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [drivrutinsinstallationer för N-serien GPU för Windows](n-series-driver-setup.md) operativsystem, drivrutiner och anvisningar för installation och verifiering.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.

