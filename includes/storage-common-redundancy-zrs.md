---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113787"
---
Zonredundant lagring (ZRS) replikerar dina data synkront i tre lagringskluster i en enda region. Varje lagringskluster är fysiskt avgränsade från de andra och finns i sin egen tillgänglighetszon (AZ). Varje tillgänglighetszon&mdash;och ZRS-klustret i den&mdash;är autonoma och inkluderar olika verktyg och funktioner.

När du lagrar dina data i ett lagringskonto med ZRS-replikering kan fortsätta du att komma åt och hantera dina data om en tillgänglighetszon blir otillgänglig. ZRS ger utmärkt prestanda och låg fördröjning. ZRS ger samma [skalbarhetsmål](../articles/storage/common/storage-scalability-targets.md) som [lokalt redundant lagring (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Överväg ZRS för scenarier som kräver konsekvens, hållbarhet och hög tillgänglighet. Även om ett avbrott eller en naturkatastrof visas med en tillgänglighetszon otillgänglig, ZRS ger hållbarhet för lagringsobjekt på minst 99,9999999999% (12 9) under ett givet år.

Läs mer om tillgänglighetszoner [Tillgänglighetszoner översikt](https://docs.microsoft.com/azure/availability-zones/az-overview).