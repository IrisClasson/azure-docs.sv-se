---
title: Azure InfoBox UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.TextBlock UI-element för Azure-portalen.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 2330197b4512dfdd72de3529145103b644594e25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711207"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI element
En kontroll som lägger till en informationsruta. Rutan innehåller viktig text eller varningar som hjälper användarna att förstå de värden som de anger. Det kan också länka till en URI för mer information.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schema
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Kommentarer

* För `icon`, använda **ingen**, **Info**, **varning**, eller **fel**.
* Den `uri` egenskapen är valfri.

## <a name="sample-output"></a>Exempel på utdata

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).
