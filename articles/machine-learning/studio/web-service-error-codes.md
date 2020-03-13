---
title: Fel koder för REST API – Azure Machine Learning Studio (klassisk) | Microsoft Docs
description: Dessa felkoder kan returneras av en åtgärd på en Azure Machine Learning-webbtjänst.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217758"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassisk) REST API felkoder

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Följande felkoder kan returneras av en åtgärd på en Azure Machine Learning Studio (klassisk)-webb tjänst.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-statuskod 400)
 
Ogiltigt argument har angetts.
 
Den här klassen av fel innebär ett argument som tillhandahålls någonstans var ogiltigt. Detta kan vara en autentiseringsuppgift eller platsen för Azure storage att något skickas till webbtjänsten. Se fel fältet "kod" i avsnittet "information" för att diagnostisera vilket särskilt argument som är ogiltigt.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| BadParameterValue | Det angivna parametervärdet uppfyller inte parametern-regeln på parametern |
| BadSubscriptionId | Prenumerations-Id som används för att bedöma är inte som finns i resursen |
| BadVersionCall | En ogiltig versions parameter skickades under API-anropet: {0}. Kontrollera på API-hjälpsidan för att skicka rätt version och försök igen. |
| BatchJobInputsNotSpecified | Följande obligatoriska indatatyper har inte angetts med begäran: {0}. Kontrollera att alla indata har angetts och försök igen. |
| BatchJobInputsTooManySpecified | Förfrågan angav mer indata än vad som definierats i tjänsten. Lista över godkända indatatyper: {0}. Kontrollera att alla indata har angetts korrekt och försök igen. |
| BlobNameTooLong | Azure Blob Storage-sökvägen som tillhandahölls för diagnostiska utdata är för lång: {0}. Korta ned sökvägen och försök igen. |
| BlobNotFound | Det gick inte att komma åt den tillhandahållna Azure Blob-{0}.  Azure-fel meddelande: {1}. |
| ContainerIsEmpty | Inga Azure storage-behållarens namn angavs. Ange ett giltigt behållarnamn och försök igen. |
| ContainerSegmentInvalid | Ogiltigt behållarnamn. Ange ett giltigt behållarnamn och försök igen. |
| ContainerValidationFailed | Det gick inte att verifiera BLOB-behållaren. fel meddelande: {0}. |
| DataTypeNotSupported | Typ av data som inte stöds har angetts. Ange giltiga data typer och försök igen. |
| DuplicateInputInBatchCall | Batchförfrågan är ogiltig. Det går inte att ange både enkla och flera indata på samma gång. Ta bort en av de här objekten från begäran och försök igen. |
| ExpiryTimeInThePast | Den angivna förfallo tiden är i det förflutna: {0}. Ange ett framtida Utgångsdatum tid i UTC och försök igen. För att aldrig upphöra, anger förfallotiden som NULL. |
| IncompleteSettings | Inställningarna för startdiagnostik är ofullständiga. |
| InputBlobRelativeLocationInvalid | Inga Azure storage blob namn har tillhandahållits. Ange ett giltigt blobbnamn och försök igen. |
| InvalidBlob | Ogiltig BLOB-specifikation för BLOB: {0}. Kontrollera att anslutningssträngen / relativ sökväg eller SAS-token specifikationen är korrekt och försök igen. |
| InvalidBlobConnectionString | Anslutnings strängen som angetts för en av blobarna för indata/utdata är ogiltig: {0}. Åtgärda detta och försök igen. |
| InvalidBlobExtension | BLOB-referensen: {0} har ett fil namns tillägg som är ogiltigt eller saknas. Fil namns tillägg som stöds för den här utdatatypen är: "{1}". |
| InvalidInputNames | Ett eller flera ogiltiga tjänst indatatyper har angetts i begäran: {0}. Mappa inkommande data till rätt tjänst indata och försök igen. |
| InvalidOutputOverrideName | Ogiltigt namn på åsidosättning av utdata: {0}. Tjänsten har inte en utdata-nod med det här namnet. Ange ett korrekt utdata nodnamn åsidosätta (skiftlägeskänslighet gäller). |
| InvalidQueryParameter | Ogiltig frågeparameter{0}. {1} |
| MissingInputBlobInformation | Azure storage blob-information som saknas. Ange en giltig anslutningssträng och relativ sökväg eller URI: N och försök igen. |
| MissingJobId | Inget jobb-Id har angetts. Ett jobb Id returneras när ett jobb har skickats för första gången. Kontrollera jobb-Id är korrekt och försök igen. |
| MissingKeys | Inga nycklar har angetts eller primär eller sekundärnyckel har inte angetts. |
| MissingModelPackage | Inga modellen paket-Id eller paket i modellen. Ange en giltig modell paket-Id eller modellera paket och försök igen. |
| MissingOutputOverrideSpecification | Begäran saknar BLOB-specifikationen för åsidosättning av utdata {0}. Ange en giltig blobbplats med förfrågan eller ta bort specifikationen utdata om Ingen åsidosättning plats är det önskade. |
| MissingRequestInput | Webbtjänsten förväntar sig indata, men inga indata har angetts. Kontrollera att giltiga inmatningar tillhandahålls baserat på de publicerade indataportar i modellen och försök igen. |
| MissingRequiredGlobalParameters | Inte alla nödvändiga web service-parametrar som tillhandahålls. Kontrollera parametrarna som förväntas för modulen eller modulerna är korrekta och försök igen. |
| MissingRequiredOutputOverrides | När du anropar en krypterad service-slutpunkt som det är obligatoriskt att skicka in åsidosättningar utdata för alla service utdata. Åsidosättningar som saknas för den här tiden för dessa utdata: {0} |
| MissingWebServiceGroupId | Ingen web service-grupp-Id har angetts. Ange en giltig web service grupp-Id och försök igen. |
| MissingWebServiceId | Inga webbtjänst-Id har angetts. Ange en giltig webbtjänst Id och försök igen. |
| MissingWebServicePackage | Inga web tjänstpaket som tillhandahålls. Ange en giltig web service-paketet och försök igen. |
| MissingWorkspaceId | Ingen arbetsyta-Id har angetts. Ange en giltig arbetsyta Id och försök igen. |
| ModelConfigurationInvalid | Ogiltig modellkonfiguration i paket för modellen. Kontrollera konfigurationen modellen innehåller slutpunkt(er) utdatadefinitionen, std fel slutpunkt och std ut slutpunkten och försök igen. |
| ModelPackageIdInvalid | Ogiltigt ID för modell paket. kontrol lera att modell paketets ID är korrekt och försök igen. |
| RequestBodyInvalid | Inga begärandetexten tillhandahålls eller fel vid deserialisering av begärandetexten. |
| RequestIsEmpty | Ingen förfrågan. Ange en giltig förfrågan och försök igen. |
| UnexpectedParameter | Oväntat parametrarna som anges. Kontrollera alla parameternamn är rättstavat, endast förväntade parametrarna har skickats och försök igen. |
| UnknownError | Okänt fel. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Det går inte att ändra kraven för samtidiga begär Anden för {0} webb tjänsten. |
| WebServiceIdInvalid | Ogiltig webbtjänst-id som angetts. Webbtjänst-id som ska vara ett giltigt guid. |
| WebServiceTooManyConcurrentRequestRequirement | Det går inte att ange krav för samtidiga begär anden till mer än {0}. |
| WebServiceTypeInvalid | Ogiltig web service typ har angetts. Kontrollera att giltiga web service-typen stämmer och försök igen. Giltiga webb tjänst typer: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-statuskod 400)
 
Ogiltig användare gruppargument har angetts.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| InputMismatchError | Indata matchar inte schemat för indataport. |
| InputParseError | Parsning av inkommande vektor misslyckades.  Kontrollera inkommande vektor har rätt antal kolumner och datatyper.  Ytterligare information: {0}. |
| MissingRequiredGlobalParameters | Parametrar som förväntas av webbtjänsten saknas. Kontrollera de obligatoriska parametrarna som förväntas av webbtjänsten är korrekta och försök igen. |
| UnexpectedParameter | Kontrollera endast de obligatoriska parametrarna som förväntas av webbtjänsten har skickats och försök igen. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-statuskod 400)
 
Begäran är ogiltig i den aktuella kontexten.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| CannotStartJob | Det går inte att starta jobbet eftersom det är i {0}s tillstånd. |
| IncompatibleModel | Modellen är inte kompatibel med version för begäran. Version för begäran stöder endast enskild datatable utdata modeller. |
| MultipleInputsNotAllowed | Modellen tillåter inte flera inmatningar. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-statuskod 400)
 
Ett internt bibliotek-fel inträffade i modulen körning.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-statuskod 400)
 
Ett fel inträffade i modulen körning.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-statuskod 400)
 
Ogiltig web service-paketet. Kontrollera web service-paketet som angetts är korrekt och försök igen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| FormatError | Web service-paketet har fel format. Information: {0} |
| RuntimesError | Web service-paketet diagrammet är ogiltig. Information: {0} |
| ValidationError | Web service-paketet diagrammet är ogiltig. Information: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Obehörig (HTTP-statuskod 401)
 
Begäran har inte behörighet att åtkomst till resursen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| AdminRequestUnauthorized | Behörighet saknas |
| ManagementRequestUnauthorized | Behörighet saknas |
| ScoreRequestUnauthorized | Ogiltiga autentiseringsuppgifter. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-statuskod 404)
 
Det gick inte att hitta resursen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| ModelPackageNotFound | Modeller paket hittades inte. Kontrollera modell paket-Id är korrekt och försök igen. |
| WebServiceIdNotFoundInWorkspace | Webbtjänsten under den här arbetsytan hittades inte. Det finns ett matchningsfel mellan webServiceId och workspaceId. Kontrollera den webbtjänst som angetts är en del av arbetsytan och försök igen. |
| WebServiceNotFound | Web service hittades inte. Kontrollera webbtjänsten Id är korrekt och försök igen. |
| WorkspaceNotFound | Arbetsytan hittades inte. Kontrollera arbetsytan Id är korrekt och försök igen. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-statuskod 408)
 
Åtgärden kunde inte slutföras under den tilldelade tiden.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| RequestCanceled | Begäran avbröts av klienten. |
| ScoreRequestTimeout | Körningsbegäran tidsgränsen. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (HTTP-statuskod 409)
 
Resursen finns redan.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Ogiltiga utdata parameternamn. Försök med modulen metadata Redigeraren för att byta namn på kolumner och försök igen. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-statuskod 413)
 
Modellen hade överskred minneskvoten tilldelade till den.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| OutOfMemoryLimit | Modellen används mer minne än vad som var används för den. Det högsta tillåtna minnet för modellen är {0} MB. Kontrollera din modell för problem. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-statuskod 500)
 
Ett internt fel inträffade vid körning.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Behållare-processen som kraschat på grund av systemfel |
| ContainerProcessTerminatedWithUnknownError | Behållare-processen som kraschat med okänt fel |
| ContainerValidationFailed | Det gick inte att verifiera BLOB-behållaren. fel meddelande: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Inga argument har angetts. Kontrollera att giltiga argument har skickats och försök igen. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port-ID ={0} har en datatyp som inte stöds: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger-genereringen misslyckades, information: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Okänd jobb status kod {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, information: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-statuskod 500)
 
Ett internt fel inträffade vid körning. System lite minne. Försök igen.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-statuskod 500)
 
Ogiltig modell-paketet. Kontrollera modell pakethanterare är korrekt och försök igen.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-statuskod 500)
 
Ogiltig web service-paketet. Kontrollera webbpaket som angetts är korrekt och försök igen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| ModuleError | Web service-paketet diagrammet är ogiltig. Information: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-statuskod 503)
 
Begäran kan inte utföra som behållarna initieras.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-statuskod 503)
 
Tjänsten är otillgänglig.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| NoMoreResources | Inga resurser som är tillgängliga för begäran. |
| RequestThrottled | Begäran begränsades för {0} slut punkten. Den maximala samtidigheten för slut punkten är {1}. |
| TooManyConcurrentRequests | För många samtidiga begäranden skickas. |
| TooManyHostsBeingInitialized | För många värdar håller på att initieras på samma gång. Överväg att begränsning / försöker igen. |
| TooManyHostsBeingInitializedPerModel | För många värdar håller på att initieras på samma gång. Överväg att begränsning / försöker igen. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-statuskoden 504)
 
Åtgärden kunde inte slutföras under den tilldelade tiden.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| BackendInitializationTimeout | Initiera för web-tjänsten kunde inte slutföras inom den tilldelade tiden. |
| BackendScoreTimeout | Frågekörningen för web-tjänsten kunde inte slutföras inom den tilldelade tiden. |
 
