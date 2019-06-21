---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187291"
---
Sedan om alla servrar i klustret kör Windows Server 2008 R2 eller Windows Server 2012 måste du kontrollera att snabbkorrigeringen [KB2854082](https://support.microsoft.com/kb/2854082) installeras på var och en av lokala servrar eller virtuella Azure-datorer som ingår i klustret. En server eller virtuell dator som är i klustret, men inte i tillgänglighetsgruppen, bör du även ha den här snabbkorrigeringen installeras.

Ladda ned i fjärrskrivbordssessionen för var och en av noderna i klustret, [KB2854082](https://support.microsoft.com/kb/2854082) till en lokal katalog. Sedan kan installera snabbkorrigeringen på varje klusternod sekventiellt. Om klustertjänsten körs på noden i klustret, startas servern om i slutet av installationen av snabbkorrigeringen.

> [!WARNING]
> Stoppar klustertjänsten eller startar om servern påverkar kvorum hälsotillståndet för ditt kluster och tillgänglighetsgruppen och det kan innebära att frånkopplas. För att bibehålla den höga tillgängligheten för ditt kluster under installationen, se till att:
> 
> * Klustret är i optimala kvorum health. 
> * Innan du installerar snabbkorrigeringen på varje nod måste är alla noder i klustret online.
> * Innan du installerar snabbkorrigeringen på en annan nod i klustret, kan installationen av snabbkorrigeringen att köra kan slutföras på en nod, inklusive fullständigt omstart av servern.
> 
> 

