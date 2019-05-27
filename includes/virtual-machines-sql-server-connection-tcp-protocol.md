---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165552"
---
1. Sök efter **Configuration Manager** medan du fortfarande är fjärrskrivbordsansluten till den virtuella datorn:

    ![Öppna SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Expandera **SQL Server-nätverkskonfiguration** i konsolfönstret i Konfigurationshanteraren för SQL Server.

1. Klicka på **Protokoll för MSSQLSERVER** (standardinstansnamnet) i konsolfönstret. Högerklicka på **TCP** i informationsfönstret och klicka på **Aktivera** om det inte redan har aktiverats.

    ![Aktivera TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klicka på **SQL Server-tjänster** i konsolfönstret. I informationsfönstret högerklickar du på **SQL Server (*instansnamn*)** (standardinstansen är **SQL Server (MSSQLSERVER)**), och klicka sedan på **starta om**, för att stoppa och starta om SQL Server-instansen.

    ![Starta om databasmotorn](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Stäng Konfigurationshanteraren för SQL Server.

Mer information om hur du aktiverar protokoll för SQL Server-databasmotorn finns i [Enable or Disable a Server Network Protocol](https://msdn.microsoft.com/library/ms191294.aspx) (Aktivera eller inaktivera ett servernätverksprotokoll).
