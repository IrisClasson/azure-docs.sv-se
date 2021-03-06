---
title: Hantering av statisk webbplats i Azure Storage
description: Azure Storage statisk webbplats värd, vilket ger en kostnads effektiv och skalbar lösning för moderna webb program.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.custom: devx-track-javascript
ms.openlocfilehash: b8864201fc5bf86a5451c790a51141cee46bffeb
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432507"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hantering av statisk webbplats i Azure Storage

Du kan hantera statiskt innehåll (HTML, CSS, Java Script och bildfiler) direkt från en lagrings behållare med namnet *$Web*. Om du är värd för ditt innehåll i Azure Storage kan du använda serverbaserade arkitekturer som inkluderar [Azure Functions](/azure/azure-functions/functions-overview) och andra PaaS-tjänster (Platform as a Service).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> Om din plats är beroende av kod på Server sidan, använder du [Azure App Service](/azure/app-service/overview) i stället.
Se till att skapa ett standard lagrings konto för generell användning v2. Statiska webbplatser är inte tillgängliga i någon annan typ av lagrings konto.

## <a name="setting-up-a-static-website"></a>Konfigurera en statisk webbplats

En statisk webbplats värd är en funktion som du måste aktivera på lagrings kontot.

Om du vill aktivera statisk webbplats värd väljer du namnet på din standard fil och anger sedan en sökväg till en anpassad 404-sida. Om en Blob Storage-behållare med namnet **$Web** inte redan finns i kontot, skapas en åt dig. Lägg till filerna på platsen i den här behållaren.

Steg-för-steg-anvisningar finns i vara [värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md).

![Mått för Azure Storage statiska webbplatser](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Filer i **$Web** container är Skift läges känsliga, hanteras via begär Anden om anonym åtkomst och är bara tillgängliga via Läs åtgärder.

## <a name="uploading-content"></a>Laddar upp innehåll

Du kan använda något av dessa verktyg för att ladda upp innehåll till **$Web** container:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-tillägg](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visa innehåll

Användare kan visa webbplats innehåll från en webbläsare med hjälp av den offentliga URL: en för webbplatsen. Du kan hitta URL: en med hjälp av Azure Portal, Azure CLI eller PowerShell. Se [hitta webbplats-URL: en](storage-blob-static-website-how-to.md#portal-find-url).

Om servern returnerar ett 404-fel och du inte har angett ett fel dokument när du aktiverade webbplatsen, returneras en standard-404-sida till användaren.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) stöds inte med den statiska webbplatsen.

### <a name="regional-codes"></a>Regionala koder

URL: en för din webbplats innehåller en regional kod. Till exempel innehåller URL: en `https://contosoblobaccount.z22.web.core.windows.net/` regional kod `z22` .

Även om koden måste finnas i URL: en, är den bara för intern användning och du behöver inte använda koden på något annat sätt.

Det index dokument som du anger när du aktiverar statisk webbplats värd, visas när användarna öppnar platsen och inte anger en speciell fil (till exempel: `https://contosoblobaccount.z22.web.core.windows.net` ).

### <a name="secondary-endpoints"></a>Sekundära slut punkter

Om du ställer in [redundans i en sekundär region](../common/storage-redundancy.md#redundancy-in-a-secondary-region)kan du också komma åt webbplats innehåll med hjälp av en sekundär slut punkt. Eftersom data replikeras till sekundära regioner asynkront är de filer som är tillgängliga på den sekundära slut punkten inte alltid synkroniserade med de filer som är tillgängliga på den primära slut punkten.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Effekt av inställningen för webb behållarens offentliga åtkomst nivå

Du kan ändra den offentliga åtkomst nivån för **$Web** containern, men detta har ingen påverkan på den primära statiska webbplats slut punkten eftersom dessa filer behandlas via begär Anden om anonym åtkomst. Det innebär offentlig (skrivskyddad) åtkomst till alla filer.

Följande skärm bild visar inställningen för offentlig åtkomst nivå i Azure Portal:

![Skärm bild som visar hur du ställer in offentlig åtkomst nivå i portalen](./media/anonymous-read-access-configure/configure-public-access-container.png)

Även om den primära statiska webbplats slut punkten inte påverkas, påverkar en ändring av den offentliga åtkomst nivån den primära BLOB-tjänstens slut punkt.

Om du till exempel ändrar den offentliga åtkomst nivån för **$Web** containern från **privat (ingen anonym åtkomst)** till **BLOB (endast anonym Läs åtkomst för blobbar)**, ändras inte nivån för offentlig åtkomst till den primära statiska webbplats slut punkten `https://contosoblobaccount.z22.web.core.windows.net/index.html` .

Den offentliga åtkomsten till den primära BLOB service-slutpunkten `https://contosoblobaccount.blob.core.windows.net/$web/index.html` ändras dock från privat till offentlig. Nu kan användare öppna filen genom att använda någon av dessa två slut punkter.

Att inaktivera offentlig åtkomst på ett lagrings konto påverkar inte statiska webbplatser som finns på lagrings kontot. Mer information finns i [Konfigurera anonym offentlig Läs behörighet för behållare och blobbar](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mappa en anpassad domän till en statisk webbplats-URL

Du kan göra din statiska webbplats tillgänglig via en anpassad domän.

Det är enklare att aktivera HTTP-åtkomst för din anpassade domän eftersom Azure Storage stöder det internt. Om du vill aktivera HTTPS måste du använda Azure CDN eftersom Azure Storage inte har inbyggt stöd för HTTPS med anpassade domäner. Se [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md) för steg-för-steg-anvisningar.

Om lagrings kontot har kon figurer ATS för att [kräva säker överföring](../common/storage-require-secure-transfer.md) över HTTPS måste användarna använda https-slutpunkten.

> [!TIP]
> Överväg att vara värd för din domän på Azure. Mer information finns i vara [värd för din domän i Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Lägger till HTTP-huvuden

Det finns inget sätt att konfigurera rubriker som en del av den statiska webbplats funktionen. Du kan dock använda Azure CDN för att lägga till rubriker och lägga till (eller överskrivna) huvud värden. Se [referens för standard regel motor för Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Om du vill använda huvuden för att styra cachelagring, se [kontroll Azure CDN cachelagring med regler för cachelagring](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Priser

Du kan aktivera statisk webbplats värd utan kostnad. Du faktureras bara för den blob-lagring som webbplatsen använder och drifts kostnader. Mer information om priser för Azure-Blob Storage finns på sidan med [priser för azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mått

Du kan aktivera mått på statiska webbplats sidor. När du har aktiverat mått rapporteras trafik statistik på filer i **$Web** -behållaren på instrument panelen mått.

Om du vill aktivera mått på dina statiska webbplats sidor, se [Aktivera mått på statiska webbplats sidor](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Nästa steg

* [Vara värd för en statisk webbplats i Azure Storage](storage-blob-static-website-how-to.md)
* [Mappa en anpassad domän till en Azure Blob Storage-slutpunkt](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Bygg din första server lös webbapp](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Självstudie: Använda Azure DNS som värd för din domän](../../dns/dns-delegate-domain-azure-dns.md)
