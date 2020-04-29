---
title: Dokument information – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Sidan dokument lista visar det första 10 dokumentet i din arbets yta. För vart och ett av dokumenten visas namn, koppling, typ, språk, uppladdnings tid och e-postadress för den användare som laddade upp dokumentet.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595590"
---
# <a name="view-document-details"></a>Visa dokumentinformation

Sidan dokument lista visar det första 10 dokumentet i din arbets yta. För vart och ett av dokumenten visas namn, koppling, typ, språk, uppladdnings tid och e-postadress för den användare som laddade upp dokumentet.

Klicka på ett enskilt dokument för att visa sidan med dokument information. Sidan dokument information visar listan över extraherade meningar från dokumentet.

- Som standard är "källa"-språket markerat i list rutan, men du kan växla för att Visa meningar på mål språket.
- 20 meningar visas som standard på varje sida. Du kan använda sid brytnings kontrollen för att bläddra mellan sidor.

![dokument information](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Ta bort ett dokument

Användaren måste vara ägare av arbets ytan för att ta bort dokumentet för att ta bort ett dokument. Dessutom, om ett dokument används av en modell, som är i någon del av inlärnings processen eller någon del av distributions processen, går det inte att ta bort dokumentet.

1. Gå till sidan dokument
2.  Hovra över alla dokument poster och klicka på pappers korgs ikonen.

    ![Ta bort dokument](media/how-to/how-to-delete-document-1.png)

3.  Bekräfta borttagning.

    ![Bekräfta borttagning](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du tränar en modell](how-to-train-model.md).
