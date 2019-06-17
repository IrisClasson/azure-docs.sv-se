---
title: 'Självstudier: Azure Active Directory-integrering med Kantega SSO för JIRA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e97a66a1b21b46649a1206eb522c180e84a0a0d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099154"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Självstudier: Azure Active Directory-integrering med Kantega SSO för JIRA

I den här självstudien får du lära dig hur du integrerar Kantega SSO för JIRA med Azure Active Directory (AD Azure).
Integrera Kantega SSO för JIRA med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Kantega SSO för JIRA.
* Du kan aktivera användarna att vara automatiskt inloggad till Kantega SSO för JIRA (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Kantega SSO för JIRA, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för JIRA enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO för JIRA stöder **SP och IDP** -initierad SSO

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Att lägga till Kantega SSO för JIRA från galleriet

För att konfigurera integrering av Kantega SSO för JIRA i Azure AD, som du behöver lägga till Kantega SSO för JIRA från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kantega SSO för JIRA från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Kantega SSO för JIRA**väljer **Kantega SSO för JIRA** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Kantega SSO för JIRA i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Kantega SSO för JIRA baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Kantega SSO för JIRA upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kantega SSO för JIRA, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Kantega SSO för JIRA enkel inloggning](#configure-kantega-sso-for-jira-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Kantega SSO för JIRA testanvändare](#create-kantega-sso-for-jira-test-user)**  – du har en motsvarighet för Britta Simon i Kantega SSO för JIRA som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Kantega SSO för JIRA:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Kantega SSO för JIRA** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Kantega SSO JIRA domän och URL: er enkel inloggning för information](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Kantega SSO JIRA domän och URL: er enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av Jira-pluginprogrammet, som beskrivs senare i självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. På den **konfigurera Kantega SSO för JIRA** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Konfigurera Kantega SSO för JIRA enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din JIRA lokal server som administratör.

1. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon1.png)

1. I avsnittet Tillägg klickar du på **Hitta nya tillägg**. Sök **Kantega SSO för JIRA (SAML & Kerberos)** och klicka på **installera** knappen för att installera den nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon2.png)

1. Installationen av plugin-programmet startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon3.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon33.png)

1.  Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Nytt plugin-program i listan under **INTEGRERINGAR**. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon35.png)

1. I den **SAML** avsnittet. Välj **Azure Active Directory (Azure AD)** från den **Lägg till identitetsprovider** listrutan.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon4.png)

1. Välj prenumerationsnivå som **grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon5.png)       

1. På den **appegenskaper** avsnittet, gör du följande: 

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon6.png)

    a. Kopiera den **Appidentitets-URI** värde och använda det som **identifierare, svars-URL och inloggnings-URL** på den **SAML grundkonfiguration** avsnitt i Azure-portalen.

    b. Klicka på **Nästa**.

1. På den **Metadata import** avsnittet, gör du följande: 

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon7.png)

    a. Välj **Metadata-filen på datorn**, och ladda upp metadatafilen, som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

1. På den **namn och SSO plats** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon8.png)

    a. Lägg till namnet på identitetsprovider i **namn på identitetsprovider** textrutan (t.ex Azure AD).

    b. Klicka på **Nästa**.

1. Verifiera certifikat för signering och klicka på **nästa**.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon9.png)

1. På den **JIRA användarkonton** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon10.png)

    a. Välj **skapa användare i JIRAS intern katalog om det behövs** och ange rätt namn i gruppen för användare (kan vara flera Nej. av (grupper avgränsade med kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon11.png)

1. På den **kända domäner för Azure AD** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforjira-tutorial/addon12.png)

    a. Välj **kända domäner** från den vänstra panelen på sidan.

    b. Ange domännamnet i den **kända domäner** textrutan.

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för JIRA.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Kantega SSO för JIRA**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Kantega SSO för JIRA**.

    ![Kantega SSO för JIRA länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-jira-test-user"></a>Skapa Kantega SSO för JIRA testanvändare

Om du vill aktivera Azure AD-användare att logga in på JIRA, måste de etableras i JIRA. I Kantega SSO för JIRA är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din JIRA lokal server som administratör.

1. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/kantegassoforjira-tutorial/user1.png) 

1. Under **Användarhantering** fliken avsnittet **skapa användare**.

    ![Lägga till medarbetare](./media/kantegassoforjira-tutorial/user2.png) 

1. I dialogrutan **”Skapa ny användare”** utför du följande steg:

    ![Lägga till medarbetare](./media/kantegassoforjira-tutorial/user3.png) 

    a. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Klicka på **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Kantega SSO för JIRA panel i åtkomstpanelen, bör det vara loggas in automatiskt till Kantega SSO för JIRA som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
