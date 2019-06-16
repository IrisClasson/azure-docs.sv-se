---
title: Exportera eller ta bort användardata - Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att exportera eller ta bort dina data i Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 1cd70bee22e56e2580b322b93e0f121261d97a94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699191"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportera eller ta bort användardata i Content Moderator

Content Moderator samlar in användardata för att driva tjänsten, men kunder har fullständig behörighet att visa, exportera och ta bort sina data med hjälp av den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) och [Moderering och granska API: er](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och ta bort användardata i Content Moderator finns i följande tabell.

| Data | Exportåtgärden | För att ta bort |
| ---- | ---------------- | ---------------- |
| Kontoinformation (Prenumerationsnycklar) | Gäller inte | Ta bort med hjälp av Azure portal (Azure-prenumerationer). Eller Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. |
| Avbildningar för anpassat matchning | Anropa den [bildsöknings-Get-ID: N API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Bilder lagras i en envägs hash för egna format och det går inte att extrahera själva bilderna. | Anropa den [ta bort alla avbildningar API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Eller ta bort Content Moderator-resursen med hjälp av Azure portal. |
| Villkor för anpassat matchning | CAL den [hämta alla villkor API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Anropa den [ta bort alla villkor API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Eller ta bort Content Moderator-resursen med hjälp av Azure portal. |
| Tags | Gäller inte | Använd den **ta bort** ikon som är tillgängliga för varje tagg på inställningssidan granska Användargränssnittet taggen. Eller Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. |
| Omdömen | Anropa den [Get granska API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet.
| Användare | Gäller inte | Använd den **ta bort** ikon som är tillgängliga för varje användare i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. Eller Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. |

