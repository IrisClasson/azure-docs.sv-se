---
title: Vad du gör i händelse av en Azure-tjänsten avbrott som påverkar Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Lär dig vad du gör i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: barclayn
ms.openlocfilehash: dba1fe91a635f467f4a3aeeaa048897065822869
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236644"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault tillgänglighet och redundans

Azure Key Vault har flera lager för redundans för att se till att dina nycklar och hemligheter finnas tillgängliga för ditt program även om enskilda komponenter i tjänsten misslyckas.

Innehållet i ditt nyckelvalv replikeras inom regionen och till en sekundär region minst 150 mil bort men inom samma geografiska område. Detta upprätthåller hög hållbarhet för dina nycklar och hemligheter. Se den [parade Azure-regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dokumentet för information om specifika regionpar.

Om enskilda komponenter i key vault-tjänsten misslyckas, steg alternativa komponenter för regionen att hantera din förfrågan om att se till att det finns inga försämring av funktioner. Du behöver inte vidta några åtgärder som utlöser. Det sker automatiskt och blir transparent till dig.

Om att en hel Azure-region inte är tillgänglig, som du gör för Azure Key Vault i den regionen begäranden dirigeras automatiskt (*redundansväxlats*) till en sekundär region. När den primära regionen är tillgänglig igen, begäranden dirigeras tillbaka (*växlas tillbaka*) till den primära regionen. Igen, behöver du inte vidta någon åtgärd eftersom detta sker automatiskt.

Via den här designen för hög tillgänglighet kräver inget avbrott i Azure Key Vault för underhållsaktiviteter.

Det finns några varningar för att vara medveten om:

* Det kan ta några minuter för tjänsten att växla över i händelse av en region redundans. Begäranden som görs under den här tiden kan misslyckas tills redundansväxlingen är klar.
* När en redundansväxling har slutförts, är ditt nyckelvalv i skrivskyddat läge. Begäranden som stöds i det här läget är:
  * Lista nyckelvalv
  * Hämta egenskaper för nyckelvalv
  * En lista över hemligheterna
  * Hämta hemligheter
  * Lista över nycklar
  * Hämta nycklar (Egenskaper)
  * Kryptera
  * Avkryptera
  * Omsluta
  * Packa upp
  * Verifiera
  * Inloggning
  * Backup
* Efter en redundansväxling växlas tillbaka, alla typer av begäranden (inklusive Läs *och* skrivförfrågningar) är tillgängliga.

