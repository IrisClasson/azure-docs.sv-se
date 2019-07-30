---
title: Publicera app – LUIS
titleSuffix: Azure Cognitive Services
description: När du är klar att skapa och testa active LUIS-appen blir tillgänglig för klientprogrammet genom att publicera den till slutpunkten.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 67f3634141e58cebc22ea643852c09c5d8d9328b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638064"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicera din aktiva, utbildade app till en mellanlagrings-eller produktions slut punkt

När du är klar att skapa och testa active LUIS-appen blir tillgänglig för klientprogrammet genom att publicera den till slutpunkten. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publicering

Om du vill publicera till slutpunkten, Välj **publicera** i övre högra panelen. 

![Övre, högra navigeringsfältet](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Välj rätt plats när popup-fönstret visas: mellanlagring eller produktion. Detta kan du ha två olika versioner med publicerade slutpunkter eller samma version på två olika slutpunkter med hjälp av två platser för publicering. 

Appen har publicerats i alla regioner som associeras med LUIS-resurser som har lagts till i LUIS-portalen. Till exempel för en app som skapats på [www.luis.ai](https://www.luis.ai), om du skapar en LUIS-resurs i **westus** och lägga till den i appen som en resurs, appen publiceras i den regionen. Mer information om LUIS regioner finns i [regioner](luis-reference-regions.md).
 
![Publicering av popup-fönster](./media/luis-how-to-publish-app/publish-pop-up.png)

När din app har publicerats visas ett meddelande om grönt överst i webbläsaren. Grön meddelandefältet innehåller också en länk till slutpunkterna. 

![Publicera popup-fönster med länk till slut punkt](./media/luis-how-to-publish-app/publish-success.png)

Välj länken om du behöver slutpunkts-URL. Du kan också få URL: er för slutpunkten genom att välja **hantera** i den översta menyn och markera **nycklar och slutpunkter** på den vänstra menyn. 

## <a name="configuring-publish-settings"></a>Konfigurera inställningar för publicering

Konfigurera Publiceringsinställningar genom att välja **hantera** direkt i övre navigeringsfältet och välja **Publiceringsinställningar**. 

![Publiceringinställningar](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Publicera när du har aktiverat attitydanalys

<a name="enable-sentiment-analysis"></a>

Attitydanalys kan LUIS för att integrera med [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) att tillhandahålla känsla och nyckeln frasen analyser. 

Du behöver inte ange en nyckel för textanalys och det är kostnadsfritt faktureringen för den här tjänsten på Azure-kontot. När du markerar den här inställningen får är det beständiga. 

Åsiktsdata är ett värde mellan 1 och 0 som anger vilka positiva (närmare 1) eller ett negativt (närmare 0) känsla av data. Sentiment-etiketten för `positive`, `neutral`, och `negative` är per kultur som stöds. För närvarande stöder endast engelska sentiment-etiketter. 

Läs mer om JSON-svar för slutpunkt med attitydanalys [attitydanalys](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="next-steps"></a>Nästa steg

* Se [hantera nycklar](./luis-how-to-azure-subscription.md) nyckel till LUIS och hur du ställer in Bing-stavningskontroll nyckel att lägga till nycklar till Azure-prenumeration och inkluderas alla avsikter i resultaten.
* Se [träna och testa din app](luis-interactive-test.md) anvisningar om hur du testar din publicerade app i test-konsolen.

