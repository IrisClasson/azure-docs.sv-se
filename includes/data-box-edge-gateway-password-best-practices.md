---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 86d1cf5e103bcbb13782aa7a2a84092aa426d670
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120582"
---
Tänk på dessa metodtips:

- Vi rekommenderar att du lagrar alla lösenord på en säker plats så att du inte behöver återställa ett lösenord om det glöms bort. Management-tjänsten kan inte hämta befintliga lösenord. Det kan bara återställa dem via Azure portal. Om du återställer ett lösenord, måste du meddela alla användare innan du återställa den.
- Du kan komma åt Windows PowerShell-gränssnittet på enheten via en fjärranslutning via HTTP. Som en säkerhetsåtgärd bör du använda HTTP betrodda nätverk.
- Kontrollera att enhetens lösenord är stark och väl skyddade. Följ den [Metodtips för lösenord](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).