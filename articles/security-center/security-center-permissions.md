---
title: Behörigheter i Azure Security Center | Microsoft Docs
description: I den här artikeln förklaras hur Azure Security Center använder rollbaserad åtkomst kontroll för att tilldela behörigheter till användare och för att identifiera tillåtna åtgärder för varje roll.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: memildin
ms.openlocfilehash: e5728b220f12c3305aa896e64010fc7e5828d6fb
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067230"
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

Azure Security Center använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), vilket ger [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Security Center bedömer konfigurationen av dina resurser för att identifiera säkerhets problem och sårbarheter. I Security Center ser du bara information som rör en resurs när du tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resurs grupp som en resurs tillhör.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhets läsare**: en användare som tillhör den här rollen har visnings rättigheter att Security Center. Användaren kan visa rekommendationer, aviseringar, säkerhets principer och säkerhets tillstånd, men kan inte göra ändringar.
* **Säkerhets administratör**: en användare som tillhör den här rollen har samma rättigheter som säkerhets läsaren och kan också uppdatera säkerhets principen och ignorera aviseringar och rekommendationer.

> [!NOTE]
> Säkerhets rollerna, säkerhets läsaren och säkerhets administratören har endast åtkomst i Security Center. Säkerhets rollerna har inte åtkomst till andra tjänst områden i Azure, till exempel lagring, webb & mobil eller Sakernas Internet.
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visas roller och tillåtna åtgärder i Security Center.

|Action|Säkerhets läsare/ <br> Läsare |Säkerhetsadministratör  |Resurs grupps deltagare/ <br> Resurs grupp ägare  |Prenumerations deltagare  |Prenumerations ägare  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|Redigera säkerhets princip|-|✔|-|-|✔|
|Lägg till/tilldela initiativ (inklusive) regler för regelefterlevnad)|-|-|-|-|✔|
|Ändra pris nivå för prenumeration|-|✔|-|-|✔|
|Aktivera/inaktivera automatisk etablering|-|✔|-|✔|✔|
|Tillämpa säkerhets rekommendationer för en resurs</br> (och Använd [snabb korrigering!](security-center-remediate-recommendations.md#quick-fix-remediation))|-|-|✔|✔|✔|
|Ignorera aviseringar|-|✔|-|✔|✔|
|Visa aviseringar och rekommendationer|✔|✔|✔|✔|✔|

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Du kan till exempel tilldela rollen läsare till användare som bara behöver visa information om säkerhets hälso tillståndet för en resurs men inte vidta åtgärder, till exempel att tillämpa rekommendationer eller redigera principer.
>
>

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras hur Security Center använder RBAC för att tilldela behörigheter till användare och identifierat tillåtna åtgärder för varje roll. Nu när du är bekant med roll tilldelningarna som krävs för att övervaka säkerhets status för din prenumeration, redigera säkerhets principer och använda rekommendationer, lär du dig att:

- [Ange säkerhetsprinciper i Security Center](tutorial-security-policy.md)
- [Hantera säkerhets rekommendationer i Security Center](security-center-recommendations.md)
- [Övervaka Azure-resursernas säkerhets hälsa](security-center-monitoring.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka säkerhets lösningar för partner](security-center-partner-solutions.md)
