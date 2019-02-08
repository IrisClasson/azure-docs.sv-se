---
title: Datalagring
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS lagrar krypterade i ett Azure datalager som motsvarar den region som anges av nyckeln.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 05a2bd5334fe2836a96bd9437121252fe6ef6eff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882326"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Datalagring och tas bort i Cognitive Services för Språkförståelse (LUIS)
LUIS lagrar krypterade i ett Azure datalager som motsvarar den region som anges av nyckeln. Dessa data lagras i 30 dagar. 

## <a name="export-and-delete-app"></a>Exportera och ta bort appen
Användarna har full kontroll över [exportera](luis-how-to-start-new-app.md#export-app) och [tar bort](luis-how-to-start-new-app.md#delete-app) appen. 

## <a name="utterances-in-an-intent"></a>Yttranden i ett intent
Ta bort exempel yttranden som används för träning [LUIS](luis-reference-regions.md). Om du tar bort en exempel-uttryck från LUIS-appen tas bort från LUIS-webbtjänsten och är inte tillgänglig för export.

## <a name="utterances-in-review"></a>Yttranden i granskning
Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår i den  **[granskningssidan endpoint yttranden](luis-how-to-review-endoint-utt.md)**. Tar bort yttranden i den här listan förhindrar du att de ska visas som förslag, men ta bort inte dem från loggar.

## <a name="accounts"></a>Konton
Om du tar bort ett konto raderas alla appar, och deras exempel yttranden och loggar. Dessa data hålls kvar i 60 dagar innan kontot och data tas bort permanent.

Tar bort kontot är tillgänglig från den **inställningar** sidan. Välj namnet på ditt konto i det övre högra navigeringsfältet för att komma till den **inställningar** sidan.

## <a name="data-inactivity-as-an-expired-subscription"></a>Data inaktivitet som en prenumeration som har upphört att gälla
För datakvarhållning och borttagning, kan en inaktiv LUIS-app på _Microsofts gottfinnande_ behandlas som en prenumeration som har upphört att gälla. En app betraktas som inaktiv om den uppfyller följande kriterier för de senaste 90 dagarna: 

* Har haft **inga** anrop som görs till den.
* Har inte ändrats.
* Inte har en aktuell nyckel tilldelat till den.
* Inte har haft en användarinloggning till den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om att exportera och ta bort en app](luis-how-to-start-new-app.md)
