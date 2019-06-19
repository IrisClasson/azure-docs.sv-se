---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187651"
---
Klientprogrammet behöver veta om ett yttrande inte är meningsfullt eller lämpligt för programmet. Avsikten **Ingen** läggs till i varje program som en del av skapandeprocessen för att avgöra om ett yttrande inte kan besvaras av klientprogrammet.

Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen eller ge fler anvisningar för att fortsätta konversationen. 

> [!CAUTION] 
> Lämna inte avsikten **Ingen** tom. 

1. Välj **Intents** (Avsikter) på den vänstra panelen.

2. Välj avsikten **None** (Ingen). Lägg till tre yttranden som användarna kan tänkas ange men som inte är relevanta för Human Resources-appen:

    | Exempel på yttranden|
    |--|
    |Skällande hundar är irriterande|
    |Beställ en pizza åt mig|
    |Pingviner i havet|
