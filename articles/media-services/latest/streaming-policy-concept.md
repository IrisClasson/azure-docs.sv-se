---
title: Strömmande principer i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vilka strömmande principer som är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: 30164e1af1948f86c68e102f4d348674ea931e7b
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544021"
---
# <a name="streaming-policies"></a>Principer för direktuppspelning

I Azure Media Services v3 kan du använda [strömmande principer](/rest/api/media/streamingpolicies) för att definiera strömmande protokoll och krypterings alternativ för dina [strömmande positionerare](streaming-locators-concept.md). Media Services v3 innehåller några fördefinierade strömmande principer så att du kan använda dem direkt för utvärdering eller produktion. 

För närvarande tillgängliga fördefinierade strömmande principer:<br/>
* Predefined_DownloadOnly
* Predefined_ClearStreamingOnly
* Predefined_DownloadAndClearStreaming
* Predefined_ClearKey
* Predefined_MultiDrmCencStreaming 
* Predefined_MultiDrmStreaming

I följande "besluts träd" kan du välja en fördefinierad strömmande princip för ditt scenario.

> [!IMPORTANT]
> * Egenskaper för **strömmande principer** som är av typen datetime är alltid i UTC-format.
> * Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina strömmande positionerare när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Beslutsträd

Klicka på bilden för att visa den i full storlek.  

[![Diagram som visar ett besluts träd som är utformat för att hjälpa dig att välja en fördefinierad strömnings princip för ditt scenario.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Om du krypterar innehållet måste du skapa en princip för [innehålls nycklar](content-key-policy-concept.md), **innehålls nyckel principen** behövs inte för att rensa strömma eller ladda ned. 

Om du har särskilda krav (till exempel om du vill ange olika protokoll, behöver använda en anpassad nyckel leverans tjänst eller om du behöver använda ett tydligt ljud spår) kan du [skapa](/rest/api/media/streamingpolicies/create) en anpassad direkt uppspelnings princip. 

## <a name="get-a-streaming-policy-definition"></a>Hämta en definition för strömnings princip  

Om du vill se definitionen av en strömmande princip använder du [Get](/rest/api/media/streamingpolicies/get) och anger namnet på principen. Ett exempel:

### <a name="rest"></a>REST

Begäran:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Svar:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtrering, sortering, växling

Se [filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Använd AES-128 dynamisk kryptering och Key Delivery Service](protect-with-aes128.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
