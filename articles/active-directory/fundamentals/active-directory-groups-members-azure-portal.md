---
title: Lägg till eller ta bort grupp medlemmar – Azure Active Directory | Microsoft Docs
description: Instruktioner om hur du lägger till eller tar bort medlemmar från en grupp med hjälp av Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb7b7da4ed5268e66260e48d6367bbe02f0845a
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797584"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Lägg till eller ta bort grupp medlemmar med Azure Active Directory
Med hjälp av Azure Active Directory kan du fortsätta att lägga till och ta bort grupp medlemmar.

## <a name="to-add-group-members"></a>Lägga till grupp medlemmar

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**och välj sedan **grupper**.

3. Från sidan **grupper – alla grupper** söker du efter och väljer den grupp som du vill lägga till medlemmen i. I det här fallet använder du vår tidigare skapade grupp, **MDM-princip – väst**.

    ![Grupper – sidan alla grupper, grupp namn markerat](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. På **översiktssidan för MDM policy – West** väljer du **Medlemmar** i området **Hantera**.

    ![MDM-princip – västra översikts sida med alternativet medlemmar markerat](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Välj **Lägg till medlemmar**och Sök sedan efter och välj var och en av de medlemmar som du vill lägga till i gruppen. Välj sedan **Välj**.

    Du får ett meddelande som säger att medlemmarna har lagts till.

    ![Sidan Lägg till medlemmar, med sökning efter medlem som visas](media/active-directory-groups-members-azure-portal/update-members.png)

6. Uppdatera skärmen om du vill se alla medlems namn som lagts till i gruppen.

## <a name="to-remove-group-members"></a>Ta bort grupp medlemmar

1. Från sidan **grupper – alla grupper** söker du efter och väljer den grupp som du vill ta bort medlemmen från. Vi kommer att använda **MDM-principen – väst**.

2. Välj **medlemmar** i avsnittet **Hantera** , Sök efter och välj namnet på medlemmen som du vill ta bort och välj sedan **ta bort**.

    ![Sidan medlems information med alternativet ta bort](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Nästa steg

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Redigera dina gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
