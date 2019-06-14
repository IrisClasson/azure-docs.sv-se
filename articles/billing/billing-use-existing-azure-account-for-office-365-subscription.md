---
title: Registrera dig för Office 365 med Azure-konto | Microsoft Docs
description: Lär dig hur du skapar en prenumeration på Office 365 med ett Azure-konto
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: b67f3c590be290515329af390b4d3d79a9746112
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369904"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Registrera dig för en prenumeration på Office 365 med ditt Azure-konto
Om du är Azure-prenumerant kan använda du ditt Azure-konto för att registrera dig för en prenumeration på Office 365. Om du är en del av en organisation som har en Azure-prenumeration kan skapa du Office 365-prenumerationer för användare i din befintliga Azure Active Directory (AD Azure). Logga på Office 365 med ett konto som har behörighet som Global administratör eller faktureringsadministratör i din Azure Active Directory-klient. Mer information finns i [Kontrollera kontobehörigheter för mitt i Azure AD](#RoleInAzureAD) och [Tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Om du redan har både ett Office 365-konto och en Azure-prenumeration kan du [associera en Office 365-klient till en Azure-prenumeration](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Skaffa en Office 365-prenumeration med hjälp av ditt Azure-konto

1. Gå till den [produktsidan för Office 365](https://products.office.com/business), och välj en plan.
2. Klicka på **logga in** i det övre högra hörnet på sidan.

    ![Skärmbild av sidan för Office 365-utvärderingsversion](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Logga in med dina Azure-konto. Om du skapar en prenumeration för din organisation kan du använda ett Azure-konto som är medlem i rollen Global administratör eller faktureringsadministratör för directory i Azure Active Directory-klient.

    ![Skärmbild av Office 365-inloggning](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Klicka på **Prova nu**.

    ![Skärmbild som bekräftar din beställning för Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. På sidan ordning kvitto **Fortsätt**.

    ![Skärmbild av Office 365-orderkvittot](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Nu var allt klart.
Om du har skapat Office 365-prenumeration för din organisation kan du använda följande steg för att kontrollera att din Azure AD-användare är nu i Office 365.

1. Öppna Administrationscenter för Microsoft 365.
2. Expandera **användare**, och klicka sedan på **aktiva användare**.

    ![Skärmbild av Microsoft 365 admin center-användare](./media/billing-use-existing-azure-account-office-365-subscription/16-microsoft-365-admin-center-users.png)

När du registrerar dig läggs Office 365-prenumeration i samma Azure Active Directory-instans som tillhör din Azure-prenumeration. Mer information finns i [mer om Azure och Office 365-prenumerationer](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) och [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Kontrollera kontobehörigheter för mitt i Azure AD
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **alla tjänster**, och söker sedan efter **Active Directory**.

    ![Skärmbild av Active Directory i Azure portal](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Klicka på **användare och grupper** > **alla användare**.
4. Välj användarnamnet.

    ![Skärmbild som visar Azure Active Directory-användare](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Klicka på **katalogroll**.

    ![Skärmbild som visar Azure portal katalogrollen](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Rollen **Global administratör** eller **begränsad administratör** > **faktureringsadministratör** krävs för att skapa en Office 365-prenumeration för användare i befintliga Azure Active Directory.

    ![Skärmbild som visar Azure portal katalogroll faktureringsadministratör](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
