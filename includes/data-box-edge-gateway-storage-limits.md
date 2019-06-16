---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161245"
---
Det här avsnittet beskriver begränsningar för Azure Storage-tjänsten och nödvändiga namnkonventionerna för Azure Files, Azure blockblob-objekt och Azure-sidblobar, som gäller för Data Box Gateway/Data Box Edge-tjänsten. Granska Lagringsgränser noggrant och följer alla rekommendationer.

Gå till den senaste informationen på tjänstbegränsningar för Azure storage och bästa praxis för namngivning av resurser, behållare och filer:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockblob-objekt och konventioner för sidan blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider gränserna för Azure Storage-tjänsten eller inte följer namngivningskonventionerna i Azure-filer/Blob, sedan dessa filer eller kataloger är inte matas in i Azure Storage via tjänsten Data Gateway/Data Box kant.