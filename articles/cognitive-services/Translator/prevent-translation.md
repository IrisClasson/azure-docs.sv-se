---
title: Förhindra att innehåll translation – Translator Text API
titlesuffix: Azure Cognitive Services
description: Förhindra översättning av innehåll med Translator Text API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: ec47c22ed38b7de261653eeeeafa4540b5d2164c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387620"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Hur du förhindrar översättning av innehåll med Translator Text API

Translator Text API kan du tagga innehåll så att de inte konverteras. Du kanske exempelvis vill tagga kod, ett varumärke eller ett ord eller en viss fras som inte passar när lokaliserade.

## <a name="methods-for-preventing-translation"></a>Metoder för att förhindra översättning
1. Återvänd till en Twitter-tagg @somethingtopassthrough eller #somethingtopassthrough. Ta bort-escape efter översättning.

2. Tagga ditt innehåll med `notranslate`.

   Exempel:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Använd den [dynamisk ordlista](dynamic-dictionary.md) att ange en specifik översättning.

4. Strängen inte skicka till Translator Text API för översättning.

5. Anpassade Translator: Använd en [ordlista i anpassade Translator](custom-translator/what-is-dictionary.md) vidtar den föreskrivna översättningen av en mening med 100% sannolikhet.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Undvika translation i Translator API-anrop](reference/v3-0-translate.md)
