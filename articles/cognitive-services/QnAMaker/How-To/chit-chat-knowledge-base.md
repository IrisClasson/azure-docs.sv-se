---
title: Att lägga till chit-chatt i en kunskapsbas med QnA Maker
titleSuffix: Azure Cognitive Services
description: Lägger till personliga chit-chatt till din robot gör det mer konversationsanpassade och engagerande när du skapar ett KB. QnA Maker kan du enkelt lägga till en i förväg uppsättning övre chit-chatt i din Kunskapsbas.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 5d410e1015b751743c171adabda1d5bcbe68b491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541006"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Lägg till Chit-chatt i en kunskapsbas

Att lägga till chit-chatt i din robot gör det mer konversationsanpassade och engagerande. Funktionen chit-chatt i QnA maker kan du enkelt lägga till en i förväg uppsättning övre chit-chatt i kunskapsbasen (KB). Detta kan vara en startpunkt för din robot personlighet och kan du spara tid och pengar på att skriva dem från grunden.  

Den här datauppsättningen innehåller cirka 100 scenarier av chit chat i form av flera personer som Professional, vänlig och Witty. Välj den person som närmast liknar din robot röst. Får en användarfråga paras QnA Maker ihop med frågor och svar för närmaste kända chit-chatt.  

Några exempel på olika personligheter finns nedan. Du kan se alla personlighet [datauppsättningar](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) tillsammans med information om personligheter.

För användarfråga av `When is your birthday?`, varje person har en formaterad svaret:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personlighet|Exempel|
|--|--|
|Professionell|Ålder verkligen avser inte mig.|
|Eget|Jag har verkligen inte en ålder.|
|Spirituell|Jag är ålder är kostnadsfria.|
|Sköta|Jag har inte en ålder.|
|Entusiastisk|Jag är en robot, så att du inte har en ålder.|
||

> [!NOTE]
> Chit chat-supporten är endast tillgänglig på engelska. 

## <a name="add-chit-chat-during-kb-creation"></a>Lägga till chit-chatt när KB skapas
Det finns ett alternativ för att lägga till chit-chatt under skapande av kunskapsbas, när du lägger till din käll-URL: er och filer. Välj den person som du vill som chit-chatt-bas. Om du inte vill lägga till chit-chatt eller om du redan har chit-chatt-stöd i dina datakällor väljer **ingen**. 
   
![Lägg till chit-chatt vid skapande](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Lägg till Chit-chatt till en befintlig KB
Välj din Kunskapsbas och navigera till den **inställningar** sidan. Det finns en länk till alla chit-chatt-datauppsättningar i rätt **.tsv** format. Ladda ned den person som du vill och ladda upp dem som en källa. Se till att du inte redigera format eller metadata när du hämtar och ladda upp filen. 
  
![Lägg till chit-chatt till befintliga KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Redigera din chit-chatt frågor och svar
När du redigerar din Kunskapsbas visas en ny källa för chit-chatt, baserat på den person som du har valt. Du kan nu lägga till ändras frågor eller redigera svar, precis som med annan källa. 

![Redigera chit-chatt kunskapsbaser](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Om du vill visa metadata, Välj **Visningsalternativ** i verktygsfältet och välj sedan **visa metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Lägg till ytterligare chit-chatt frågor och svar
Du kan lägga till nya chit-chatt frågor och svar som inte i den fördefinierade anges. Se till att du inte duplicerar ett QnA-par som redan omfattas av chit-chatt-uppsättningen. När du lägger till några nya chit-chatt frågor och svar om den läggs till din **språkliga** källa. Lägg till nyckel/värde-par metadata för att säkerställa rankningen förstår att detta är chit-chatt ”, språkliga: chit-chatt”, som visas i följande bild:
   
![! [Add chit-chatt kunskapsbaser] (.. / media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Ta bort chit-chatt från en befintlig KB
Välj din Kunskapsbas och navigera till den **inställningar** sidan. Specifika chit-chatt-källa har listats som en fil med namnet på valda personlighet. Du kan ta bort detta som en källfil.

![Ta bort chit-chatt från KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
