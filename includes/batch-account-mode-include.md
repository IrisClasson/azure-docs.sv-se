---
title: ta med fil
description: ta med fil
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127556"
---
> [!NOTE]
> När du skapar ett Batch-konto kan du välja mellan två *poolallokeringslägen*: **användarprenumeration** och **Batch-tjänst**. I de flesta fall bör du använda standardläget Batch-tjänst. I det här läget allokeras pooler i bakgrunden i Azure-hanterade prenumerationer. I det alternativa användarprenumerationsläget skapas virtuella Batch-datorer och andra resurser direkt i din prenumeration när en pool skapas. Användarprenumerationsläge krävs om du vill skapa Batch-pooler med [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Du måste registrera din prenumeration med Azure Batch och koppla kontot till ett Azure Key Vault för att kunna skapa ett Batch-konto i läget Användarprenumeration.