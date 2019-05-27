---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165458"
---
1. I Klusterhanteraren Expandera **roller**, och sedan markera din tillgänglighetsgruppen.  

2. På den **resurser** fliken, högerklicka på namnet på lyssnare och klicka sedan på **egenskaper**.

3. Klicka på den **beroenden** fliken. Om flera resurser visas, kontrollerar du att IP-adresser har eller inte och beroenden.  

4. Klicka på **OK**.

5. Högerklicka på namnet på lyssnare och klicka sedan på **Anslut**.

6. När lyssnaren är online på den **resurser** fliken, högerklicka på tillgänglighetsgruppen och klicka sedan på **egenskaper**.
   
    ![Konfigurera tillgänglighetsgruppresursen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Skapa ett beroende på lyssnaren namn resursen (inte IP-adress resurser namnet) och klicka sedan på **OK**.
   
    ![Lägg till beroende på namnet på lyssnare](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Starta SQL Server Management Studio och Anslut till den primära repliken.

9. Gå till **AlwaysOn hög tillgänglighet** > **Tillgänglighetsgrupper** > **\<AvailabilityGroupName\>**   >  **Tillgänglighetsgruppens lyssnare**.  
    Namnet på lyssnare som du skapade i hanteraren för redundanskluster ska visas.

10. Högerklicka på namnet på lyssnare och klicka sedan på **egenskaper**.

11. I den **Port** skriver du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort som du använde tidigare (1433 var standard i den här självstudien), och klicka sedan på **OK**.

