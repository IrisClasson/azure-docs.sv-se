---
title: Azure Data Box-Disk begränsar | Microsoft Docs
description: Beskriver system gränser och storlekar som rekommenderas för Microsoft Azure Data Box-Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 6a7f7943e9d567a953c0e21697dfe4fdedd6e8f0
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744797"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box-Disk-gränser


Överväg att dessa gränser som du distribuerar och använder din Microsoft Azure Data Box-Disk-lösning. 

## <a name="data-box-service-limits"></a>Data Box-tjänstbegränsningar

 - Data Box-tjänsten finns bara i USA, EU, Kanada och Australien i alla Azure-regioner för offentliga Azure-molnet.
 - Ett enda lagringskonto stöds med Data Box-Disk.

## <a name="data-box-disk-performance"></a>Prestanda för data Box-Disk

Vid tester med diskar ansluta via USB 3.0 var diskprestandan upp till 430 MB/s. De faktiska hastigheterna varierar beroende på filstorleken. För mindre filer kan du se lägre prestanda.

## <a name="azure-storage-limits"></a>Azure storage-begränsningar

Det här avsnittet beskriver begränsningar för Azure Storage-tjänsten och de nödvändiga namngivningskonventionerna för Azure Files, Azure blockblob-objekt och Azure-sidblobar, som gäller för Data Box-tjänsten. Granska Lagringsgränser noggrant och följer alla rekommendationer.

Gå till den senaste informationen på tjänstbegränsningar för Azure storage och bästa praxis för namngivning av resurser, behållare och filer:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockblob-objekt och konventioner för sidan blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider gränserna för Azure Storage-tjänsten eller inte följer namngivningskonventionerna i Azure-filer/Blob, sedan dessa filer eller kataloger är inte matas in i Azure Storage via Data Box-tjänsten.

## <a name="data-upload-caveats"></a>Överföra data varningar

- Kopiera inte data direkt till diskarna. Kopiera data till färdiga *BlockBlob* och *PageBlob* mappar.
- En mapp under den *BlockBlob* och *PageBlob* är en behållare. Exempelvis behållare skapas som *BlockBlob/behållare* och *PageBlob/behållare*.
- Om du har ett befintligt Azure objekt (till exempel en blob) i molnet med samma namn som det objekt som ska kopieras skrivs Data Box-Disk till filen i molnet.
- Alla filer som skrivits till *BlockBlob* och *PageBlob* resurser överförs som en blockblob och sidblob respektive.
- Eventuella tomma Kataloghierarki (utan några filer) som skapats under *BlockBlob* och *PageBlob* mappar inte laddas.
- Om det finns några fel när du överför data till Azure, skapas en fellogg i mållagringskontot. Sökvägen till den här loggen är tillgänglig i portalen när överföringen är klar och du kan granska loggen för att vidta åtgärder. Ta inte bort data från källan utan att verifiera överförda data.

## <a name="azure-storage-account-size-limits"></a>Storleksgränser för Azure storage-konto

Här finns gränserna på mängden data som kopieras till storage-konto. Kontrollera att datan överensstämmer med de här gränserna. Gå till den senaste informationen om dessa begränsningar [prestandamål i Azure blob storage skala](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) och [Azure Files skala mål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Storleken på data som kopieras till Azure storage-konto                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Sidan och block Blob-blob                                            | 500 TB per lagringskonto. <br> Detta inkluderar data från alla källor, inklusive Data Box-Disk.|


## <a name="azure-object-size-limits"></a>Storleksgränser för Azure-objekt

Här följer storlekarna på de Azure-objekt som kan skrivas. Se till att alla filer som har laddats upp överensstämmer med de här gränserna.

| Azure objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> (Alla filer som överförts i sidans Blob-format måste vara justerad 512 byte (en integrerad flera), annars överföringen misslyckas. <br> VHD- och VHDX är 512 byte justerad.) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Azure blockblob och page blob namngivningskonventioner

| Entitet                                       | Konventioner                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Namn på behållare för blockblob och page blob | Måste vara ett giltigt DNS-namn som är 3 till 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Kan innehålla endast gemener, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Flera bindestreck i rad tillåts inte i namn. |
| Blobnamn för blockblobar och sidblobar      | Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. <br> Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. <br> Reserverade URL-tecken måste undantas korrekt. <br>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog. |


## <a name="next-steps"></a>Nästa steg
* Granska [Data Box-systemkrav](data-box-system-requirements.md)
