---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c3677e7897498aa06d7bd547988ad4dc0326f39b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187432"
---
## <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider

Om du inte har använt Event Grid förut i din Azure-prenumeration kan du behöva registrera Event Grid-resursprovidern.

På Azure Portal:

1. Välj **Prenumerationer**.
1. Välj den prenumeration som du använder för Event Grid.
1. Under **Inställningar** väljer du **Resursprovidrar**.
1. Hitta **Microsoft.EventGrid**.
1. Om du inte är registrerad väljer du **Registrera**. 

Det kan ta en stund att slutföra registreringen. Välj **Uppdatera** för att uppdatera statusen. När **Status** är **Registrerad** är du redo att fortsätta.