---
title: Introduktion till Azure-köer – Azure Storage
description: Se en introduktion till Azure-köer, en tjänst för att lagra ett stort antal meddelanden. En Queue Service innehåller ett URL-format, lagrings konto, kö och meddelande.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: c16f02922b2e815ddd4986ab07bf260b20d871ee
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826508"
---
# <a name="what-are-azure-queues"></a>Vad är Azure-köer?

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

## <a name="queue-service-concepts"></a>Kötjänst begrepp

Kötjänsten består av följande komponenter:

![Diagram över relationen mellan ett lagrings konto, köer och meddelanden](./media/storage-queues-introduction/queue1.png)

* **URL-format:** köer är adresserbara via följande URL-format:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Följande URL adresserar en kö i diagrammet:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Lagrings konto:** All åtkomst till Azure Storage görs via ett lagrings konto. Information om kapacitet för lagrings konton finns i [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Kö:** en kö innehåller en uppsättning meddelanden. Könamnet **får** bara innehålla gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Meddelande:** ett meddelande i valfritt format, som är upp till 64 KB. Före version 2017-07-29 är den högsta tillåtna tiden till Live sju dagar. För version 2017-07-29 eller senare kan den maximala tiden till Live vara ett positivt tal eller-1 som anger att meddelandet inte upphör att gälla. Om den här parametern utelämnas, är standardvärdet för Time-to-Live sju dagar.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett lagringskonto](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Komma igång med köer med hjälp av .NET](storage-dotnet-how-to-use-queues.md)
