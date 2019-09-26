---
title: Korrigera felstavade ord – LUIS
titleSuffix: Azure Cognitive Services
description: Rätta felstavade ord i yttranden genom att lägga till Bing stavningskontroll kontrollera API V7 LUIS endpoint frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: 3bde609c83425d660c867939e034247d7e6898c5
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300277"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Rätta felstavade ord med stavningskontroll i Bing

Du kan integrera LUIS-appen med [Bing stavningskontroll kontrollera API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) att korrigera felstavade ord. i yttranden innan LUIS förutsäger poäng och entiteter av uttryck. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Skapa första nyckeln för Bing stavningskontroll kontrollera V7
Din [första nyckeln för stavningskontroll i Bing v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) är kostnadsfri. 

![Skapa kostnadsfria nyckel](./media/luis-tutorial-bing-spellcheck/free-key.png)

< ett namn på ”Skapa-subscription-key” ></a>
## <a name="create-endpoint-key"></a>Skapa slutpunktsnyckel
Om din kostnadsfria nyckel har gått ut kan du skapa en slutpunktsnyckel.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **skapa en resurs** i det övre vänstra hörnet.

3. Skriv `Bing Spell Check API V7` i sökrutan.

    ![Sök efter för stavningskontroll i Bing Kontrollera API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Välj tjänsten. 

5. En panelen visas till höger som innehåller information inklusive juridiskt meddelande. Välj **skapa** att starta processen att skapa prenumerationen. 

6. Ange inställningar i nästa panel. Vänta tills tjänsten skapandeprocessen ska slutföras.

    ![Ange inställningar för tjänsten](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Välj **alla resurser** under den **Favoriter** rubrik navigeringen till vänster.

8. Välj den nya tjänsten. Är av typen **Cognitive Services** och platsen är **globala**. 

9. I panelen huvudsakliga väljer **nycklar** att se din nya nycklar.

    ![Hämta nycklar](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopiera den första nyckeln. Du behöver bara en av två nycklar. 

## <a name="using-the-key-in-luis-test-panel"></a>I nyckeln i LUIS test panelen
Det finns två platser i LUIS för att använda nyckeln. Först är i den [test panelen](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Nyckeln sparas inte i LUIS men i stället är en sessionsvariabeln. Du måste ange nyckeln varje gång du vill test-panelen för att tillämpa stavningskontroll i Bing v7-tjänsten på uttryck. Se [instruktioner](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) på panelen test för att ställa in nyckeln.

## <a name="adding-the-key-to-the-endpoint-url"></a>Att lägga till nyckeln till slutpunkts-URL
Slutpunkt-frågan måste nyckeln som har skickats frågesträngparametrarna för varje fråga som du vill använda stavning korrigering. Du kan ha en chattrobot som anropar LUIS eller du kan anropa LUIS slutpunkten API direkt. Oavsett hur slutpunkten som anropas, måste varje anrop innehålla information som behövs för stavningskorrigeringar ska fungera korrekt.

Slutpunkten URL: en har flera värden som ska skickas på rätt sätt. Stavningskontroll i Bing v7 nyckeln är bara en av dessa. Måste du ställa in den **Stavkontroll** parametern till true och du måste ange värdet för **bing-stavningskontroll-kontroll-subscription-key** till nyckelvärdet:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Skicka felstavade uttryck till LUIS
1. I en webbläsare, Kopiera föregående sträng och Ersätt den `region`, `appId`, `luisKey`, och `bingKey` med dina egna värden. Se till att använda regionen slutpunkten om den skiljer sig från din publicering [region](luis-reference-regions.md).

2. Lägg till en felstavade uttryck som ”hur långt är mountainn”?. På engelska, `mountain`, med en `n`, är rätt stavning. 

3. Välj ange för att skicka frågan till LUIS.

4. LUIS svarar med ett JSON-resultat för `How far is the mountain?`. Om stavningskontroll i Bing v7 identifierar stavningsförslag, den `query` innehåller den ursprungliga frågan i fältet i LUIS-app JSON-svar och `alteredQuery` fältet innehåller korrigerad fråga som skickats till LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorera stavfel

Om du inte vill använda tjänsten API för stavningskontroll i Bing v7 måste du lägga till korrekt och felaktig stavning. 

Två lösningar:

* Label-exempel yttranden som har alla olika stavningar så att LUIS kan lära sig korrekt stavning och skrivfel. Det här alternativet kräver mer etikettering av dig än att använda en stavningskontroll.
* Skapa en fras lista med alla varianter av ordet. Med den här lösningen behöver du inte märka ord variationerna i exemplet yttranden. 

## <a name="publishing-page"></a>Publiceringssida
Den [publishing](luis-how-to-publish-app.md) sidan har en **Aktivera Bing-stavningskontroll** kryssrutan. Det här är att underlätta för att skapa nyckeln och förstå hur slutpunkts-URL ändras. Du måste använda rätt slutpunkt-parametrar för att få stavning korrigerad för varje uttryck. 

> [!div class="nextstepaction"]
> [Mer information om exempel yttranden](luis-how-to-add-example-utterances.md)
