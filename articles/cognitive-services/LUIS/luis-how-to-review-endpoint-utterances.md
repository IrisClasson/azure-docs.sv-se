---
title: Granska användarens yttranden-LUIS
titleSuffix: Azure Cognitive Services
description: Active Learning samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa Read-World-yttranden. Acceptera ändringarna i ditt exempel yttranden och träna och publicera. LUIS identifierar sedan yttranden mer noggrant.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: c617e4aa62ce2ff468545bef0b2ebe2c4d0e4f03
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382365"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Så här granskar du slut punkts yttranden i LUIS-portalen för aktiv inlärning

[Active Learning](luis-concept-review-endpoint-utterances.md) samlar in slut punkts frågor och väljer användarens slut punkt yttranden att det är osäkert. Du kan granska dessa yttranden för att välja avsikten och markera entiteter för dessa Read-World-yttranden. Acceptera ändringarna i ditt exempel yttranden och träna och publicera. LUIS identifierar sedan yttranden mer noggrant.


## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Logga användar frågor för att aktivera aktiv inlärning. Detta åstadkommer du genom att ställa in [slut punkts frågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med `log=true` parametern QueryString och värdet.

## <a name="disable-active-learning"></a>Inaktivera aktiv inlärning

Du inaktiverar aktiv inlärning genom att inte logga användar frågor. Detta åstadkommer du genom att ställa in [slut punkts frågan](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) med `log=false` parametern QueryString och värdet.

## <a name="filter-utterances"></a>Filtrera uttryck

1. Öppna din app (till exempel TravelAgent) genom att välja dess namn på **Mina appar** sidan och välj sedan **skapa** i det översta fältet.

1. Under den **förbättra apprestanda**väljer **granska endpoint yttranden**.

1. På den **granska endpoint yttranden** väljer i den **filtrera listan efter avsikt eller entitet** textrutan. Den här listan innehåller alla avsikter under **AVSIKTER** och alla entiteter under **ENTITETER**.

    ![Yttranden filter](./media/label-suggested-utterances/filter.png)

1. Välj en kategori (avsikter eller entiteter) i den nedrullningsbara listan och granska talade.

    ![Avsiktshantering yttranden](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etikett-entiteter
LUIS ersätter entitet token (ord) med entitetsnamn bara. Om ett uttryck har utan etikett entiteter, märker du dem i uttryck. 

1. Välj på orden i uttryck. 

1. Välj en entitet i listan.

    ![Etikett-entitet](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Justera enda uttryck

Varje uttryck har en föreslagna avsikt som visas i den **justerad avsikt** kolumn. 

1. Om du godkänner det förslaget, väljer du på kryssmarkeringen.

    ![Behåll justerade avsikt](./media/label-suggested-utterances/align-intent-check.png)

1. Om du inte samtycker till förslaget, Välj rätt avsikten i justerade avsikt nedrullningsbara listan och välj sedan på kryssmarkeringen till höger om justerade avsikten. 

    ![Justera avsikt](./media/label-suggested-utterances/align-intent.png)

1. När du har valt på kryssmarkeringen tas i uttryck bort från listan. 

## <a name="align-several-utterances"></a>Justera flera yttranden

Om du vill justera flera yttranden kryssrutan till vänster om talade och välj sedan på den **Lägg till vald** knappen. 

![Justera flera](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Kontrollera justerade avsikt

Du kan kontrollera uttryck har justerats med rätt avsikten genom att gå till den **avsikter** väljer du namnet på avsikt, och granska talade. Uttryck från **granska endpoint yttranden** i listan.

## <a name="delete-utterance"></a>Ta bort uttryck

Varje uttryck kan tas bort från listan över granskning. När bort kommer visas den inte i listan igen. Detta gäller även om användaren anger samma uttryck från slutpunkten. 

Om du är osäker på om bör du ta bort uttryck, flytta den till avsikt, ingen antingen eller skapa en ny avsikt, till exempel ”övriga” och flytta uttryck till detta syfte. 

## <a name="delete-several-utterances"></a>Ta bort flera yttranden

Om du vill ta bort flera yttranden, markerar du varje objekt och väljer på Papperskorgen till höger om den **Lägg till vald** knappen.

![Ta bort flera](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Nästa steg

Om du vill testa hur prestanda förbättras när du lagt till etiketter föreslagna yttranden, du kan komma åt konsolen test genom att välja **testa** i den övre panelen. Anvisningar om hur du testar din app med hjälp av test-konsolen finns i [träna och testa din app](luis-interactive-test.md).
