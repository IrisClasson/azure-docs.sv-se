---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187286"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Skapa en TCP-slutpunkt för den virtuella datorn
För att kunna ansluta till SQL Server via Internet måste den virtuella datorn ha en slutpunkt för att lyssna på inkommande TCP-kommunikation. I det här Azure-konfigurationssteget dirigeras inkommande trafik via TCP-porten till en TCP-port som är tillgänglig på den virtuella datorn.

> [!NOTE]
> Om du ansluter i samma moln tjänst eller virtuella nätverk behöver du inte skapa en offentligt tillgänglig slut punkt. I så fall kan du fortsätta till nästa steg. Mer information finns i [Connection Scenarios](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios) (Anslutningsscenarier).
> 
> 

1. Välj **Virtuella datorer (klassiska)** i Azure Portal.
2. Välj sedan den virtuella SQL Server-datorn.
3. Välj **Slutpunkter** och klicka sedan på **Lägg till** längst upp på bladet Slutpunkter.
   
    ![Steg för att skapa en slutpunkt i Azure Portal](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. Ange ett **namn**, till exempel SQLSlutpunkt på bladet **Lägg till slutpunkt**.
5. Välj **TCP** i **Protokoll**.
6. För **Offentlig port** anger du ett portnummer, till exempel **57500**.
7. För **Privat port** anger du en lyssningsport för SQL Server. Standardinställningen är **1433**.
8. Klicka på **OK** för att skapa slutpunkten.

