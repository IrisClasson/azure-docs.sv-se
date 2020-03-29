---
title: Kom igång med att leverera innehåll på begäran med REST | Microsoft-dokument
description: Den här självstudien går igenom stegen för att implementera ett innehållsleveransprogram på begäran med Azure Media Services med REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8989acc6d21a3c53be9d97c74ed7fbf03ba54819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773684"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Kom igång med att leverera innehåll på begäran med REST  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Den här snabbstarten går igenom stegen för att implementera ett Program för leverans av Video-on-Demand -innehåll med hjälp av AZURE Media Services (AMS) REST API:er.

Självstudierna innehåller det grundläggande Media Services-arbetsflödet och de vanligaste programmeringsobjekt och -uppgifter som krävs för utveckling av Media Services. När självstudien är klar kan du strömma eller gradvis hämta en exempelmediefil som du har laddat upp, kodat och hämtat.

Följande bild visar några av de vanligast använda objekten när du utvecklar VoD-program mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Krav
Följande förutsättningar krävs för att börja utveckla med Media Services med REST API:er.

* Ett Azure-konto. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Ett Media Services-konto. Om du vill skapa ett Media Services-konto läser du [Så här skapar du ett Media Services-konto](media-services-portal-create-account.md).
* Förståelse för hur man utvecklar med REST API för mediatjänster. Mer information finns i [översikt över REST-API FÖR Media Services.](media-services-rest-how-to-use.md)
* Ett program som du väljer som kan skicka HTTP-begäranden och svar. Den här självstudien använder [Fiddler](https://www.telerik.com/download/fiddler).

Följande uppgifter visas i den här snabbstarten.

1. Starta slutpunkt för direktuppspelning (med hjälp av Azure Portal).
2. Anslut till Media Services-kontot med REST API.
3. Skapa en ny tillgång och ladda upp en videofil med REST API.
4. Koda källfilen till en uppsättning adaptiva bitrate MP4-filer med REST API.
5. Publicera tillgången och få strömmande och progressiva nedladdningsadresser med REST API.
6. Spela upp ditt innehåll.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att finnas kvar under en längre tid (principer som inte är uppladdningsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Mer information om AMS REST-entiteter som används i den här artikeln finns i [Azure Media Services REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Se även [Azure Media Services-begrepp](media-services-concepts.md).

>[!NOTE]
>När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Starta slutpunkter för direktuppspelning med Azure Portal

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera video via adaptiv bithastighetsstreaming. Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4-kodade innehåll med anpassningsbar bithastighet i direktuppspelningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) direkt när du så önskar, utan att du behöver lagra på förhand paketerade versioner av vart och ett av dessa direktuppspelningsformat.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

Starta slutpunkten för direktuppspelning genom att göra följande:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. I fönstret Inställningar klickar du på Slutpunkter för direktuppspelning.
3. Klicka på den slutpunkt för direktuppspelning som är standard.

    Fönstret INFORMATION OM DEN SLUTPUNKT FÖR DIREKTUPPSPELNING SOM ÄR STANDARD visas.

4. Klicka på ikonen Start.
5. Klicka på knappen Spara för att spara ändringarna.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Ansluta till Media Services-kontot med REST API

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Skapa en ny tillgång och ladda upp en videofil med REST API

I Media Services överför du dina digitala filer till en tillgång. **Asset-entiteten** kan innehålla video- ljud, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text (och metadata om dessa filer.)  När filerna har överförts till tillgången lagras ditt innehåll säkert i molnet för vidare bearbetning och streaming.

Ett av de värden som du måste ange när du skapar en tillgång är alternativ för att skapa tillgångar. Egenskapen **Alternativ** är ett uppräkningsvärde som beskriver de krypteringsalternativ som en tillgång kan skapas med. Ett giltigt värde är ett av värdena i listan nedan, inte en kombination av värden från den här listan:

* **Ingen** = **0** - Ingen kryptering används. När du använder det här alternativet är ditt innehåll inte skyddat under överföring eller i vila i lagring.
    Om du planerar att leverera en MP4 med progressivt nedladdning ska du använda det här alternativet.
* **StorageEncrypted** = **1** - Krypterar ditt rensa innehåll lokalt med AES-256-bitars kryptering och överför det sedan till Azure Storage där det lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
* **CommonEncryptionProtected** = **2** - Använd det här alternativet om du laddar upp innehåll som redan har krypterats och skyddats med gemensam kryptering eller PlayReady DRM (till exempel Smooth Streaming skyddas med PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – Använd det här alternativet om du laddar upp HLS krypterat med AES. Filerna måste ha kodats och krypterats av Transform Manager.

### <a name="create-an-asset"></a>Skapa en tillgång
En tillgång är en behållare för flera typer eller uppsättningar av objekt i Media Services, inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text. I REST API, skapa en tillgång kräver att skicka POST begäran till Media Services och placera någon egendom information om din tillgång i begäran kroppen.

I följande exempel visas hur du skapar en tillgång.

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP-svar**

Om det lyckas returneras följande:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Skapa en tillgångsfil
[AssetFile-entiteten](https://docs.microsoft.com/rest/api/media/operations/assetfile) representerar en video- eller ljudfil som lagras i en blob-behållare. En tillgångsfil är alltid associerad med en tillgång och en tillgång kan innehålla en eller flera AssetFiles. Aktiviteten Media Services Encoder misslyckas om ett tillgångsfilobjekt inte är associerat med en digital fil i en blob-behållare.

När du har laddat upp din digitala mediefil till en blob-behållare använder du **APPENS HTTP-begäran** för att uppdatera AssetFile med information om mediefilen (som visas senare i avsnittet).

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-svar**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Skapa AccessPolicy med skrivbehörighet
Innan du överför filer till blob-lagring anger du åtkomstprinciprättigheterna för att skriva till en tillgång. Det gör du genom att publicera en HTTP-begäran till entityten AccessPolicies. Definiera ett DurationInMinutes-värde när du skapar eller så visas ett felmeddelande om 500 intern server som svar. Mer information om AccessPolicies finns i [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

I följande exempel visas hur du skapar en AccessPolicy:

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP-svar**

Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Hämta url:en

Skapa en SAS-positionerare om du vill ta emot den faktiska överförings-URL:en. Positionerare definierar starttid och typ av anslutningsslutpunkt för klienter som vill komma åt filer i en tillgång. Du kan skapa flera positionerarentiteter för ett givet AccessPolicy- och Tillgångspar för att hantera olika klientbegäranden och behov. Var och en av dessa positionerare använder StartTime-värdet plus värdet DurationInMinutes för AccessPolicy för att bestämma hur länge en URL kan användas. Mer information finns i [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

En SAS-URL har följande format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Vissa förutsättningar gäller:

* Du kan inte ha fler än fem unika positionerare som är associerade med en viss tillgång samtidigt. 
* Om du behöver ladda upp dina filer omedelbart bör du ställa in StartTime-värdet till fem minuter före den aktuella tiden. Detta beror på att det kan finnas en klocksnedställning mellan klientdatorn och Media Services. Starttime-värdet måste också vara i följande DateTime-format: YYYY-MM-DDTHH:mm:ssZ (till exempel "2014-05-23T17:53:50Z").    
* Det kan finnas en fördröjning på 30-40 sekunder efter att en sökare har skapats till när den är tillgänglig för användning. Det här problemet gäller både [SAS-URL:en](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) och Origin Locators.

I följande exempel visas hur du skapar en SAS-URL-positionerare, som definieras av egenskapen Type i förfrågetexten ("1" för en SAS-positionerare och "2" för en ursprungspositionerare på begäran). Egenskapen **Path** som returneras innehåller den URL som du måste använda för att ladda upp filen.

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-svar**

Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Ladda upp en fil till en behållare för bloblagring
När du har angett AccessPolicy och Locator överförs den faktiska filen till en Azure blob-lagringsbehållare med Azure Storage REST API:er. Du måste ladda upp filerna som blockblobar. Sidblobar stöds inte av Azure Media Services.  

> [!NOTE]
> Du måste lägga till filnamnet för filen som du vill överföra till värdet Locator **Path** som tas emot i föregående avsnitt. Till exempel `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Mer information om hur du arbetar med Azure storage blobbar finns i [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Uppdatera AssetFile
Nu när du har laddat upp filen uppdaterar du fileasset-storleksinformationen (och annan) information. Ett exempel:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-svar**

Om det lyckas returneras följande:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Ta bort positioneraren och AccessPolicy
**HTTP-begäran**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**

Om det lyckas returneras följande:

    HTTP/1.1 204 No Content
    ...

**HTTP-begäran**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-svar**

Om det lyckas returneras följande:

    HTTP/1.1 204 No Content
    ...

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Koda källfilen till en uppsättning adaptiva MP4-filer med bithastighet

När du har inmatat tillgångar i Media Services kan media kodas, transmuxed, vattenstämpel och så vidare, innan det levereras till kunder. Dessa aktiviteter schemaläggs och körs mot flera bakgrundsrollinstanser för höga prestanda och tillgänglighet. Dessa aktiviteter kallas Jobb och varje jobb består av atomaktiviteter som utför det verkliga arbetet med tillgångsfilen (mer information finns i [Jobb](https://docs.microsoft.com/rest/api/media/operations/job), Aktivitetsbeskrivningar). [Task](https://docs.microsoft.com/rest/api/media/operations/task)

Som nämndes tidigare, när du arbetar med Azure Media Services ett av de vanligaste scenarierna är att leverera adaptiv bitrate streaming till dina kunder. Media Services kan dynamiskt paketera en uppsättning adaptiva bithastighet MP4-filer till något av följande format: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

I följande avsnitt visas hur du skapar ett jobb som innehåller en kodningsaktivitet. Uppgiften anger att mezzaninfilen ska kodas om till en uppsättning adaptiva bithastighets-MP4:er med **Media Encoder Standard**. Avsnittet visar också hur du övervakar förloppet för jobbbearbetning. När jobbet är klart kan du skapa positionerare som behövs för att få åtkomst till dina tillgångar.

### <a name="get-a-media-processor"></a>Skaffa en medieprocessor
I Media Services är en medieprocessor en komponent som hanterar en viss bearbetningsuppgift, till exempel kodning, formatkonvertering, kryptering eller dekryptering av medieinnehåll. För kodningsuppgiften som visas i den här självstudien kommer vi att använda Media Encoder Standard.

Följande kod begär kodarens id.

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Skapa ett jobb
Varje jobb kan ha en eller flera aktiviteter beroende på vilken typ av bearbetning du vill utföra. Via REST API kan du skapa jobb och deras relaterade uppgifter på ett av två sätt: Uppgifter kan definieras infogade via egenskapen Uppgiftervigering på jobbentiteter eller via OData-batchbearbetning. Media Services SDK använder batchbearbetning. För att kodexemplen i den här artikeln ska kunna läsas definieras dock uppgifter infogade. Information om batchbearbetning finns i [OData-batchbearbetning (Open Data Protocol).](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)

I följande exempel visas hur du skapar och bokför ett jobb med en aktivitet inställd på att koda en video med en viss upplösning och kvalitet. Följande dokumentationsavsnitt innehåller en lista över alla [aktivitetsförinställningar som](https://msdn.microsoft.com/library/mt269960) stöds av Media Encoder Standard-processorn.  

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP-svar**

Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Det finns några viktiga saker att notera i en jobbbegäran:

* TaskBody-egenskaper MÅSTE använda litteral XML för att definiera antalet indata eller utdatatillgångar som används av aktiviteten. The Task article contains the XML Schema Definition for the XML.
* I TaskBody-definitionen måste varje `<inputAsset>` `<outputAsset>` inre värde för och ställas in som JobInputAsset(value) eller JobOutputAsset(värde).
* En aktivitet kan ha flera utdatatillgångar. En JobOutputAsset(x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som en indatatillgång för en aktivitet.
* Aktiviteter får inte utgöra en cykel.
* Värdeparametern som du skickar till JobInputAsset eller JobOutputAsset representerar indexvärdet för en tillgång. De faktiska tillgångarna definieras i navigeringsegenskaperna InputMediaAssets och OutputMediaAssets i definitionen av jobbentitet.

> [!NOTE]
> Eftersom Media Services bygger på OData v3 refereras de enskilda tillgångarna i InputMediaAssets och OutputMediaAssets navigeringsegenskapssamlingar via ett "__metadata : uri"-namnvärdespar.
>
>

* InputMediaAssets mappar till en eller flera tillgångar som du har skapat i Media Services. OutputMediaAssets skapas av systemet. De refererar inte till en befintlig tillgång.
* OutputMediaAssets kan namnges med attributet assetName. Om det här attributet inte finns är namnet på OutputMediaAsset `<outputAsset>` det inre textvärdet för elementet som har ett suffix med värdet Jobbnamn eller värdet Jobb-ID (i det fall där egenskapen Namn inte har definierats). Om du till exempel anger ett värde för assetName till "Exempel" anges egenskapen OutputMediaAsset Name till "Exempel". Men om du inte angav ett värde för assetName, men angav jobbnamnet till "NewJob", skulle namnet För OutputMediaAsset vara "JobOutputAsset(value)_NewJob".

    I följande exempel visas hur du ställer in attributet assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Så här aktiverar du aktivitetskedja:

  * Ett jobb måste ha minst två aktiviteter
  * Det måste finnas minst en aktivitet vars indata är utdata från en annan aktivitet i projektet.

Mer information finns i [Skapa ett kodningsjobb med REST API FÖR Media Services.](media-services-rest-encode-asset.md)

### <a name="monitor-processing-progress"></a>Förloppet för övervakarebehandling
Du kan hämta jobbstatusen med egenskapen State, som visas i följande exempel:

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-svar**

Om det lyckas returneras följande svar:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Avbryta ett jobb
Med Media Services kan du avbryta jobb som körs via funktionen CancelJob. Det här anropet returnerar en 400-felkod om du försöker avbryta ett jobb när tillståndet avbryts, avbryts, fel eller är avslutat.

I följande exempel visas hur du anropar CancelJob.

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Om det lyckas returneras en 204-svarskod utan meddelandetext.

> [!NOTE]
> Du måste URL-koda jobb-ID (normalt nb:jid:UUID: somevalue) när du skickar in det som en parameter till CancelJob.
>
>

### <a name="get-the-output-asset"></a>Hämta utdatatillgången
Följande kod visar hur du begär utdatatillgångs-ID.

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publicera tillgången och få strömmande och progressiva nedladdningsadresser med REST API

Om du vill strömma eller hämta en tillgång behöver du först ”publicera” den genom att skapa en positionerare. Lokaliserare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) och Access Signature (SAS)-positionerare som används för att hämta mediefiler. 

När du har skapat positionerarna kan du skapa webbadresserna som används för att strömma eller hämta dina filer.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

En strömmande URL för Smooth Streaming har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

En strömmande URL för HLS har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

En strömmande URL för MPEG DASH har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

En SAS-URL som används för att hämta filer har följande format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

I det här avsnittet visas hur du utför följande uppgifter som krävs för att "publicera" dina tillgångar.  

* Skapa AccessPolicy med läsbehörighet
* Skapa en SAS-URL för nedladdning av innehåll
* Skapa en ursprungs-URL för direktuppspelning av innehåll

### <a name="creating-the-accesspolicy-with-read-permission"></a>Skapa AccessPolicy med läsbehörighet
Innan du hämtar eller direktar något medieinnehåll definierar du först en AccessPolicy med läsbehörighet och skapar rätt locatorentitet som anger vilken typ av leveransmekanism du vill aktivera för dina klienter. Mer information om tillgängliga egenskaper finns i [AccessPolicy Entitetsegenskaper](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

I följande exempel visas hur du anger AccessPolicy för läsbehörigheter för en viss tillgång.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Om det lyckas returneras en 201-lyckadeskod som beskriver den AccessPolicy-entitet som du skapade. Du använder sedan AccessPolicy-ID:t tillsammans med tillgångs-ID:t för den tillgång som innehåller filen som du vill leverera (till exempel en utdatatillgång) för att skapa locatorentiteten.

> [!NOTE]
> Det här grundläggande arbetsflödet är samma som att ladda upp en fil när du intag av en tillgång (som diskuterades tidigare i det här avsnittet). Dessutom, som att ladda upp filer, om du (eller dina klienter) behöver komma åt dina filer omedelbart, ställa in ditt StartTime-värde till fem minuter före den aktuella tiden. Den här åtgärden är nödvändig eftersom det kan finnas klocksnedställning mellan klienten och Media Services. StartTime-värdet måste vara i följande DateTime-format: YYYY-MM-DDTHH:mm:ssZ (till exempel "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Skapa en SAS-URL för nedladdning av innehåll
Följande kod visar hur du skaffar en URL som kan användas för att hämta en mediefil som skapats och överförts tidigare. AccessPolicy har läsbehörighetsuppsättning och sökvägen till positioneraren refererar till en SAS-hämtnings-URL.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Egenskapen returnerad **sökväg** innehåller SAS-URL:en.

> [!NOTE]
> Om du hämtar lagringskrypterat innehåll måste du manuellt dekryptera det innan du gör det, eller använda Storage Decryption MediaProcessor i en bearbetningsuppgift för att mata ut bearbetade filer i området rensa till en OutputAsset och sedan hämta från den tillgången. Mer information om bearbetning finns i Skapa ett kodningsjobb med REST-API:et för Medietjänster. Sas URL-positionerare kan inte heller uppdateras när de har skapats. Du kan till exempel inte återanvända samma positionerare med ett uppdaterat StartTime-värde. Detta beror på hur SAS-url:er skapas. Om du vill komma åt en tillgång för nedladdning när en sökare har upphört att gälla måste du skapa en ny med en ny StartTime.
>
>

### <a name="download-files"></a>Hämta filer
När du har angett AccessPolicy och Locator kan du hämta filer med Azure Storage REST API:er.  

> [!NOTE]
> Du måste lägga till filnamnet för filen som du vill hämta till värdet Locator **Path** som tas emot i föregående avsnitt. Till exempel, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Mer information om hur du arbetar med Azure storage blobbar finns i [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Som ett resultat av kodningsjobbet som du utförde tidigare (kodning till Anpassad MP4-uppsättning) har du flera MP4-filer som du kan hämta progressivt. Ett exempel:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Skapa en strömmande URL för direktuppspelning av innehåll
Följande kod visar hur du skapar en url-positionerare för direktuppspelning:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Om du vill strömma en url till ett smidigt strömmande ursprung i en direktuppspelningsmediespelare måste du lägga till egenskapen Path med namnet på manifestfilen Smooth Streaming, följt av "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Om du vill strömma HLS lägger du till (format=m3u8-aapl) efter "/manifestet".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Om du vill strömma MPEG DASH lägger du till (format=mpd-time-csf) efter "/manifestet".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a name="play-your-content"></a><a id="play"></a>Spela upp ditt innehåll
Strömma videon med hjälp av [Azure Media Services Player](https://aka.ms/azuremediaplayer).

Om du vill testa progressiv nedladdning klistrar du in en webbadress i en webbläsare (till exempel IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Nästa steg: sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
