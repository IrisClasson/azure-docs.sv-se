---
title: Använd anpassade taggar för innehållsmoderering - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator innehåller standardtaggar och du kan skapa anpassade taggar för att kontrollera innehållet som är specifika för din verksamhet.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: c1a547f99995d25d19dafb03276306c50a544c9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264724"
---
# <a name="create-and-use-moderation-tags"></a>Skapa och använda taggar för moderering

Förutom de två standardtaggar **isadult** (**en**) och **isracy** (**r**), du kan skapa anpassade taggar mer riktad genomsökning. Dessa anpassade taggar är sedan tillgängliga för mänsklig granskare att tilldela bilder eller text.

## <a name="create-tags"></a>Skapa taggar

1.  Välj taggar på fliken Inställningar.

  ![Content Moderation Tags](images/tags-1.png)

2.  Ange en kort kod för två bokstäver för taggen.
3.  Ange ett namn för taggen. Behåll namnet korta och beskrivande. Till exempel **isbullying**.
4.  Ange en beskrivning.
5.  Klicka på Add (Lägg till).
6.  Klicka på Spara när du är klar skapar taggar.

![Defining Content Moderation Tags](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Med hjälp av anpassade taggar

Anpassade taggar som används under mänsklig granskning. De Visa förhandsgranskning och granskaren väljer du den genom att klicka på den.

![Med hjälp av taggar för innehållsmoderering](images/tags-3-use.png)

Du kan stänga av olika taggar för olika granskning genom att markera eller avmarkera är synliga.
 
![Inaktivera innehållsmoderering taggar](images/tags-4-disable.png)

Du kan inte ta bort de två standardtaggar **isadult** och **isracy**, kan du ta bort eventuella anpassade taggar som du har definierat. Klicka på Papperskorgen bredvid den tagg som du vill ta bort.

![Deleting Content Moderation Tags](images/tags-5-delete.png)

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att använda taggar för bildmoderering, se [granska modereras avbildningar](Review-Moderated-Images.md).
