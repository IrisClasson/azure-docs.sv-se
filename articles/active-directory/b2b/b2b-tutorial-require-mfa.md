---
title: Tutorial - Multi-factor authentication for B2B - Azure AD
description: In this tutorial, learn how to require multi-factor authentication (MFA) when you use Azure AD B2B to collaborate with external users and partner organizations.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bddf1642b2013567fbc23278b3d8d32692601d55
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420588"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Självstudier: Använda multifaktorautentisering för B2B-gästanvändare

När du samarbetar med externa B2B-gästanvändare är det en bra idé om du skyddar dina appar med principer för multifaktorautentisering (MFA). De externa användarna kommer då att behöva mer än bara ett användarnamn och ett lösenord för att få åtkomst till dina resurser. In Azure Active Directory (Azure AD), you can accomplish this goal with a Conditional Access policy that requires MFA for access. MFA-principerna kan tillämpas i klientorganisations- eller appnivå eller på enskild gästanvändarnivå, på samma sätt som de aktiveras för medlemmar i din organisation.

Exempel:

![Diagram showing a guest user signing into a company's apps](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  En administratör eller anställd på företag A bjuder in en gästanvändare att använda ett molnprogram eller lokalt program som är konfigurerat för att kräva MFA för åtkomst.
2.  Gästanvändaren loggar med sina egna arbets- eller skolidentiteter eller sociala identiteter. 
3.  Användaren uppmanas att slutföra en MFA-kontroll. 
4.  Användaren ställer in MFA med företag A och väljer sina MFA-alternativ. Användaren får åtkomst till programmet.

I den här kursen ska du:

> [!div class="checklist"]
> * Testa inloggningen innan du påbörjar MFA-installationen.
> * Create a Conditional Access policy that requires MFA for access to a cloud app in your environment. I den här självstudien använder vi Microsoft Azure Management-appen för att illustrera processen.
> * Använd What If-verktyget för att simulera MFA-inloggningen.
> * Test your Conditional Access policy.
> * Rensa testanvändare och princip.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra scenariot i den här självstudien behöver du:

 - **Access to Azure AD Premium edition**, which includes Conditional Access policy capabilities. To enforce MFA, you need to create an Azure AD Conditional Access policy. Observera att MFA-principer alltid tillämpas i din organisation, oavsett om partnern har MFA-funktioner eller inte. Om du konfigurerar MFA i organisationen måste du se till att du har tillräckligt med Azure AD Premium-licenser för din gästanvändare. 
 - **Ett giltigt externt e-postkonto** som du kan lägga till i din klientkatalog som gästanvändare och använda för att logga in. Om du inte vet hur du skapar ett gästkonto, så gå till [Lägga till en B2B-gästanvändare i Azure Portal](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Skapa en testgästanvändare i Azure AD

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Välj **Azure Active Directory** i den vänstra rutan.
3.  Under **Hantera** väljer du **Användare**.
4.  Välj **Ny gästanvändare**.

    ![Screenshot showing where to select the New guest user option](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  Under **Användarnamn** anger du den externa användarens e-postadress. Du kan också lägga till ett välkomstmeddelande. 

    ![Screenshot showing where to enter the guest invitation message](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Välj **Bjud in** för att skicka inbjudan till gästanvändaren automatiskt. Meddelandet **Användaren har bjudits in** visas. 
7.  När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testa inloggningen innan du påbörjar MFA-installationen
1.  Använd ditt testanvändarnamn och lösenordet för att logga in på [Azure Portal](https://portal.azure.com/).
2.  Observera att du kan få åtkomst till Azure Portal bara genom att använda dina inloggningsuppgifter. Ingen ytterligare autentisering krävs.
3.  Logga ut.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Create a Conditional Access policy that requires MFA
1.  Sign in to your [Azure portal](https://portal.azure.com/) as a security administrator or a Conditional Access administrator.
2.  Välj **Azure Active Directory** i Azure Portal. 
3.  On the **Azure Active Directory** page, in the **Security** section, select **Conditional Access**.
4.  Välj **Ny princip** i verktygsfältet högst upp på sidan **Villkorsstyrd åtkomst**.
5.  Skriv **Kräver MFA för B2B-portalåtkomst** i textrutan **Namn** på sidan **Ny**.
6.  Välj **Användare och grupper** i avsnittet **Tilldelningar**.
7.  Välj **Välj användare och grupper** på sidan **Användare och grupper** och välj sedan **Alla gästanvändare (förhandsversion)** .

    ![Screenshot showing selecting all guest users](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Välj **Done** (Klar).
10. Välj **Molnappar** i avsnittet **Tilldelningar** på sidan **Ny**.
11. Välj **Välj appar** på sidan **Molnappar** och välj sedan **Välj**.

    ![Screenshot showing the Cloud apps page and the Select option](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Välj **Microsoft Azure-hantering** på sidan **Välj** och välj sedan **Välj**.

    ![Screenshot showing the Microsoft Azure Management app selected](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Välj **Klar** på sidan **Molnappar**.
14. Välj **Bevilja** i avsnittet **Åtkomstkontroller** på sidan **Ny**.
15. Välj **Bevilja åtkomst** på sidan **Bevilja**, markera kryssrutan **Kräv multifaktorautentisering** och välj sedan **Välj**.

    ![Screenshot showing the Require multi-factor authentication option](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. Välj **På** under **Aktivera princip**.

    ![Screenshot showing the Enable policy option set to On](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Välj **Skapa**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Simulera inloggningen med hjälp av What If-alternativet

1.  On the **Conditional Access - Policies** page, select **What If**. 

    ![Screenshot showing where to select the What if option](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Välj **Användare**, marker din testgästanvändare och välj sedan **Välj**.

    ![Screenshot showing a guest user selected](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Välj **Molnappar**.
4.  Välj **Välj appar** på sidan **Molnappar** och klicka sedan på **Välj**. Välj **Microsoft Azure-hantering** i listan över program och klicka sedan på **Välj**. 

    ![Screenshot showing the Microsoft Azure Management app selected](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Välj **Klar** på sidan **Molnappar**.
6.  Välj **What If** och verifiera att den nya principen visas under **Utvärderingsresultat** på fliken **Principer som gäller**.

    ![Screenshot showing where to select the What if option](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Test your Conditional Access policy
1.  Använd ditt testanvändarnamn och lösenordet för att logga in på [Azure Portal](https://portal.azure.com/).
2.  Du bör se en begäran om ytterligare autentiseringsmetoder. Observera att det kan ta lite tid innan principen träder i kraft.

    ![Screenshot showing the More information required message](media/tutorial-mfa/mfa-required.png)
 
3.  Logga ut.

## <a name="clean-up-resources"></a>Rensa resurser
When no longer needed, remove the test user and the test Conditional Access policy.
1.  Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2.  Välj **Azure Active Directory** i den vänstra rutan.
3.  Under **Hantera** väljer du **Användare**.
4.  Välj testanvändaren och välj sedan **Ta bort användare**.
5.  Välj **Azure Active Directory** i den vänstra rutan.
6.  Välj **Villkorsstyrd åtkomst** under **Säkerhet**.
7.  Välj snabbmenyn (...) för testprincipen i listan **Principnamn** och välj sedan **Ta bort**. Välj **Ja** för att bekräfta.

## <a name="next-steps"></a>Nästa steg
In this tutorial, you’ve created a Conditional Access policy that requires guest users to use MFA when signing in to one of your cloud apps. Läs mer om att lägga till gästanvändare för samarbete i [Lägga till B2B-samarbetsanvändare för Azure Active Directory i Azure-portalen](add-users-administrator.md).
