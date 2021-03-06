---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187049"
---
|Parameternamn| Typ | Beskrivning| Möjliga värden|
|-|-|-|-|
| /ServerMode|Obligatorisk|Anger om både konfigurations- och processervrar eller endast processervern ska installeras|CS<br>PS|
|/InstallLocation|Obligatorisk|Den mapp där komponenterna installeras| Vilken mapp på datorn som helst|
|/MySQLCredsFilePath|Obligatorisk|Filsökvägen till platsen där autentiseringsuppgifterna för MySQL-servern lagras|Filen ska vara i det format som anges nedan|
|/VaultCredsFilePath|Obligatorisk|Sökvägen för valvautentiseringsfilen|Giltig sökväg|
|/EnvType|Obligatorisk|Typ av miljö som du vill skydda |VMware<br>NonVMware|
|/PSIP|Obligatorisk|Nätverkskortets IP-adress används för överföring av replikeringsdata| Vilken giltig IP-adress som helst|
|/CSIP|Obligatorisk|Nätverkskortets IP-adress som konfigurationsservern lyssnar på| Vilken giltig IP-adress som helst|
|/PassphraseFilePath|Obligatorisk|Den fullständiga sökvägen till platsen för lösenfrasfilen|Giltig sökväg|
|/BypassProxy|Valfritt|Anger att konfigurationsservern ansluter till Azure utan en proxyserver|För att få det här värdet från Venu|
|/ProxySettingsFilePath|Valfritt|Proxy-inställningar (standardproxy kräver autentisering, eller en anpassad proxy)|Filen ska vara i det format som anges nedan|
|DataTransferSecurePort|Valfritt|Portnumret på PSIP ska användas för replikeringsdata| Giltigt portnummer (standardvärdet är 9433)|
|/SkipSpaceCheck|Valfritt|Hoppa över utrymmeskontroll för cachedisk| |
|/AcceptThirdpartyEULA|Obligatorisk|När du flaggar innebär det att du godkänner licensavtalet från tredje part| |
|/ShowThirdpartyEULA|Valfritt|Visar licensavtalet (EULA) från tredje part. Om detta anges som indata ignoreras alla andra parametrar| |
