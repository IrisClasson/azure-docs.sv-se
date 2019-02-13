---
title: Hantera användardata finns i en undersökning av Azure Security Center | Microsoft Docs
description: " Lär dig hur du hanterar användardata finns i undersökningsfunktionen i Azure Security Center. "
services: operations-management-suite
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: 81bd34cdbe35f3e12d5afddc929b0fac7631f4cc
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113081"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Hantera användardata finns i en undersökning av Azure Security Center
Den här artikeln innehåller information om hur du hanterar användardata finns i undersökningsfunktionen i Azure Security Center. Undersökning data lagras i [Azure Log Analytics](../log-analytics/log-analytics-overview.md) och visas i Security Center. Hantering av användardata omfattar möjligheten att ta bort eller exportera data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Söka efter och identifiera personliga data
Du kan använda Security Center i Azure-portalen [undersökningsfunktionen](../security-center/security-center-investigation.md) att söka efter personliga data. Undersökningsfunktionen är tillgänglig under **säkerhetsaviseringar**.

Undersökningsfunktionen visar alla entiteter, användarinformation och data under den **entiteter** fliken.

## <a name="securing-and-controlling-access-to-personal-information"></a>Skydda och kontrollera åtkomst till personlig information
En Security Center-användare har tilldelats rollen Läsare, ägare, deltagare eller kontoadministratören kan komma åt kundinformation i verktyget.

Se [inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md) mer information om rollerna som läsare, ägare och deltagare. Se [Azure prenumerationens administratörer](../billing/billing-add-change-azure-subscription-administrator.md) vill veta mer om administratörsrollen för kontot.

## <a name="deleting-personal-data"></a>Ta bort personliga data
En Security Center-användare tilldelas rollen som ägare, deltagare, eller kontoadministratören kan ta bort informationen om undersökning.

Om du vill ta bort en undersökning, kan du skicka en `DELETE` begäran till Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

Den `incidentName` indata kan hittas genom att lista alla incidenter med hjälp av en `GET` begäran:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportera personliga data
En Security Center-användare tilldelas rollen som ägare, deltagare, eller kontoadministratör exportera undersökning information. Om du vill exportera undersökning information går du till den **entiteter** flik för att kopiera och klistra in den relevanta informationen.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du hanterar användardata, [hanterar användardata i Azure Security Center](security-center-privacy.md).
Mer information om att ta bort personliga data i Log Analytics finns [exportera och ta bort privata data](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
