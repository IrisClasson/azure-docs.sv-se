---
title: Jämförelse av Video Indexer och Azure Media Services v3 förinställningar | Microsoft Docs
description: Det här avsnittet jämför Video Indexer och Azure Media Services v3 förinställningar.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 275178998948e357a6a72fbe5d0b3c9c01485a3a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800164"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Jämför Azure Media Services v3 förinställningar och Video Indexer 

Den här artikeln jämförs funktionerna i **Video Indexer API: er** och **API: er för Media Services v3**. 

För närvarande finns ett överlapp mellan funktioner som erbjuds av den [Video Indexer API: er](https://api-portal.videoindexer.ai/) och [API: er för Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). Följande tabell ger de aktuella riktlinjerna för att förstå skillnader och likheter. 

## <a name="compare"></a>Jämför

|Funktion|Video Indexer API: er |Video Analyzer och ljud Analyzer förinställningar<br/>i API: er för Media Services v3|
|---|---|---|
|Media Insights|[Förbättrad](video-indexer-output-json-v2.md) |[Grunderna](../latest/intelligence-concept.md)|
|Upplevelser|Se en fullständig lista över funktioner som stöds: <br/> [Översikt](video-indexer-overview.md)|Returnerar endast videoinsikter|
|Fakturering|[Prissättningen av Medietjänsterna](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prissättningen av Medietjänsterna](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Efterlevnad|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci), och [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certifierade. De senaste uppdateringarna finns [aktuella certifieringar statusen för Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Media Services är kompatibelt med många certifieringar. Kolla in [Azure efterlevnad Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) och Sök efter ”Media Services” för att se om den överensstämmer med ett certifikat av intresse.|
|Kostnadsfri utvärderingsversion|Östra USA|Saknas|
|Regional tillgänglighet|Östra USA 2, södra centrala USA, västra USA 2, Nordeuropa, Västeuropa, Sydostasien, Östasien och Östra Australien.  De senaste uppdateringarna finns i [produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) sidan.|Se [Azure-status](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)

[Översikt över Media Services v3](../latest/media-services-overview.md)
