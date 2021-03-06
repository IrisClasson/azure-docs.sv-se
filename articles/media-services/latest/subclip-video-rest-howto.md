---
title: Under klipp en video vid kodning med Azure Media Services REST
description: I det här avsnittet beskrivs hur du klipper en video när du kodar med Azure Media Services med hjälp av REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: b112b3844ddb3758b79a086b624a227127e65ce2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091886"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Under klipp en video vid kodning med Media Services REST

Du kan trimma eller under klipp en video när du kodar den med ett [jobb](/rest/api/media/jobs). Den här funktionen fungerar med alla [transformeringar](/rest/api/media/transforms) som har skapats med antingen [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar. 

RESTEN av exemplet i det här avsnittet skapar ett jobb som rensar en video när den skickar ett kodnings jobb. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i det här avsnittet måste du:

- [Skapa ett Azure Media Services-konto](./create-account-howto.md).
- [Konfigurera PostMan för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).
    
    Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Skapa en transformering och en utmatnings till gångar. Du kan se hur du skapar en transformering och en utmatnings till gångar i [koda en fjärrfil baserat på URL och strömma video rest-](stream-files-tutorial-with-rest.md) kursen.
- Läs avsnittet [encoding Concept](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Skapa ett under urklipps jobb

1. I Postman-samlingen som du laddade ned väljer du **transformeringar och jobb**  ->  **skapa jobb med under Urklipp**.
    
    **Placerings** förfrågan ser ut så här:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Uppdatera värdet för miljövariabeln "transformName" med Transformations namnet. 
1. Välj fliken **brödtext** och uppdatera "myOutputAsset" med namnet på din utmatnings till gång.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Tryck på **Skicka**.

    Du kan se **svaret** med information om jobbet som skapades och skickades och jobbets status. 

## <a name="next-steps"></a>Nästa steg

[Koda med en anpassad transformering](custom-preset-rest-howto.md) 
