---
title: Lista över alla dina Azure Import/Export-jobb | MicrosoftDocs
description: Lär dig mer om att lista alla jobb för Azure Import/Export-tjänsten i en prenumeration.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478699"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Räkna upp jobb i tjänsten Azure Import/Export
Om du vill räkna upp alla jobb i en prenumeration, anropa den [lista jobb](/rest/api/storageimportexport/jobs) igen. `List Jobs` Returnerar en lista över jobb samt följande attribut:

-   Vilken typ av jobb (Import eller Export)

-   Jobbets aktuella tillstånd

-   Jobbet Tjänstadministratörens storage-konto

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
