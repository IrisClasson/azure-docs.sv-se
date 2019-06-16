---
title: 'Självstudier: Azure Active Directory-integrering med Cezanne HR Software | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78d259c0354a1519fa57633a68a1dcfa5a183890
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105703"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Självstudier: Azure Active Directory-integrering med Cezanne HR Software

I den här självstudien lär du dig att integrera Cezanne HR Software med Azure Active Directory (AD Azure).
Integreringen av Cezanne HR Software med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Cezanne HR Software.
* Du kan göra så att dina användare loggas in automatiskt på Cezanne HR Software (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Cezanne HR Software behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cezanne HR Software-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cezanne HR Software har stöd för **SP**-initierad enkel inloggning

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Lägga till Cezanne HR Software från galleriet

För att konfigurera integreringen av Cezanne HR Software i Azure AD måste du lägga till Cezanne HR Software från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cezanne HR Software från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cezanne HR Software**, väljer **Cezanne HR Software** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Cezanne HR Software i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Cezanne HR Software baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cezanne HR Software upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Cezanne HR Software behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cezanne HR Software](#configure-cezanne-hr-software-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Cezanne HR Software-testanvändare](#create-cezanne-hr-software-test-user)** – för att ha en motsvarighet för Britta Simon i Cezanne HR Software som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Cezanne HR Software:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Cezanne HR Software** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Cezanne HR Software-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [kundsupporten för Cezanne HR Software](https://cezannehr.com/services/support/) och be om dessa värden.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Cezanne HR Software** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurera enkel inloggning för Cezanne HR Software

1. Logga in på din Cezanne HR Software-klientorganisation som administratör i ett annat webbläsarfönster.

2. I det vänstra navigeringsfönstret klickar du på **System Setup** (Systemkonfiguration). Gå till **Säkerhetsinställningar**. Gå sedan till **Single Sign-On Configuration** (Konfiguration av enkel inloggning).

    ![Konfigurera enkel inloggning på appsidan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. I panelen **Allow users to log in using the following Single Sign-On (SSO) Service** (Tillåt användare att logga in med följande tjänst för enkel inloggning (SSO)) markerar du rutan **SAML 2.0** och väljer alternativet **Avancerad konfiguration**.

    ![Konfigurera enkel inloggning på appsidan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Klicka på knappen **Lägg till ny**.

    ![Konfigurera enkel inloggning på appsidan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Utför följande steg i avsnittet **SAML 2.0 IDENTITY PROVIDERS** (SAML 2.0-identitetsprovidrar).

    ![Konfigurera enkel inloggning på appsidan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Ange namnet på din identitetsprovider som **visningsnamnet**.

    b. I textrutan **Entity Identifier** (Entitetsidentifierare) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. Ändra **SAML-bindningen** till ”POST”.

    d. I textrutan **Security Token Service Endpoint** (Tjänstslutpunkt för säkerhetstoken) klistrar du in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen.

    e. I User ID Attribute Name (Attributnamn för användar-ID) textrutan anger du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Klicka på ikonen **Ladda upp** för att ladda upp det nedladdade certifikatet från Azure-portalen.

    g. Klicka på knappen **OK**.

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning på appsidan](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Cezanne HR Software.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cezanne HR Software**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Cezanne HR Software**.

    ![Länken för Cezanne HR Software i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cezanne-hr-software-test-user"></a>Skapa Cezanne HR Software-testanvändare

För att Azure AD-användare ska kunna logga in i Cezanne HR Software måste de etableras till Cezanne HR Software. När det gäller Cezanne HR Software är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din Cezanne HR Software-företagswebbplats som administratör.

2. I det vänstra navigeringsfönstret klickar du på **System Setup** (Systemkonfiguration). Gå till **Hantera användare**. Gå sedan till **Lägg till ny användare**.

    ![Ny användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Ny användare")

3. I avsnittet **PERSON DETAILS** (Personuppgifter) utför du stegen nedan:

    ![Ny användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Ny användare")

    a. Ange **Intern användare** till AV.

    b. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.  

    c. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    d. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

4. I avsnittet **Kontoinformation** utför du följande steg:

    ![Ny användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Ny användare")

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    c. Välj **HR Professional** som **Säkerhetsroll**.

    d. Klicka på **OK**.

5. Gå till fliken **Enkel inloggning** och markera **Lägg till ny** i området **SAML 2.0 Identifiers** (SAML 2.0-identifierare).

    ![Användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Användare")

6. Välj din identitetsprovider för **Identitetsprovider**. I textrutan för **Användaridentifierare** anger du e-postadressen för kontot Britta Simon.

    ![Användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Användare")

7. Klicka på **spara** knappen.

    ![Användare](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Användare")

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Cezanne HR Software-panelen i åtkomstpanelen bör du automatiskt loggas in på Cezanne HR Software som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
