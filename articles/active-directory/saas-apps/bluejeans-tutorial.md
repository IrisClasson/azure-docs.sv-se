---
title: 'Självstudier: Azure Active Directory-integrering med BlueJeans | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0308fbb103fb06bd2dffe0a442346a3fc4f7db62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106161"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Självstudier: Azure Active Directory-katalogintegrering med BlueJeans

I den här självstudien får du lära dig hur du integrerar BlueJeans med Azure Active Directory (AD Azure).
När du integrerar BlueJeans med Azure AD får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till BlueJeans.
* Du kan göra så att dina användare automatiskt loggas in på BlueJeans (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med BlueJeans behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* BlueJeans-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* BlueJeans stöder **SP**-initierad enkel inloggning

* BlueJeans stöder [**automatisk** användaretablering](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>Lägga till BlueJeans från galleriet

För att konfigurera integreringen av BlueJeans i Azure AD måste du lägga till BlueJeans från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BlueJeans från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **BlueJeans**, väljer **BlueJeans** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![BlueJeans i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med BlueJeans baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i BlueJeans upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med BlueJeans måste du utföra följande komponenter:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för BlueJeans](#configure-bluejeans-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare för BlueJeans](#create-bluejeans-test-user)** – för att få en motsvarighet till Britta Simon i BlueJeans som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med BlueJeans:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **BlueJeans** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. I den **SAML grundkonfiguration** dialogrutan Ange följande värden:

    ![BlueJeans-domän och URL:er med information om enkel inloggning](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - I textrutan **Identifierare** skriver du följande: `http://samlsp.bluejeans.com`
    
   - I den **inloggnings-URL** text skriver startsida sid-URL du fått av BlueJeans (för att få det här värdet kan du kontakta den [BlueJeans klienten supportteamet](https://support.bluejeans.com/contact)): `https://<companyname>.bluejeans.com`
    
   - Klicka på **Spara**.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera BlueJeans** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-bluejeans-single-sign-on"></a>Konfigurera enkel inloggning för BlueJeans

1. I ett annat webbläsarfönster, loggar du in din **BlueJeans** företagets plats som administratör.

2. Gå till **ADMIN \>GRUPPINSTÄLLNINGAR \> SÄKERHET**.

    ![Admin](./media/bluejeans-tutorial/ic785868.png "Admin")

3. I avsnittet **SÄKERHET** utför du följande steg:

    ![SAML enkel inloggning](./media/bluejeans-tutorial/ic785869.png "SAML enkel inloggning")

    a. Välj **SAML Single Sign On** (SAML enkel inloggning).

    b. Välj **aktivera automatisk etablering**.

4. Gå vidare med följande steg:

    ![Certifikatsökväg](./media/bluejeans-tutorial/ic785870.png "Certifikatsökväg")

    a. Klicka på **Välj fil** för att ladda upp det base-64-kodade certifikatet som du har laddat ned från Azure-portalen.

    b. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    c. I textrutan **Password Change URL** (URL för Byt lösenord) klistrar du in värdet för **URL:en för Byt lösenord** som du har kopierat från Azure-portalen.

    d. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

5. Gå vidare med följande steg:

    ![Spara ändringar](./media/bluejeans-tutorial/ic785874.png "Spara ändringar")

    a. I den **användar-Id** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. I textrutan **E-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon\@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska göra det möjligt för Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till BlueJeans.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **BlueJeans**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **BlueJeans**.

    ![Länken BlueJeans i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bluejeans-test-user"></a>Skapa BlueJeans-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i BlueJeans. BlueJeans stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](bluejeans-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **BlueJeans** företagets plats som administratör.

2. Gå till **ADMIN \> HANTERA ANVÄNDARE \> LÄGG TILL ANVÄNDARE**.

    ![Admin](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > Fliken **LÄGG TILL ANVÄNDARE** är endast tillgänglig om **Aktivera automatisk etablering** är avmarkerat på fliken **SÄKERHET**.

3. I avsnittet **LÄGG TILL ANVÄNDARE** utför du följande steg:

    ![Lägg till användare](./media/bluejeans-tutorial/ic785886.png "Lägg till användare")

    a. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    b. I textrutan **Efternamn** anger du efternamnet på användaren som **Simon**.

    c. I textrutan för att **välja ett BlueJeans-användarnamn** anger du användarnamnet för användaren, som **Brittasimon**

    d. I textrutan **Skapa ett lösenord** anger du ditt lösenord.

    e. I textrutan **Företag** skriver du ditt företag.

    f. I **e-postadress** text, ange den e-postadressen för användaren som `brittasimon\@contoso.com`.

    g. I textrutan för att **skapa ett ID för BlueJeans-möte** anger du ditt mötes-ID.

    h. I textrutan för att **välja ett lösenord för moderator** anger du ditt lösenord.

    i. Klicka på **FORTSÄTT**.

    ![Lägg till användare](./media/bluejeans-tutorial/ic785887.png "Lägg till användare")

    J. Klicka på **LÄGG TILL ANVÄNDARE**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa BlueJeans-användarkonton som tillhandahålls av BlueJeans för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på BlueJeans-panelen i åtkomstpanelen bör du automatiskt loggas in på BlueJeans som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
