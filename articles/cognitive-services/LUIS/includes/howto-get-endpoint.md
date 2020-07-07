---
title: inkludera fil
description: inkludera fil
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: a6e6c89c34723fb9e11b0c7e4ab8c9bedb8aa9ca
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959057"
---
I avsnittet **Hantera** (övre högra menyn) på sidan **Azure-resurser** (den vänstra menyn) kopierar du **frågans** URL och klistrar in den i en ny flik i webbläsaren.

Slut punkts-URL: en ser ut som i följande format, med din egen anpassade under domän, app-ID och slut punkts nyckel som ersätter APP-ID och nyckel-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```