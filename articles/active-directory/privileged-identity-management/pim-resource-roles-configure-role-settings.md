---
title: Konfigurera Azure-resurs rollinställningar i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar Azure-resurs rollinställningar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da92ccd50707e676a1f5d583fe22ae8b3d8b73
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476307"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurera Azure-resurs rollinställningar i PIM

När du konfigurerar Azure-resurs rollinställningar kan definiera du standardinställningarna som tillämpas på Azure-resurs-rolltilldelningar i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Använd följande procedurer för att konfigurera arbetsflöde för godkännande och ange vem som kan godkänna eller neka förfrågningar.

## <a name="open-role-settings"></a>Öppna rollinställningar

Följ stegen nedan för att öppna inställningarna för en roll i Azure-resurs.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är medlem i den [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på resursen som du vill hantera, till exempel en prenumerations- eller grupp.

    ![Azure-resurser sida där resurser som kan hanteras](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Klicka på **rollinställningar**.

    ![Rollinställningar sidan lista Azure-resursroller](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Klicka på den roll som du vill konfigurera inställningarna.

    ![Inställningen information rollsidan lista över flera inställningar för tilldelning och aktivering](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Klicka på **redigera** att öppna inställningsfönstret för rollen.

    ![Redigera inställningar för rollsidan med alternativ för att uppdatera inställningarna för tilldelning och aktivering](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    Det finns flera inställningar som du kan konfigurera i fönstret för roll-inställningen för varje roll.

## <a name="assignment-duration"></a>Tilldelningsvaraktighet

Du kan välja mellan två alternativ för tilldelning varaktighet för varje tilldelning (tillgängliga och aktiva) när du konfigurerar inställningar för en roll. De här alternativen blir standard maximal varaktighet när en medlem har tilldelats rollen i PIM.

Du kan välja någon av dessa **berättigade** tilldelningsalternativ varaktighet:

| | |
| --- | --- |
| **Tillåt permanent berättigad uppgift** | Resurs-administratörer kan tilldela permanent berättigad medlemskap. |
| **Upphör att gälla berättigad uppgift efter** | Resurs-administratörer kan kräva att alla berättigade uppgifter har ett visst datum för start- och slutdatum. |

Och du kan välja någon av dessa **active** tilldelningsalternativ varaktighet:

| | |
| --- | --- |
| **Tillåt permanent aktiv uppgift** | Resurs-administratörer kan tilldela permanent aktiv medlemskap. |
| **Upphör att gälla aktiv uppgift efter** | Resurs-administratörer kan kräva att alla aktiva uppgifter har ett visst datum för start- och slutdatum. |

> [!NOTE] 
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av resurs-administratörer. Medlemmar kan också initiera självbetjäning begäranden till [utöka eller förnya rolltilldelningar](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Kräv multifaktorautentisering

PIM innehåller valfria tvingande av Azure Multi-Factor Authentication (MFA) för två olika scenarier.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Kräv Multi-Factor Authentication vid aktiv uppgift

I vissa fall kanske du vill tilldela en medlem till en roll under en kort period (en dag, till exempel). I det här fallet behöver de inte tilldelade medlemmar ska kunna begära aktivering. I det här scenariot kan inte PIM tvinga MFA när medlemmen använder sina rolltilldelningen, eftersom de redan är aktiv i rollen från den tidpunkt då de är tilldelade.

För att säkerställa att resursadministratören uppfyller tilldelningen är de säger att de är, kan du använda Multifaktorautentisering vid aktiv uppgift genom att markera den **Multi-Factor Authentication vid aktiv uppgift** box.

### <a name="require-multi-factor-authentication-on-activation"></a>Kräv Multi-Factor Authentication vid aktivering

Du kan kräva berättigade medlemmar i en roll att köra MFA innan du kan aktivera. Den här processen säkerställer att den användare som begär aktivering är de säger att de är med rimlig säkerhet. Tillämpa det här alternativet skyddar kritiska resurser i situationer när användarkonton kan ha komprometterats.

För att kräva en behörig medlem att köra MFA innan aktivering, kontrollera den **Multi-Factor Authentication vid aktivering** box.

Mer information finns i [multifaktorautentisering (MFA) och PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Aktiveringens maximala varaktighet

Använd den **aktiveringens maximala varaktighet** skjutreglaget för att ange den maximala tiden, i timmar, att en roll förblir aktiv innan den upphör. Det här värdet kan vara mellan 1 och 24 timmar.

## <a name="require-justification"></a>Kräv motivering

Du kan kräva att medlemmar ange en motivering vid aktiv uppgift eller när de aktiverar. För att kräva en anledning, kontrollera den **Kräv motivering vid aktiv uppgift** box eller **Kräv motivering vid aktivering** box.

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Kontrollera den **kräver godkännande för att aktivera** markerar du kryssrutan.

1. Klicka på **Välj godkännare** att öppna dialogrutan Välj ett fönster som är medlem eller grupp.

    ![Välj en medlem eller grupp rutan och välj godkännare](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Välj minst en medlem eller gruppen och klicka sedan på **Välj**. Du kan lägga till valfri kombination av medlemmar och grupper. Du måste välja minst en godkännare. Det finns inga standard godkännare.

    Dina val visas i listan över valda godkännare.

1. När du har angett alla dina rollinställningarna, klickar du på **uppdatering** att spara dina ändringar.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
- [Konfigurera säkerhetsaviseringar för Azure-resursroller i PIM](pim-resource-roles-configure-alerts.md)
