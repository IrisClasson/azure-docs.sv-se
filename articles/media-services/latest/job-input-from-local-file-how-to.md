---
title: Skapa en Azure Media Services-jobb från en lokal fil | Microsoft Docs
description: Det här avsnittet visar hur du skapar en jobbindata från en lokal fil.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4860390820c29cd8f44ebbf5e05e8c58090d5740
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996972"
---
# <a name="create-a-job-input-from-a-local-file"></a>Skapa en jobbindata från en lokal fil

I Media Services v3, när du skickar in jobb för att bearbeta videor, har du ska berätta var du hittar indatavideon för Media Services. Indatavideon kan lagras som en Media Service-tillgång då du skapar en indatatillgången som baseras på en fil (som lagras lokalt eller i Azure Blob storage). Det här avsnittet visar hur du skapar en jobbindata från en lokal fil. Ett fullständigt exempel finns i den här [GitHub-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar en indatatillgången och använder den som indata för jobbet. Funktionen CreateInputAsset utför följande åtgärder:

* Skapar tillgången
* Hämtar en skrivbar [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) till tillgångens [container i lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Laddar upp filen till containern i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Nästa steg

[Skapa en jobbindata från en HTTPS-URL](job-input-from-http-how-to.md).
