---
title: 'Självstudier: Azure Active Directory-integrering med LaunchDarkly | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och LaunchDarkly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3f0671bc-f93f-496e-b465-b9ce8c6633fa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de59ce587facb94728e0652f47c7cdfce0ab4388
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098430"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Självstudier: Azure Active Directory-integrering med LaunchDarkly | Microsoft Docs

I den här självstudien får du lära dig hur du integrerar LaunchDarkly med Azure Active Directory (Azure AD).
Integrera LaunchDarkly med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till LaunchDarkly.
* Du kan låta dina användare automatiskt loggas in på LaunchDarkly (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med LaunchDarkly så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* LaunchDarkly-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LaunchDarkly har stöd för **SP och IDP**-initierad enkel inloggning
* LaunchDarkly stöder **just-in-time**-användaretablering

## <a name="adding-launchdarkly-from-the-gallery"></a>Lägga till LaunchDarkly från galleriet

Om du vill konfigurera integrering av LaunchDarkly i Azure AD så behöver du lägga till LaunchDarkly från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LaunchDarkly från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **LaunchDarkly**, väljer **LaunchDarkly** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![LaunchDarkly i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för LaunchDarkly](#configure-launchdarkly-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa en LaunchDarkly-testanvändare](#create-launchdarkly-test-user)** – så att du har en motpart för Britta Simon i LaunchDarkly som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. I [Azure Portal](https://portal.azure.com/), på sidan för **LaunchDarkly**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Information om LaunchDarkly-domän och URL:er med enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du in en URL: `app.launchdarkly.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Du uppdaterar värdet med den faktiska svars-URL:en, som förklaras senare i självstudiekursen. Om du har för avsikt att använda programmet i **IDP**-läge så behöver du lämna fältet **Inloggnings-URL** tomt annars kommer du inte att kunna initiera inloggningen från **IDP**. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://app.launchdarkly.com`

    ![Information om LaunchDarkly-domän och URL:er med enkel inloggning](common/metadata-upload-additional-signon.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

7. I avsnittet **Konfigurera LaunchDarkly** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-launchdarkly-single-sign-on"></a>Konfigurera enkel inloggning med LaunchDarkly

1. I ett annat webbläsarfönster så loggar du in på din LaunchDarkly-företagswebbplats som en administratör.

2. Välj **Kontoinställningar** från den vänstra navigeringspanelen.

    ![LaunchDarkly-konfiguration](./media/launchdarkly-tutorial/configure1.png)

3. Klicka på fliken **Säkerhet**.

    ![LaunchDarkly-konfiguration](./media/launchdarkly-tutorial/configure2.png)

4. Klicka på **ENABLE SSO** och därefter **EDIT SAML CONFIGURATION**.

    ![LaunchDarkly-konfiguration](./media/launchdarkly-tutorial/configure3.png)

5. I avsnittet **Redigera din SAML-konfiguration** utför du följande steg:

    ![LaunchDarkly-konfiguration](./media/launchdarkly-tutorial/configure4.png)

    a. Kopiera **SAML URL för konsumenttjänst** för din instans och klistra in den i svars-URL-textrutan i avsnittet **LaunchDarkly-domän och URL:er** på Azure-portalen.

    b. I textrutan **Inloggnings-URL** klistrar du in värdet för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Öppna det hämtade certifikatet från Azure-portalen till Anteckningar, kopiera innehållet och klistra därefter in det i rutan **X.509-certifikat** eller så kan du direkt överföra certifikatet genom att klicka på **överför en**.

    d. Klicka på **Spara**

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

I det här avsnittet låter du Britta Simon använda enkel inloggning med Azure genom att bevilja åtkomst till LaunchDarkly.

1. I Azure-portalen, väljer du **Företagsprogram**, **Alla program** och därefter **LaunchDarkly**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **LaunchDarkly**.

    ![LaunchDarkly-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-launchdarkly-test-user"></a>Skapa en LaunchDarkly-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i LaunchDarkly. LaunchDarkly stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt LaunchDarkly om det inte finns ännu.

> [!Note]
> Om du behöver skapa en användare manuellt så kontaktar du [supportteamet för LaunchDarkly](mailto:support@launchdarkly.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på LaunchDarkly-panelen i åtkomstpanelen bör du automatiskt loggas in på LaunchDarkly som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
