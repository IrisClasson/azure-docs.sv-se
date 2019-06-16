---
title: 'Självstudier: Azure Active Directory-integrering med Mercer BenefitsCentral (av MBC) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mercer BenefitsCentral (av MBC).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: 9ae074e8732898524d917f2537023c4b506cff80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097472"
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Självstudier: Azure Active Directory-integrering med Mercer BenefitsCentral (av MBC)

I den här självstudien får du lära dig hur du integrerar Mercer BenefitsCentral (av MBC) med Azure Active Directory (AD Azure).
Integrera Mercer BenefitsCentral (av MBC) med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Mercer BenefitsCentral (av MBC).
* Du kan aktivera användarna att vara automatiskt inloggad till Mercer BenefitsCentral (av MBC) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Mercer BenefitsCentral (av MBC), behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mercer BenefitsCentral (av MBC) enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mercer BenefitsCentral (av MBC) stöder **IDP** -initierad SSO

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Att lägga till Mercer BenefitsCentral (av MBC) från galleriet

För att konfigurera integrering av Mercer BenefitsCentral (av MBC) till Azure AD, som du behöver lägga till Mercer BenefitsCentral (av MBC) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mercer BenefitsCentral (av MBC) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Mercer BenefitsCentral (av MBC)** väljer **Mercer BenefitsCentral (av MBC)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Mercer BenefitsCentral (av MBC) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mercer BenefitsCentral (av MBC) baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Mercer BenefitsCentral (av MBC) ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mercer BenefitsCentral (av MBC), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Mercer BenefitsCentral (av MBC) Single Sign-On](#configure-mercer-benefitscentral-mbc-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Mercer BenefitsCentral (av MBC)](#create-mercer-benefitscentral-mbc-test-user)**  – du har en motsvarighet för Britta Simon i Mercer BenefitsCentral (av MBC) som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Mercer BenefitsCentral (av MBC):

1. I den [Azure-portalen](https://portal.azure.com/)på den **Mercer BenefitsCentral (av MBC)** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Mercer BenefitsCentral (av MBC)-domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `stg.mercerhrs.com/saml2.0`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Kontakta [Mercer BenefitsCentral (av MBC) klient-supportteamet](https://www.mercer.com/contact-us.html) att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **ange upp Mercer BenefitsCentral (av MBC)** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mercer-benefitscentral-mbc-single-sign-on"></a>Konfigurera enkel inloggning för Mercer-BenefitsCentral (av MBC)

Att konfigurera enkel inloggning på **Mercer BenefitsCentral (av MBC)** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [Mercer BenefitsCentral (av MBC) supportteamet](https://www.mercer.com/contact-us.html). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Mercer BenefitsCentral (av MBC).

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Mercer BenefitsCentral (av MBC)** .

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Mercer BenefitsCentral (av MBC)** .

    ![Länken Mercer BenefitsCentral (av MBC) i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mercer-benefitscentral-mbc-test-user"></a>Skapa testanvändare Mercer BenefitsCentral (av MBC)

I det här avsnittet skapar du en användare som kallas Britta Simon i Mercer BenefitsCentral (av MBC). Arbeta med [Mercer BenefitsCentral (av MBC) supportteamet](https://www.mercer.com/contact-us.html) att lägga till användare i Mercer BenefitsCentral (av MBC)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mercer BenefitsCentral (av MBC) i åtkomstpanelen bör du vara loggas in automatiskt till den Mercer BenefitsCentral (av MBC) som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

