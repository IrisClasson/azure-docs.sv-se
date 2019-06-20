---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187288"
---
I det här steget ska testa du tillgänglighetsgruppens lyssnare med hjälp av ett klientprogram som körs på samma nätverk.

Klientanslutning har följande krav:

* Klientanslutningar till lyssnaren måste komma från datorer som finns i en annan molntjänst än det som är värd för tillgänglighetsrepliker alltid på.
* Om Always On-repliker är i olika undernät, måste klienter ange *MultisubnetFailover = True* i anslutningssträngen. Det här tillståndet resulterar i parallella anslutningsförsök till kopior i olika undernät. Det här scenariot innehåller en interregionala Always On availability gruppdistribution.

Ett exempel är att ansluta till lyssnaren från någon av de virtuella datorerna i samma Azure-nätverk (men inte en som är värd för en replik). Ett enkelt sätt att slutföra det här testet är att försöka ansluta SQL Server Management Studio till tillgänglighetsgruppens lyssnare. En annan enkel metod är att köra [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), enligt följande:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Om värdet EndpointPort är *1433*, du behöver inte ange den i anropet. Det föregående anropet förutsätter också att klientdatorn är ansluten till samma domän och att anroparen har beviljats behörigheter för databasen med hjälp av Windows-autentisering.
> 
> 

När du testar lyssnaren Glöm inte att redundansväxla tillgänglighetsgruppen att se till att klienter kan ansluta till lyssnaren för redundans.

