---
title: Sök över Azure Data Lake Storage Gen2 (för hands version)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar innehåll och metadata i Azure Data Lake Storage Gen2. Den här funktionen är för närvarande en offentlig för hands version
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 5862654dac99bea7755eab41cf3c1bd6cdd84526
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760319"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexera dokument i Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2-support finns för närvarande i en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Du kan begära åtkomst till för hands versionerna genom att fylla i [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Den [REST API version 2020-06-30-Preview](search-api-preview.md) och Portal innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.


När du konfigurerar ett Azure Storage-konto har du möjlighet att aktivera [hierarkiskt namn område](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Detta gör att samlingen av innehåll i ett konto kan organiseras i en hierarki med kataloger och kapslade under kataloger. Genom att aktivera hierarkiskt namn område aktiverar du [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Den här artikeln beskriver hur du kommer igång med att indexera dokument i Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Konfigurera Azure Data Lake Storage Gen2 indexerare

Det finns några steg som du måste utföra för att indexera innehåll från Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Steg 1: registrera dig för för hands versionen

Registrera dig för för hands versionen av Data Lake Storage Gen2 indexerare genom att fylla i [det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Du får ett bekräftelse meddelande när du har accepterat i för hands versionen.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Steg 2: följa installations stegen för Azure Blob Storage-indexering

När du har fått en bekräftelse på att din registrering av förhands granskning lyckades, är du redo att skapa indexerings pipelinen.

Du kan indexera innehåll och metadata från Data Lake Storage Gen2 med [REST API version 2020-06-30 – för hands versionen](search-api-preview.md) eller portalen. Det finns för närvarande inget stöd för .NET SDK.

Indexering av innehåll i Data Lake Storage Gen2 är identiskt med indexering av innehåll i Azure Blob Storage. För att förstå hur du konfigurerar Data Lake Storage Gen2 data källa, index och indexerare, se [hur du indexerar dokument i azure Blob Storage med azure kognitiv sökning](search-howto-indexing-azure-blob-storage.md). Blob Storage-artikeln innehåller också information om vilka dokument format som stöds, vilka egenskaper för BLOB-metadata som extraheras, stegvis indexering med mera. Den här informationen är samma för Data Lake Storage Gen2.

## <a name="access-control"></a>Åtkomstkontroll

Azure Data Lake Storage Gen2 implementerar en [modell för åtkomst kontroll](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) som stöder både Azure-rollbaserad åtkomst kontroll (Azure RBAC) och POSIX-liknande åtkomst kontrol listor (ACL: er). När du indexerar innehåll från Data Lake Storage Gen2 kommer Azure Kognitiv sökning inte att extrahera RBAC-och ACL-information från innehållet. Detta innebär att den här informationen inte ingår i ditt Azure Kognitiv sökning-index.

Om det är viktigt att ha åtkomst kontroll på varje dokument i indexet är det upp till programutvecklaren att implementera [säkerhets trimning](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Ändrings identifiering

Data Lake Storage Gen2 indexeraren stöder ändrings identifiering. Det innebär att när indexeraren kör så indexeras bara de ändrade blobbar som fastställs av blobens `LastModified` tidsstämpel.

> [!NOTE] 
> Data Lake Storage Gen2 tillåter att kataloger byter namn. När du byter namn på en katalog får tidsstämplar för blobarna i katalogen inte uppdaterats. Det innebär att indexeraren inte Omindexerar dessa blobbar. Om du behöver blobarna i en katalog som ska indexeras om efter en katalog namns ändring eftersom de nu har nya URL: er, måste du uppdatera `LastModified` tidsstämpeln för alla blobbar i katalogen så att indexeraren vet att de kan indexera om dem under en framtida körning.
