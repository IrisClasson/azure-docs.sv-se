---
title: Bevilja åtkomst till andra administratörer att hantera PIM - Azure Active Directory | Microsoft Docs
description: Lär dig mer om att bevilja åtkomst till andra myndigheter att hantera Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22e3cc93baebac023c0148812c6a4c6c95be60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60288650"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Bevilja åtkomst till andra administratörer att hantera PIM

Den globala administratören som gör Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för en organisation att automatiskt få rolltilldelningar och åtkomst till PIM. Ingen annan hämtar skrivåtkomst som standard, men även andra globala administratörer. Andra globala administratörer, säkerhetsadministratörer och Säkerhetsläsare har skrivskyddad åtkomst till PIM. Om du vill bevilja åtkomst till PIM, kan den första användaren tilldela andra användare till den **privilegierad Rolladministratör** roll.

> [!NOTE]
> Hantera PIM kräver Azure MFA. Eftersom Microsoft-konton inte kan registrera dig för Azure MFA, en användare som loggar in med ett Microsoft-konto inte åtkomst till PIM.

Kontrollera att det finns alltid minst två användare i en privilegierad Rolladministratör roll, om en användare är utelåst eller deras kontot har tagits bort.

## <a name="grant-access-to-manage-pim"></a>Bevilja åtkomst till hantera PIM

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **roller**.

    ![PIM-Azure AD-roller – roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Klicka på den **privilegierad Rolladministratör** roll att öppna sidan medlemmar.

    ![Privilegierad Rolladministratör - medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Klicka på **Lägg till medlem** att öppna fönstret Lägg till hanterade medlemmar.

1. Klicka på **Välj medlemmar** att öppna fönstret Välj medlemmar.

    ![Privilegierad Rolladministratör - Välj medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Välj en och klickar sedan på **Välj**.

1. Klicka på **OK** att berättiga medlemmen för den **privilegierad Rolladministratör** roll.

    När du tilldelar en ny roll till någon i PIM måste de konfigureras automatiskt som **berättigade** aktiverar rollen.

1. Om du vill göra medlemmen permanenta, klickar du på användaren i medlemslistan privilegierad Rolladministratör.

1. Klicka på **mer** och sedan **göra permanenta** att tilldelningen är permanent.

    ![Privilegierad Rolladministratör - göra permanenta](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Skicka användaren en länk till [börja använda PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Ta bort åtkomst för att hantera PIM

Innan du tar bort någon från rollen privilegierad Rolladministratör kan du alltid kontrollera kommer fortfarande att ha minst två användare som är tilldelade till den.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **roller**.

1. Klicka på den **privilegierad Rolladministratör** roll att öppna sidan medlemmar.

1. Lägg till en bock bredvid de användare som du vill ta bort och klicka sedan på **ta bort medlem**.

    ![Privilegierad Rolladministratör - ta bort medlem](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Klicka på meddelandet som visas som frågar om du vill ta bort medlemmen från rollen **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Börja använda PIM](pim-getting-started.md)
