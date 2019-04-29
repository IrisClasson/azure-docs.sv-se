---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: a8f0e61a953a2e2471e49d571063f6202b7ab76d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540494"
---
Azure virtuella datorer i H-serien är senast inom databehandling virtuella datorer som syftar till att hantera arbetsbelastningar som batchbearbetning, analyser, molekulär modellering och fluiddynamik med höga prestanda. Dessa 8 och 16 vCPU virtuella datorer är baserade på Intel Haswell E5-2667 V3-processor teknik DDR4-minne och SSD-baserad temporär lagring. 

Förutom den imponerande processorkraften erbjuder H-serien olika alternativ för RDMA-nätverk med låg fördröjning med FDR InfiniBand och flera minneskonfigurationer som ger stöd för minnesintensiva beräkningskrav.



## <a name="h-series"></a>H-serien

ACU: 290-300

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 × 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 × 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 × 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 × 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 × 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 × 500 |4 |

<sup>1</sup> för MPI-program, dedikerade RDMA backend-nätverket är aktiverat som FDR InfiniBand-nätverk, som har extremt korta svarstider och hög bandbredd.

<br>






