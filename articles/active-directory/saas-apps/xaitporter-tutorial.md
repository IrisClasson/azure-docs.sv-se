---
title: 'Självstudier: Azure Active Directory-integrering med XaitPorter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 8652073eb3d7d154958566b68fb6e27c35d8da30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086526"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Självstudier: Azure Active Directory-integrering med XaitPorter

I den här självstudien får du lära dig hur du integrerar XaitPorter med Azure Active Directory (AD Azure).
Integrera XaitPorter med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till XaitPorter.
* Du kan aktivera användarna att vara automatiskt inloggad till XaitPorter (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med XaitPorter, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* XaitPorter enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för XaitPorter **SP** -initierad SSO

## <a name="adding-xaitporter-from-the-gallery"></a>Att lägga till XaitPorter från galleriet

För att konfigurera integrering av XaitPorter i Azure AD, som du behöver lägga till XaitPorter från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till XaitPorter från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **XaitPorter**väljer **XaitPorter** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![XaitPorter i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med XaitPorter baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i XaitPorter upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med XaitPorter, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera XaitPorter Single Sign-On](#configure-xaitporter-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare XaitPorter](#create-xaitporter-test-user)**  – du har en motsvarighet för Britta Simon i XaitPorter som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med XaitPorter:

1. I den [Azure-portalen](https://portal.azure.com/)på den **XaitPorter** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![XaitPorter domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.xaitporter.com/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [XaitPorter klienten supportteamet](https://www.xait.com/support/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

6. Ange den **IP-adress** eller **Appfederationsmetadata** till den [SmartRecruiters supportteam](https://www.smartrecruiters.com/about-us/contact-us/), så att XaitPorter kan se till att IP-adressen kan nås från din Konfigurera listan över godkända på sin sida XaitPorter-instans. 

### <a name="configure-xaitporter-single-sign-on"></a>Konfigurera XaitPorter Single Sign-On

1. Om du vill automatisera konfigurationen inom XaitPorter, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet XaitPorter** omdirigerar dig till programmet XaitPorter. Ange administratörsautentiseringsuppgifter för att logga in på XaitPorter därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 6.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera XaitPorter manuellt, öppna ett nytt webbläsarfönster och logga till XaitPorter företagets webbplatsen som administratör och utför följande steg:

4. Klicka på **Admin**.

    ![Konfigurera enkel inloggning](./media/xaitporter-tutorial/user1.png)

5. Välj **hantera enkel inloggning** från den **systeminställningarna** listrutan.

    ![Konfigurera enkel inloggning](./media/xaitporter-tutorial/user2.png)

6. I den **hantera enkel inloggning** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/xaitporter-tutorial/user3.png)

    a. Välj **aktivera enkel inloggning för autentisering**.

    b. I **identitet providerinställningar** textrutan klistra in **Appfederationsmetadata** som du har kopierat från Azure-portalen och klicka på **hämta**.

    c. Välj **Aktivera skapa automatiskt användare**.

    d. Klicka på **OK**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till XaitPorter.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **XaitPorter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **XaitPorter**.

    ![Länken XaitPorter i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-xaitporter-test-user"></a>Skapa XaitPorter testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i XaitPorter. Arbeta med [XaitPorter klienten supportteamet](https://www.xait.com/support/) att lägga till användare i XaitPorter-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen XaitPorter i åtkomstpanelen, bör det vara loggas in automatiskt till XaitPorter som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

