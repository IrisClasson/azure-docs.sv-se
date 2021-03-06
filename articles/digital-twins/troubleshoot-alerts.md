---
title: Konfigurera aviseringar
titleSuffix: Azure Digital Twins
description: Se hur du aktiverar aviseringar på digitala Azure-mått.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1b296cd942e36817da2832467ab603ebd833f825
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87910381"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Felsöka Azure Digitals dubbla: aviseringar

Azure Digitals flätar samlar in [Mät värden](troubleshoot-metrics.md) för din tjänst instans som ger information om statusen för dina resurser. Du kan använda dessa mått för att utvärdera den övergripande hälso statusen för Azure Digital-tjänster och de resurser som är anslutna till den.

**Aviseringar** proaktivt meddela dig när viktiga villkor finns i dina Mät data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Du kan läsa mer om aviseringar i [*Översikt över aviseringar i Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).

## <a name="turn-on-alerts"></a>Aktivera aviseringar

Så här aktiverar du aviseringar för din Azure Digital-instansen:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din Azure Digital-instansen. Du kan hitta det genom att skriva dess namn i portalens sökfält. 

2. Välj **aviseringar** på menyn och sedan **+ ny varnings regel**.

    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Skärm bild som visar sidan aviseringar och knapp att lägga till. Inga aviseringar har kon figurer ATS än" lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. På sidan *skapa aviserings regel* som följer kan du följa anvisningarna för att definiera villkor, åtgärder som ska utlösas och aviserings information.     
    * **Omfattnings** information ska automatiskt fylla i med information om din instans.
    * Du definierar **villkor** och **Åtgärds grupps** information för att anpassa aviserings utlösare och svar.

    :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Skärm bild som visar sidan Skapa aviserings regel med avsnitt för omfattning, villkor och åtgärds grupp" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

En guidad genom gång av hur du fyller i de här fälten finns i [*Översikt över aviseringar i Microsoft Azure*](../azure-monitor/platform/alerts-overview.md). Nedan visas några exempel på hur stegen kommer att se ut för Azure Digital-dubbla.

Här är ett utdrag från *Select condition* -processen som illustrerar vilka typer av aviserings signaler som är tillgängliga för Azure Digital-meddelanden. På den här sidan kan du filtrera typ av signal och välja den signal som du vill använda i en lista.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Skärm bild som visar den första sidan för att konfigurera signal logik. Det finns en markering i rutan signal typ för att välja mått eller aktivitets loggar, samt en lista över mått under som kan väljas":::

När du har valt en signal uppmanas du att konfigurera logiken för aviseringen. Du kan filtrera på en dimension, ange ett tröskelvärde för aviseringen och ange frekvensen för kontrollerna för villkoret. Här är ett exempel på hur du ställer in en avisering för när måttet för den genomsnittliga routing-felfrekvensen hamnar över 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Skärm bild som visar den andra sidan för att konfigurera signal logik. Sidan visar mått historiken, har ett område för att filtrera på en dimension som Event Grid åtgärder och ett avsnitt för att definiera varnings logik som till exempel genomsnittet är större än 5":::
 
När du har konfigurerat aviseringarna visas de upp på *aviserings* sidan för din instans.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Skärm bild som visar sidan aviseringar och knapp att lägga till. En varning har kon figurer ATS" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>Nästa steg

* Mer information om aviseringar med Azure Monitor finns i [*Översikt över aviseringar i Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).
* Information om Azure Digitals dubbla mått finns i [*fel sökning: Visa mått med Azure Monitor*](troubleshoot-metrics.md).
* Information om hur du aktiverar diagnostikloggning för dina mått finns i [*fel sökning: Konfigurera diagnostik*](troubleshoot-diagnostics.md).
