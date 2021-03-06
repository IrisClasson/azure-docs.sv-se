---
title: inkludera fil
titleSuffix: Azure
description: inkludera fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680783"
---
PowerShell-cmdleten **Get-AzPeeringLocation** returnerar en lista med peering-platser med den obligatoriska parametern `Kind` , som du kommer att använda i senare steg.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Direkta peering-platser innehåller följande fält:
* PeeringLocation 
* Land
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Kontrol lera att du är närvarande vid önskad peering-anläggning genom att referera till [PeeringDB](https://wwww.peeringdb.com).

Det här exemplet visar hur du använder Seattle som peering-plats för att skapa en direkt peering.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```