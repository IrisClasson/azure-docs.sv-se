---
title: Inställningar för AS2-meddelanden
description: Referens guide för AS2 för att skicka och ta emot inställningar i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74793039"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Referens för AS2-meddelande inställningar i Azure Logic Apps med Enterprise-integrationspaket

Den här referensen beskriver de egenskaper som du kan ange för att ange hur ett AS2-avtal ska hantera meddelanden som skickas och tas emot mellan handels partner. Konfigurera dessa egenskaper baserat på ditt avtal med partnern som utbyter meddelanden med dig.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 ta emot inställningar

![Välj "ta emot inställningar"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Åsidosätt meddelande egenskaper** | No | Åsidosätter egenskaperna för inkommande meddelanden med dina egenskaps inställningar. |
| **Meddelandet ska vara signerat** | No | Anger om alla inkommande meddelanden måste signeras digitalt. Om du behöver logga in väljer du en befintlig gäst partner offentligt certifikat i listan **certifikat** för att verifiera signaturen på meddelandena. Om du inte har ett certifikat kan du läsa mer om [att lägga till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Meddelandet ska vara krypterat** | No | Anger om alla inkommande meddelanden måste krypteras. Icke-krypterade meddelanden avvisas. Om du behöver kryptering väljer du ett befintligt privat certifikat för värd partner för dekryptering av inkommande meddelanden i listan **certifikat** . Om du inte har ett certifikat kan du läsa mer om [att lägga till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Meddelandet ska komprimeras** | No | Anger om alla inkommande meddelanden måste komprimeras. Icke-komprimerade meddelanden avvisas. |
| **Tillåt inte dubbletter av meddelande-ID** | No | Anger om meddelanden med dubbla ID: n ska tillåtas. Om du inte tillåter dubbletter av ID väljer du antalet dagar mellan kontrollerna. Du kan också välja om du vill pausa dubbletter. |
| **MDN-text** | No | Anger standard meddelande dispositions meddelandet (MDN) som du vill skicka till meddelande avsändaren. |
| **Skicka MDN** | No | Anger om synkrona MDNs ska skickas för mottagna meddelanden.  |
| **Skicka signerade MDN** | No | Anger om signerade MDNs ska skickas för mottagna meddelanden. Om du behöver signering väljer du algoritmen som ska användas för signering av meddelanden i listan **MIC algorithm** . |
| **Skicka asynkrona MDN** | No | Anger om MDNs ska skickas asynkront. Om du väljer asynkrona MDNs anger du URL: en för var du vill skicka MDNs i rutan **URL** . |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Skicka inställningar för AS2

![Välj "Skicka inställningar"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Aktivera meddelande signering** | No | Anger om alla utgående meddelanden måste signeras digitalt. Om du behöver logga väljer du dessa värden: <p>– I listan **Signeringsalgoritm** väljer du algoritmen som ska användas för signering av meddelanden. <br>– Gå till listan **certifikat** och välj ett befintligt privat certifikat för värd partner för signering av meddelanden. Om du inte har ett certifikat kan du läsa mer om [att lägga till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Aktivera meddelande kryptering** | No | Anger om alla utgående meddelanden måste krypteras. Om du behöver kryptering väljer du dessa värden: <p>– I listan **krypteringsalgoritm** väljer du den offentliga certifikats algoritm för gäst partner som ska användas för att kryptera meddelanden. <br>– Gå till listan **certifikat** och välj ett befintligt privat certifikat för gäst partner för kryptering av utgående meddelanden. Om du inte har ett certifikat kan du läsa mer om [att lägga till certifikat](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Aktivera meddelande komprimering** | No | Anger om alla utgående meddelanden måste komprimeras. |
| **Unfold HTTP-rubriker** | No | Placerar HTTP- `content-type` rubriken på en enda rad. |
| **Överför fil namn i MIME-huvud** | No | Anger om fil namnet ska inkluderas i MIME-huvudet. |
| **Begär MDN** | No | Anger om meddelande dispositions aviseringar (MDNs) ska tas emot för alla utgående meddelanden. |
| **Begär signerad MDN** | No | Anger om du vill ta emot signerade MDNs för alla utgående meddelanden. Om du behöver signering väljer du algoritmen som ska användas för signering av meddelanden i listan **MIC algorithm** . |
| **Begär asynkron MDN** | No | Anger om MDNs ska tas emot asynkront. Om du väljer asynkrona MDNs anger du URL: en för var du vill skicka MDNs i rutan **URL** . |
| **Aktivera NRR** | No | Anger om du vill kräva oavvislig het (NRR). Detta kommunikations-attribut ger bevis på att data har tagits emot som de adresserade. |
| **Format för SHA2-algoritm** | No | Anger det format för MIC-algoritm som ska användas för signering i meddelandehuvuden för utgående AS2-meddelanden eller MDN |
||||

## <a name="next-steps"></a>Nästa steg

[Utbyta AS2-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2.md)
