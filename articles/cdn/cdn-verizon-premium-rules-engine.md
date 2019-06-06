---
title: Åsidosätt HTTP beteende med hjälp av Azure CDN från Verizon Premium regelmotor | Microsoft Docs
description: Regelmotorn kan du anpassa hur HTTP-begäranden hanteras av Azure CDN från Verizon Premium, till exempel blockera leverans av vissa typer av innehåll, definiera en Cachelagringsprincip och ändra HTTP-huvuden.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 81af3073d64e4379972568a57907a7fb2f82356d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481736"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Åsidosätt HTTP beteende med hjälp av Azure CDN från Verizon Premium regelmotor

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Regelmotor Azure CDN kan du anpassa hur HTTP-begäranden ska hanteras. Till exempel blockera leverans av vissa typer av innehåll, definiera en Cachelagringsprincip eller ändra ett HTTP-huvud. Den här självstudien visar hur du skapar en regel som ändrar funktionssättet för cachelagring av CDN-tillgångar. Läs mer om vilken syntax som motorn regler [Azure CDN regelmotor – referens](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

För att komma åt regelmotorn, måste du först välja **hantera** högst upp på den **CDN-profil** sidan för att komma åt sidan för hantering av Azure CDN. Beroende på om slutpunkten är optimerad för acceleration av dynamisk webbplats (DSA), åt du sedan regelmotor med uppsättningen regler som är lämpliga för din typ av slutpunkt:

- Slutpunkter som är optimerad för allmän webbleverans eller andra icke-DSA-optimering:
    
    Välj den **HTTP stora** och sedan välja **regelmotor**.

    ![Regelmotor för HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Slutpunkter som är optimerade för DSA:
    
    Välj den **ADN** och sedan välja **regelmotor**.
    
    ADN är en term som används av Verizon för att ange DSA-innehåll. Alla regler som du skapar här ignoreras av alla slutpunkter i din profil som inte har optimerats för DSA.

    ![Regelmotor för DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Självstudier

1. Från den **CDN-profil** väljer **hantera**.
   
    ![Knappen för CDN-profil hantera](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.

2. Välj den **HTTP stora** och sedan välja **regelmotor**.
   
    Alternativ för en ny regel visas.
   
    ![Nya alternativ för CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Angiven ordning i vilken flera regler påverkar hur de ska hanteras. En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   >

3. Ange ett namn i den **namn / beskrivning** textrutan.

4. Identifiera vilken typ av förfrågningar som regeln gäller för. Använda matchningsvillkor standard **alltid**.
   
   ![Matchningsvillkor för CDN-regel](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Flera matchningsvillkor är tillgängliga i den nedrullningsbara listan. För information om den markerade matchningsvillkor, väljer du endast i informationssyfte ikonen till vänster.
   >
   >  En detaljerad lista över villkorsuttryck Se [regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > En detaljerad lista över matchningsvillkor Se [regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Om du vill lägga till en ny funktion, Välj den **+** bredvid knappen **funktioner**.  I listrutan till vänster, Välj **kraft interna Max-Age**.  Ange i textrutan som visas, **300**. Ändra inte de återstående standardvärdena.
   
   ![Funktionen för CDN-regel](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Flera funktioner är tillgängliga i den nedrullningsbara listan. För information om den valda funktionen väljer du endast i informationssyfte ikonen till vänster.
   >
   > För **kraft interna Max-Age**, tillgångens `Cache-Control` och `Expires` rubriker åsidosätts för att styra när kantnod CDN uppdaterar tillgången från ursprunget. I det här exemplet cachelagrar kantnod CDN tillgången i 300 sekunder eller 5 minuter innan den uppdateras tillgången från dess ursprung.
   >
   > En detaljerad lista över funktioner finns i [regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Välj **Lägg till** att spara den nya regeln.  Den nya regeln är nu väntar på godkännande. När det har godkänts får status ändras från **väntande XML** till **Active XML**.
   
   > [!IMPORTANT]
   > Regler ändringar kan ta upp till 10 minuter att sprida via Azure CDN.
   >
   >

## <a name="see-also"></a>Se också

- [Översikt över Azure CDN](cdn-overview.md)
- [Regelmotor – referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Azure CDN kraftfulla nya premium-funktioner](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)