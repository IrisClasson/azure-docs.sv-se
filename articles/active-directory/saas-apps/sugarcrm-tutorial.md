---
title: 'Självstudier: Azure Active Directory-integrering med socker CRM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och socker CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 5c46b096b1182b3f2a998f992f72e6127dfe0888
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089545"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Självstudier: Azure Active Directory-integrering med socker CRM

I den här självstudien får du lära dig hur du integrerar socker CRM med Azure Active Directory (AD Azure).
Integrera socker CRM med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till socker CRM.
* Du kan aktivera användarna att vara automatiskt inloggad till socker CRM (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med socker CRM, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Socker CRM enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Socker CRM stöder **SP** -initierad SSO

## <a name="adding-sugar-crm-from-the-gallery"></a>Att lägga till socker CRM från galleriet

Om du vill konfigurera integreringen av socker CRM till Azure AD, som du behöver lägga till socker CRM från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till socker CRM från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **socker CRM**väljer **socker CRM** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Socker CRM i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med socker CRM baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i socker CRM upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med socker CRM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för socker-CRM](#configure-sugar-crm-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa socker CRM testanvändare](#create-sugar-crm-test-user)**  – du har en motsvarighet för Britta Simon i socker CRM som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med socker CRM:

1. I den [Azure-portalen](https://portal.azure.com/)på den **socker CRM** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Socker CRM domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [socker CRM-klienten supportteamet](https://support.sugarcrm.com/) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. På den **konfigurera socker CRM** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-sugar-crm-single-sign-on"></a>Konfigurera enkel inloggning för socker-CRM

1. I ett annat webbläsarfönster, loggar du in din socker CRM företagets webbplats som administratör.

1. Gå till **Admin**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. I den **Administration** klickar du på **lösenordshantering**.

    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. Välj **aktivera SAML-autentisering**.

    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. Gör följande i avsnittet **SAML-autentisering**:

    ![SAML-autentisering](./media/sugarcrm-tutorial/ic795891.png "SAML-autentisering")  

    a. I den **inloggnings-URL** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.
  
    b. I den **SLO URL** textrutan klistra in värdet för **URL för utloggning**, som du har kopierat från Azure-portalen.
  
    c. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet i **X.509-certifikat** textrutan.
  
    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till socker CRM.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **socker CRM**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **socker CRM**.

    ![Socker CRM-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sugar-crm-test-user"></a>Skapa socker CRM testanvändare

För att aktivera Azure AD-användare att logga in på socker CRM, måste de etableras till socker CRM. När det gäller socker CRM är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **socker CRM** företagets plats som administratör.

1. Gå till **Admin**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. I den **Administration** klickar du på **Användarhantering**.

    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. Gå till **användare \> Skapa ny användare**.

    ![Skapa ny användare](./media/sugarcrm-tutorial/ic795894.png "Skapa ny användare")

1. På den **användarprofil** fliken, utför följande steg:

    ![Ny användare](./media/sugarcrm-tutorial/ic795895.png "Ny användare")

    * Skriv den **användarnamn**, **efternamn**, och **e-postadress** av en giltig Azure Active Directory-användare i den relaterade textrutor.
  
1. Som **Status**väljer **Active**.

1. Utför följande steg på fliken lösenord:

    ![Ny användare](./media/sugarcrm-tutorial/ic795896.png "Ny användare")

    a. Ange lösenordet i relaterade textrutan.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda något annat socker CRM användaren-konto skapas verktyg eller API: er som tillhandahålls av socker CRM att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen socker CRM på åtkomstpanelen, bör det vara loggas in automatiskt till socker-CRM som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

