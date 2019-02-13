---
title: 'Självstudier: Azure Active Directory-integrering med join.me | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och join.me.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cda5ea0d-3270-4ba5-ad81-4df108eaad12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61520994bdeeab75b6d26731dee9af15b4ccda6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209548"
---
# <a name="tutorial-azure-active-directory-integration-with-joinme"></a>Självstudier: Azure Active Directory-integrering med join.me

I den här självstudien får du lära dig hur du integrerar join.me med Azure Active Directory (AD Azure).

Integrera join.me med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till join.me.
- Du kan aktivera användarna att automatiskt få loggat in på join.me (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med join.me, behöver du följande objekt:

- En Azure AD-prenumeration
- En join.me enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till join.me från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-joinme-from-the-gallery"></a>Att lägga till join.me från galleriet
För att konfigurera integrering av join.me i Azure AD, som du behöver lägga till join.me från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till join.me från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/joinme-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/joinme-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/joinme-tutorial/a_new_app.png)

4. I sökrutan skriver **join.me**väljer **join.me** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/joinme-tutorial/tutorial_joinme_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med join.me baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i join.me är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i join.me upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med join.me, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare join.me](#create-a-joinme-test-user)**  – du har en motsvarighet för Britta Simon i join.me som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt join.me program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med join.me:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **join.me** application integration markerar **enkel inloggning**.

    ![image](./media/joinme-tutorial/b1_b2_select_sso.png)

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![image](./media/joinme-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/joinme-tutorial/b1_b2_saml_sso.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/joinme-tutorial/b1-domains_and_urlsedit.png)

5. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

     ![image](./media/joinme-tutorial/tutorial_joinme_url.png)
 
6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och spara den på din dator.

    ![image](./media/joinme-tutorial/certificatebase64.png) 

7. Att konfigurera enkel inloggning på **join.me** sida, som du behöver skicka den **Appfederationsmetadata** som du har kopierat från Azure portal för att [join.me supportteamet](https://help.join.me/s/?language). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/joinme-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/joinme-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/joinme-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
  
### <a name="create-a-joinme-test-user"></a>Skapa en join.me testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i join.me. Arbeta med [join.me supportteamet](https://help.join.me/s/?language) att lägga till användare i join.me-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till join.me.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/joinme-tutorial/d_all_applications.png)

2. I listan med program väljer **join.me**.

    ![image](./media/joinme-tutorial/tutorial_joinme_app.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/joinme-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/joinme-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen join.me i åtkomstpanelen du bör få automatiskt loggat in på ditt join.me program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

