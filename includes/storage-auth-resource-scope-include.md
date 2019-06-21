---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187013"
---
Fastställa omfattningen av åtkomst som säkerhetsobjektet bör ha innan du tilldelar en RBAC-roll till ett säkerhetsobjekt. Bästa metoder kräver att det alltid är bäst att bevilja endast den minsta möjliga definitionsområdet.

I följande lista beskrivs de nivåer som du kan begränsa åtkomst till Azure blob och kö-resurser, från och med det minsta definitionsområdet:

- **En enskild behållare.** I den här omfattningen, en rolltilldelning som gäller för alla blobar i behållaren, samt egenskaper för behållare och metadata.
- **En enskild kö.** I den här omfattningen gäller en rolltilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Storage-konto.** I den här omfattningen gäller en rolltilldelning till alla behållare och deras blobbar, eller alla köer och deras meddelanden.
- **Resursgruppen.** I den här omfattningen, en rolltilldelning som gäller för alla behållare eller köer i alla lagringskonton i resursgruppen.
- **Prenumerationen.** I den här omfattningen, en rolltilldelning som gäller för alla behållare eller köer i alla lagringskonton i alla resursgrupper i prenumerationen.

> [!IMPORTANT]
> Om din prenumeration omfattar ett namnområde för Azure DataBricks, kommer roller som är tilldelade prenumerationsområde att blockeras från att bevilja åtkomst till blob-och kö.
