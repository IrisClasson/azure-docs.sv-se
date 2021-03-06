---
title: Gräns för Azure Stack gräns | Microsoft Docs
description: Lär dig mer om gränser och rekommenderade storlekar när du distribuerar och använder Azure Stack Edge, inklusive tjänst gränser, enhets gränser och lagrings gränser.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 8bbcf3f61121813b91cb951809992d10977a640c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922718"
---
# <a name="azure-stack-edge-limits"></a>Gränser för Azure Stack Edge

Tänk på följande begränsningar när du distribuerar och använder din Microsoft Azure Stack Edge-lösning. 

## <a name="azure-stack-edge-service-limits"></a>Gränser för Azure Stack Edge-tjänsten

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack enhets gränser

I följande tabell beskrivs gränserna för Azure Stack Edge-enheten. 

| Beskrivning | Värde |
|---|---|
|Nej. av filer per enhet |100 000 000 |
|Nej. av resurser per enhet |24 |
|Nej. av resurser per behållare |1 |
|Maximal fil storlek som skrivs till en resurs| 5 TB |

## <a name="azure-storage-limits"></a>Azure Storage-gränser

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Varningar för datauppladdning

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Storleks gränser för Azure Storage-konto och objekt storlek

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Storleks gränser för Azure-objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nästa steg

- [Förbered för att distribuera Azure Stack Edge](azure-stack-edge-deploy-prep.md)
