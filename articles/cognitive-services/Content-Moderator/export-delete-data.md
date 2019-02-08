---
title: Exportera eller ta bort dina data – Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att exportera eller ta bort dina data i Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: e6a4e12d37886472c4352f16fd10051a50492eda
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868945"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportera eller ta bort användardata i Content Moderator

Content Moderator samlar in användardata för att driva tjänsten, men kunder har fullständig kontroll över visa, exportera och ta bort sina data med hjälp av den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) och [API: er](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och ta bort användardata i Content Moderator finns i följande tabell.

| Data | Exportåtgärden | Borttagningsåtgärd |
| ---- | ---------------- | ---------------- |
| Kontoinformation (Prenumerationsnycklar) | Gäller inte | Ta bort med hjälp av Azure portal (Azure-prenumerationer). Eller Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. |
| Avbildningar för anpassat matchning | [Hämta bild ID](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Bilder lagras i en envägs hash för egna format och det går inte att extrahera själva bilderna. | [Ta bort alla avbildningar](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Eller ta bort Content Moderator-resursen med hjälp av Azure portal. |
| Villkor för anpassat matchning | [Hämta alla villkor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Ta bort alla villkor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Eller ta bort Content Moderator-resursen med hjälp av Azure portal. |
| Taggar | Gäller inte | Använd den **ta bort** ikon som är tillgängliga för varje tagg på inställningssidan granska Användargränssnittet taggen. Eller Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. |
| Omdömen | [Hämta granskning](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet.
| Användare | Gäller inte | Använd den **ta bort** ikon som är tillgängliga för varje användare i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. Eller Använd den **ta bort Team** knappen i den [granska Användargränssnittet](https://contentmoderator.cognitive.microsoft.com/) inställningssidan för teamet. |

