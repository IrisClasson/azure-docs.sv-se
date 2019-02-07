---
title: Azure Data Box Gateway begränsar | Microsoft Docs
description: Beskriver system gränser och storlekar som rekommenderas för Microsoft Azure Data Box-Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/15/2019
ms.author: alkohli
ms.openlocfilehash: acf455bff739666712917008dc8090c6a95c6dc4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815660"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Azure Data Box Gateway-begränsningar (förhandsgranskning)


Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box Gateway-lösning. 

> [!IMPORTANT] 
> Data Box Gateway är en förhandsversion. Läs [användningsvillkoren för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen. 


## <a name="data-box-gateway-service-limits"></a>Tjänstbegränsningar för data Box-Gateway

- I den här är tjänsten tillgänglig i vissa regioner i USA, Europa och Asien/Stillahavsområdet. Mer information går du till regiontillgänglighet. Lagringskontot ska vara fysiskt närmast den region där enheten distribuerats (kan skilja sig från service geo).
- Flytta en resurs för Data Box-gatewayen till en annan prenumeration eller resursgrupp grupp stöds inte. Mer information går du till [flytta resurser till ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>Enhetsgränser för data Box-Gateway

I följande tabell beskrivs gränserna för Data Box-Gateway-enheten.

| Beskrivning | Värde |
|---|---|
|Nej. filer per enhet |100 miljoner <br> Gränsen är ~ 25 miljoner filer för varje 2 TB diskutrymme med maxgränsen på 100 miljoner |
|Nej. resurser per enhet |24 |
|Maximal filstorlek som skrivs till en resurs|Maximal filstorlek är 500 GB för virtuella enheter 2 TB. <br> Den maximala filstorleken ökar med datadiskstorleken i föregående förhållandet tills den når högst 5 TB. |

## <a name="azure-storage-limits"></a>Azure storage-begränsningar

Det här avsnittet beskriver begränsningar för Azure Storage-tjänsten och nödvändiga namnkonventionerna för Azure Files, Azure blockblob-objekt och Azure-sidblobar, som gäller för Data Box Gateway/Data Box Edge-tjänsten. Granska Lagringsgränser noggrant och följer alla rekommendationer.

Gå till den senaste informationen på tjänstbegränsningar för Azure storage och bästa praxis för namngivning av resurser, behållare och filer:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockblob-objekt och konventioner för sidan blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider gränserna för Azure Storage-tjänsten eller inte följer namngivningskonventionerna i Azure-filer/Blob, sedan dessa filer eller kataloger är inte matas in i Azure Storage via tjänsten Data Gateway/Data Box kant.

## <a name="data-upload-caveats"></a>Överföra data varningar

Följande villkor gäller för data som flyttas till Azure.

- Vi rekommenderar att mer än en enhet inte bör skriva till samma behållare.
- Om du har ett befintligt Azure objekt (till exempel en blob eller en fil) i molnet med samma namn som det objekt som ska kopieras skrivs enhet till filen i molnet.
- En tom katalog-hierarki (utan några filer) som skapats under dela mappar inte laddas upp till blob-behållare.
- Om du kopierar filer som är större än storleken som enheten, rekommenderar vi för att använda *Robocopy* eller *rsync* så det inte finns några fel.

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure-konto storlek och objektet storleksgränser för storage

Här finns gränserna på mängden data som kopieras till storage-konto. Kontrollera att datan överensstämmer med de här gränserna. Gå till den senaste informationen om dessa begränsningar [prestandamål i Azure blob storage skala](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) och [Azure Files skala mål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Storleken på data som kopieras till Azure storage-konto                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Sidan och block Blob-blob                                            | 500 TB per lagringskonto|


## <a name="azure-object-size-limits"></a>Storleksgränser för Azure-objekt

Här följer storlekarna på de Azure-objekt som kan skrivas. Se till att alla filer som har laddats upp överensstämmer med de här gränserna.

| Azure objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 8 TB                                                 |
| Sidblob         | 1 TB <br> Varje fil som laddats upp Sidblobb format måste vara justerad 512 byte (en integrerad flera), annars överföringen misslyckas. <br> VHD- och VHDX är 512 byte justerad. |


## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
