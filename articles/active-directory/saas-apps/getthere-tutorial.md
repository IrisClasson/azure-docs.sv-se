---
title: 'Självstudier: Azure Active Directory-integrering med GetThere | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GetThere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 499b7f5761c97f6986ca0291af2242d694b609d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101742"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>Självstudier: Azure Active Directory-integrering med GetThere

I den här självstudien får du lära dig hur du integrerar GetThere med Azure Active Directory (AD Azure).
Integrera GetThere med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till GetThere.
* Du kan aktivera användarna att vara automatiskt inloggad till GetThere (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med GetThere, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* GetThere enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för GetThere **IDP** -initierad SSO

## <a name="adding-getthere-from-the-gallery"></a>Att lägga till GetThere från galleriet

För att konfigurera integrering av GetThere i Azure AD, som du behöver lägga till GetThere från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till GetThere från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **GetThere**väljer **GetThere** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![GetThere i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med GetThere baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i GetThere upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med GetThere, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera GetThere Single Sign-On](#configure-getthere-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare GetThere](#create-getthere-test-user)**  – du har en motsvarighet för Britta Simon i GetThere som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med GetThere:

1. I den [Azure-portalen](https://portal.azure.com/)på den **GetThere** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![GetThere domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL:

    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. I den **svars-URL** text skriver du någon av de nedan URL: er:
    
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |

5. GetThere program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

6. Förutom ovanstående GetThere program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn |  Källattribut |  Namnrymd |
    | ---------------| --------------- | --------------- |
    | Webbplatsnamn | ”Ge värde enligt din organisations” | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | Användarnamn |  user.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I den **Namespace** textrutan skriver attributets namnområde visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. På den **konfigurera GetThere** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-getthere-single-sign-on"></a>Konfigurera GetThere Single Sign-On

Att konfigurera enkel inloggning på **GetThere** sida, som du behöver skicka de hämtade **certifikat (Base64)** och lämpliga kopierade URL: er från Azure portal för att [GetThere supportteamet](mailto:dataintegration@sabre.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till GetThere.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **GetThere**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **GetThere**.

    ![Länken GetThere i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-getthere-test-user"></a>Skapa GetThere testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i GetThere. Arbeta med [GetThere supportteamet](mailto:dataintegration@sabre.com) att lägga till användare i GetThere-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen GetThere i åtkomstpanelen, bör det vara loggas in automatiskt till GetThere som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

