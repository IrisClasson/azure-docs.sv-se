---
title: Skapa en Azure Media Services-jobb från en HTTPS-URL | Microsoft Docs
description: Det här avsnittet visar hur du skapar en jobbindata från en HTTP-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: f6eee912bb3bba112bd13969f1a8d9cb5748e387
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413816"
---
# <a name="create-a-job-input-from-an-https-url"></a>Skapa en jobbindata från en HTTPS-URL

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Ett alternativ är att ange en HTTPS-URL som jobbindata (vilket visas i det här exemplet). Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er. Ett fullständigt exempel finns i den här [GitHub-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Innan du börjar utveckla granska [utveckla med API: er för Media Services v3](media-services-apis-overview.md) (innehåller information om hur du använder API: er, namngivningskonventioner, osv.)

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar ett jobb med en HTTPS-URL som indata.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md).
