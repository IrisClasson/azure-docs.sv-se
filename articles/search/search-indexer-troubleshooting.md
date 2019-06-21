---
title: Felsöka vanliga search-indexeraren problem – Azure Search
description: Åtgärda fel och vanliga problem med indexerare i Azure Search, inklusive anslutning till datakälla, brandvägg och saknas dokument.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 4ed18b5f83bdb052f2db6847a320c26a8e49f83e
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147532"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Felsökning av vanliga problem med indexerare i Azure Search

Indexerare kan hamna i ett antal problem vid indexering av data till Azure Search. Huvudkategorier för felet är:

* [Ansluta till en datakälla](#data-source-connection-errors)
* [Dokumentbearbetning](#document-processing-errors)
* [Inmatning av dokumentet till ett index](#index-errors)

## <a name="data-source-connection-errors"></a>Anslutningsfel för data källan

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Brandvägg för Storage-konto

Azure Storage tillhandahåller en konfigurerbar brandvägg. Brandväggen är inaktiverad som standard så att Azure Search kan ansluta till ditt lagringskonto.

Det finns inga specifika meddelanden när en brandvägg har aktiverats. Normalt brandväggen fel ut `The remote server returned an error: (403) Forbidden`.

Du kan kontrollera att brandväggen är aktiverad i den [portal](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Den enda stödda lösningen är att inaktivera brandväggen genom att välja att tillåta åtkomst från [”alla nätverk”](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Om indexeraren inte har en ansluten kompetens du _kan_ försöker [lägga till ett undantag](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) för IP-adresserna för din söktjänst. Det här scenariot stöds inte och är inte säkert att fungera.

Du hittar IP-adressen för din söktjänst genom att skicka pingsignaler dess FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indexering har inte aktiverats

Azure Search är en implicit beroende på Cosmos DB indexering. Om du inaktiverar automatisk indexering i Cosmos DB, Azure Search returnerar tillståndet lyckades, men går inte att index behållarens innehåll. Instruktioner om hur du kontrollerar inställningar och aktivera indexering finns [hantera indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#manage-indexing-using-azure-portal).

## <a name="document-processing-errors"></a>Dokumentet bearbetningsfel

### <a name="unprocessable-or-unsupported-documents"></a>Filer eller ej stödd dokument

Blob-indexeraren [dokument som dokumenterar format stöds uttryckligen.](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Ibland innehåller en blob storage-behållare som inte stöds dokument. Andra gånger kan det vara problematiskt dokument. Du kan undvika stoppar indexeraren på de här dokumenten genom [ändrar konfigurationsalternativ](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Saknas innehållet i dokumentet

Blob-indexeraren [söker efter och extraherar text från blobarna i en behållare](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Vissa problem med att extrahera text är:

* Dokumentet innehåller endast inlästa bilder. PDF-blobar som har textinnehåll, som skannade bilder (jpg) ger inte resultat i en pipeline för fulltextindexering standard blob. Om du har innehållet med textelement kan du använda [kognitiv sökning](cognitive-search-concept-image-scenarios.md) att söka efter och extrahera text.
* Blob-indexeraren är konfigurerad att endast indexets metadata. Om du vill extrahera innehållet, blob-indexeraren måste konfigureras för att [extrahera både innehåll och metadata](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index-fel

### <a name="missing-documents"></a>Saknas dokument

Indexerare hitta dokument från en [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Ibland verkar ett dokument från datakällan som ska indexeras sakna från ett index. Det finns några vanliga orsaker till att dessa fel kan inträffa:

* Dokumentet har inte indexerats. Kontrollera portal för en lyckad indexer-körning.
* Dokumentet har uppdaterats efter indexeraren körs. Om indexeraren finns på en [schema](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), kommer så småningom kör och fortsatte dokumentet.
* Den [fråga](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) anges i data källan utesluter dokumentet. Indexerare inte kan indexera dokument som inte ingår i datakällan.
* [Fältmappningar](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) eller [kognitiv sökning](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) har ändrats dokumentet och det ser annorlunda ut än du förväntar dig.
* Använd den [lookup dokumentet API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) att hitta dokumentet.
