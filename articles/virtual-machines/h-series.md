---
title: H-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i H-serien.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e9f876f3d20af01867283f550590b3af23dec662
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926628"
---
# <a name="h-series"></a>H-serien

Virtuella datorer i H-serien är optimerade för program som drivs av höga CPU-frekvenser eller stora minnen per kärn krav. Virtuella datorer i H-serien 8 eller 16 Intel Xeon E5 2667 v3 processor kärnor, upp till 14 GB RAM-minne per CPU-kärna och ingen hyperthreading-funktion. H-seriens funktioner 56 GB/s Mellanox FDR InfiniBand i en icke-blockerande fett träds konfiguration för konsekvent RDMA-prestanda. Virtuella datorer i H-serien har stöd för Intel MPI 5. x och MS-MPI.

ACU: 290–300

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnes bandbredd GB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GB/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Högsta Ethernet-nätverkskort |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 × 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 × 500 | 4 |

<sup>1</sup> för MPI-program aktive ras dedicerade RDMA-backend-nätverk av FDR Infiniband-nätverket.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> Bland de [virtuella](sizes-hpc.md#rdma-capable-instances)datorerna med RDMA-kapacitet är H-serien inte SR-IOV-aktiverad. Därför skiljer sig de [virtuella dator avbildningarna](./workloads/hpc/configure.md#vm-images), [InfiniBand-skrivardrivrutiner](./workloads/hpc/enable-infiniband.md) och [MPI-bibliotek](./workloads/hpc/setup-mpi.md) som stöds från SR-IOV-aktiverade virtuella datorer.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [att konfigurera virtuella datorer](./workloads/hpc/configure.md), [Aktivera INFINIBAND](./workloads/hpc/enable-infiniband.md), konfigurera [MPI](./workloads/hpc/setup-mpi.md) och optimera HPC-program för Azure i [HPC-arbetsbelastningar](./workloads/hpc/overview.md).
- Läs om de senaste meddelandena och några HPC-exempel och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
- Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
