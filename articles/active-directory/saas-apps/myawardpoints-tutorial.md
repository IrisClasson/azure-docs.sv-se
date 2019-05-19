---
title: 'Självstudier: Azure Active Directory-integrering med min Award punkter upp Sub/Top-teamet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mina Award punkter upp Sub/Top-teamet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: bfb858930bef87239021d049b59c282197bb49ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65871484"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Självstudier: Azure Active Directory-integrering med min Award punkter upp Sub/Top-teamet

I den här självstudien får lära du att integrera Mina Award punkter upp Sub/Top-teamet med Azure Active Directory (AD Azure).
Integrera Mina Award punkter upp Sub/Top-teamet med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till min Award punkter upp Sub/Top-teamet.
* Du kan aktivera användarna att vara automatiskt inloggad till min Award punkter upp Sub/Top-teamet (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med min Award punkter upp Sub/Top-teamet, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Min Award punkter upp Sub/Top-teamet enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för min Award punkter upp Sub/Top-teamet **SP** -initierad SSO

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Att lägga till min Award punkter upp Sub/Top-teamet från galleriet

För att konfigurera integrering av Mina Award punkter upp Sub/Top-teamet i Azure AD, som du behöver lägga till min Award punkter upp Sub/Top-teamet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till min Award punkter upp Sub/Top-teamet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Mina Award punkter upp Sub/Top-teamet**väljer **Mina Award punkter upp Sub/Top-teamet** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Min Award punkter upp Sub/Top-teamet i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med min Award punkter upp Sub/Top-teamet baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Mina Award punkter upp Sub/Top-teamet ska upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med min Award punkter upp Sub/Top-teamet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **Konfigurera mina Award punkter upp Sub/upp Team enkel inloggning** – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **Skapa min Award punkter upp Sub/Top-teamet testanvändare** – du har en motsvarighet för Britta Simon i Mina Award punkter upp Sub/Top-teamet som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med min Award punkter upp Sub/Top-teamet, utför du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Mina Award punkter upp Sub/Top-teamet** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Min Award punkter upp Sub/upp Team domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Du får den `<Azure AD Identifier>` värdet i senare steg i den här självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **Konfigurera mina Award punkter upp Sub/Top-teamet** avsnittet, kopiera den lämpliga URL: er enligt dina behov. 

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    >[!NOTE]
    >Lägg till den kopierade Azure AD-ID-värde med URL: en i stället för inloggning `<Azure AD Identifier>` i den **SAML grundkonfiguration** avsnitt i Azure-portalen.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Konfigurera mina Award Points viktigaste Sub/upp Team enkel inloggning

Att konfigurera enkel inloggning på **Mina Award punkter upp Sub/Top-teamet** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [Mina Award Punkter upp Sub/upp Team-supportteamet](mailto:myawardpoints@biworldwide.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till min Award punkter upp Sub/Top-teamet.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Mina Award punkter upp Sub/Top-teamet**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Mina Award punkter upp Sub/Top-teamet**.

    ![Länken Mina Award punkter upp Sub/Top-teamet i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Skapa min Award punkter upp Sub/Top-teamet testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Mina Award punkter upp Sub/Top-teamet. Arbeta med [Mina Award punkter upp Sub/Top-teamet supportteamet](mailto:myawardpoints@biworldwide.com) att lägga till användare i min Award punkter upp Sub/Top-teamet-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mina Award punkter upp Sub/Top-teamet på åtkomstpanelen, bör det vara loggas in automatiskt till min Award punkter upp Sub/Top-teamet som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
