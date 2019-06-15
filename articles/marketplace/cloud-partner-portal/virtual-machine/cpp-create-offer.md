---
title: Skapa erbjudande för virtuell dator i Azure Marketplace
description: Visar de steg som krävs för att skapa en ny virtuell dator (VM) erbjuder för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 4cd635c6f664a5260b79e62ea72bbb86fc4e1e4f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938364"
---
# <a name="create-virtual-machine-offer"></a>Skapa erbjudande för virtuell dator

Det här avsnittet visas de steg som krävs för att skapa en ny virtuell dator (VM) erbjudandet begäran för Azure Marketplace.  Varje erbjudande visas som en egen enhet på Azure Marketplace och är associerad med en eller flera SKU: er.  Erbjudande om en virtuell dator består av följande grupper i tillgångar och stödtjänster: 

![Tillgångar till en virtuell dator](./media/publishvm_002.png)

Där:

|  **Grupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKUs            |  Den minsta köpbara enheten i ett erbjudande. Ett erbjudande (produkten klassen) kan ha flera SKU: er som är associerade med den att skilja mellan funktioner som stöds och VM-avbildningstyper faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsföring, juridiska och inverka management tillgångar och specifikationer.  <ul><li> Marknadsföring-material inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar är en sekretesspolicy, användningsvillkor och andra juridisk dokumentation</li>  <li> Lead princip kan du ange hur du hanterar leads från Azure Marketplace slutanvändarportal.</li> </ul> |
| Support            | Innehåller information om support kontakta och princip |
| Test Drive         | Definierar tillgångar som gör det möjligt för användare att testa ditt erbjudande innan de köper det |
|  |  |


## <a name="new-offer-form"></a>Nytt erbjudande formulär

När din logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/), klickar du på den **+ nytt erbjudande** objekt i den vänstra menyraden. I den resulterande menyn, klickar du på **virtuella datorer** att visa den **nytt erbjudande** formar och starta processen med att definiera tillgångar till en ny virtuell dator. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nytt erbjudande användaren gränssnittet val av virtuell dator](./media/publishvm_003.png)

> [!WARNING]
> Om den **virtuella datorer** alternativet inte visas eller inte är aktiverat och sedan ditt konto har inte behörighet att skapa den här erbjudandetypen.  Kontrollera att du uppfyller alla de [krav](./cpp-prerequisites.md) för den här erbjudandetypen, inklusive registrering för ett utvecklarkonto.


## <a name="next-steps"></a>Nästa steg

I efterföljande avsnitt i det här avsnittet spegling av flikarna i den **nytt erbjudande** (för en typ av erbjudande virtuell dator).  Varje artikeln förklarar hur du använder fliken associerade definiera tillgångar och stödtjänster till din nya VM-erbjudandet.

- [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [fliken SKU:er](./cpp-skus-tab.md)
- [fliken Test Drive](./cpp-test-drive-tab.md)
- [fliken Marketplace](./cpp-marketplace-tab.md)
- [fliken Stöd](./cpp-support-tab.md)
