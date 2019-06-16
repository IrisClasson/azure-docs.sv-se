---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159790"
---
## <a name="what-is-queue-storage"></a>Vad är Queue Storage?
Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

Vanliga användningsområden för Queue Storage är:

* Skapa en lista med kvarvarande uppgifter att bearbeta asynkront
* Skicka meddelanden från en Azure-webbroll till en Azure-arbetsroll

## <a name="queue-service-concepts"></a>Kötjänst-koncept
Kötjänsten består av följande komponenter:

![Kö1](./media/storage-queue-concepts-include/queue1.png)

* **URL-format:** Köer är adresserbara via följande URL-format:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Följande URL adresserar en kö i diagrammet:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/common/storage-scalability-targets.md) för information om kapacitet för lagringskonton.
* **Kö:** En kö innehåller en uppsättning meddelanden. Alla meddelanden måste vara i en kö. Observera att könamnet måste vara helt i gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **meddelande:** Ett meddelande i valfritt format, upp till 64 KB. Den maximala tid som ett meddelande kan finnas i kön är 7 dagar.

