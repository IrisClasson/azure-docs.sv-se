---
title: Namnkonventioner för Azure VM-storlekar
description: Förklarar namngivnings konventioner som används för virtuella Azure-storlekar
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: a49d1cc47515e698737bf833af09d30da16712a6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832333"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Namngivnings konventioner för virtuella Azure-datorer

Den här sidan beskriver namngivnings konventionerna som används för virtuella Azure-datorer. Virtuella datorer använder dessa namn konventioner för att ange varierande funktioner och specifikationer.

## <a name="naming-convention-explanation"></a>Förklaring av namngivnings konvention

**[Familj]**  +  **[Under grupp *]**  +  **[antal virtuella processorer]**  +  **[Additiva funktioner]**  +  **[Accelerator-typ *]**  +  **[Version]**

|Värde | Förklaring|
|---|---|
| Familj | Anger serien för VM-serien| 
| * Under serie | Används endast för specialiserade VM-differentieringar|
| antal virtuella processorer| Anger antalet virtuella processorer för den virtuella datorn |
| Additiva funktioner | En eller flera gemener anger additiva funktioner, till exempel: <br> a = AMD-baserad processor <br> d = disk (lokal temporär disk finns); Det här är för nya virtuella Azure-datorer, se [Ddv4 och Ddsv4-serien](./ddv4-ddsv4-series.md) <br> h = stöd för vilo läge <br> i = isolerad storlek <br> l = ont om minne; en mindre mängd minne än minnes intensiv storlek <br> m = minnes intensiv; den största mängden minne i en viss storlek <br> t = litet minne; den minsta mängden minne i en viss storlek <br> r = RDMA-kompatibel <br> s = Premium Storage möjligt, inklusive eventuell användning av [Ultra SSD](./windows/disks-types.md#ultra-disk) (Obs! vissa nyare storlekar utan attributet för s kan fortfarande stödja Premium Storage, t. ex. M128, M64 osv.)<br> |
| * Typ av Accelerator | Anger typen av maskin varu Accelerator i specialiserade/GPU-SKU: er. Endast de nya specialiserade/GPU-SKU: er som lanserats från Q3 2020 kommer att ha maskin varu acceleratorn i namnet. |
| Version | Anger versionen för VM-serien |

## <a name="example-breakdown"></a>Exempel på detalj nivå

**[Familj]**  +  **[Under grupp *]**  +  **[antal virtuella processorer]**  +  **[Additiva funktioner]**  +  **[Accelerator-typ *]**  +  **[Version]**

### <a name="example-1-m416ms_v2"></a>Exempel 1: M416ms_v2

|Värde | Förklaring|
|---|---|
| Familj | M | 
| antal virtuella processorer | 416 |
| Additiva funktioner | m = minnes intensiv <br> s = Premium Storage kapabel |
| Version | v2 |

### <a name="example-2-nv16as_v4"></a>Exempel 2: NV16as_v4

|Värde | Förklaring|
|---|---|
| Familj | N | 
| Under grupp | V |
| antal virtuella processorer | 16 |
| Additiva funktioner | a = AMD-baserad processor <br> s = Premium Storage kapabel |
| Version | IPv4 |

### <a name="example-3-nc4as_t4_v3"></a>Exempel 3: NC4as_T4_v3

|Värde | Förklaring|
|---|---|
| Familj | N | 
| Under grupp | C |
| antal virtuella processorer | 4 |
| Additiva funktioner | a = AMD-baserad processor <br> s = Premium Storage kapabel |
| Accelerator-typ | T4 |
| Version | v3 |

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga [VM-storlekar](./sizes.md) i Azure. 