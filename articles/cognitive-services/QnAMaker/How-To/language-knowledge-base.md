---
title: Icke-engelska knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker stöder kunskapsbas innehåll på många olika språk. Men ska varje QnA Maker-tjänsten reserveras för ett språk. Första kunskapsbasen skapade riktar in sig på en viss QnA Maker-tjänst anger språket i tjänsten.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: f6c317cc1281a5a9bc18a2057fa12b7b61bb7689
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61372045"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Språkstöd för kunskapsbas innehåll för QnA Maker
QnA Maker stöder kunskapsbas innehåll på många olika språk. Men ska varje QnA Maker-tjänsten reserveras för ett språk. Första kunskapsbasen skapade riktar in sig på en viss QnA Maker-tjänst anger språket i tjänsten. Se [här](../Overview/languages-supported.md) lista över språk som stöds.

Språk identifieras automatiskt från innehållet i de datakällor som extraheras. När du skapar en ny QnA Maker-tjänsten och en ny kunskapsbas i tjänsten kan kontrollera du att språket som har ställts in korrekt.

1. Navigera till den [Azure-portalen](https://portal.azure.com/).

2. Välj **resursgrupper** och navigera till resursgruppen där QnA Maker-tjänsten har distribuerats och väljer den **Azure Search** resurs.

    ![Välj Azure Search-resurs](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Välj den **testkb** index. Den här Azure Search-index är alltid den första skapas och innehåller sparade innehållet i alla kunskapsbaser i tjänsten. 

    ![Välj testet KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Välj **fält** avsnitt som visar testkb information.

    ![Välj fält](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Markera kryssrutan för **Analyzer** att visa språkinformationen.

    ![Välj Analyzer](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Du bör hitta att analysatorn är inställt på ett visst språk. Det här språket identifierades automatiskt under steget kunskapsbas skapas. Det här språket kan inte ändras när resursen har skapats.

    ![Valda Analyzer](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en QnA-roboten med Azure Bot Service](../Tutorials/create-qna-bot.md)
