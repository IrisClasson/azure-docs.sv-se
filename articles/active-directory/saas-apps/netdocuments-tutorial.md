---
title: 'Självstudier: Azure Active Directory-integrering med NetDocuments | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 929d5d7a8e2b45aeb4ef71e4599cfcf23be83088
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096606"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Självstudier: Azure Active Directory-integrering med NetDocuments

I den här självstudien får du lära dig hur du integrerar NetDocuments med Azure Active Directory (AD Azure).
Integrera NetDocuments med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till NetDocuments.
* Du kan aktivera användarna att vara automatiskt inloggad till NetDocuments (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med NetDocuments, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* NetDocuments enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för NetDocuments **SP** -initierad SSO

## <a name="adding-netdocuments-from-the-gallery"></a>Att lägga till NetDocuments från galleriet

För att konfigurera integrering av NetDocuments i Azure AD, som du behöver lägga till NetDocuments från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till NetDocuments från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **NetDocuments**väljer **NetDocuments** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![NetDocuments i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med NetDocuments baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i NetDocuments upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med NetDocuments, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera NetDocuments Single Sign-On](#configure-netdocuments-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare NetDocuments](#create-netdocuments-test-user)**  – du har en motsvarighet för Britta Simon i NetDocuments som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med NetDocuments:

1. I den [Azure-portalen](https://portal.azure.com/)på den **NetDocuments** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![NetDocuments domän och URL: er med enkel inloggning för information](common/sp-reply.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och svars-URL. Databas-ID är ett värde som börjar med **CA -** följt av 8 teckenkod som är associerade med din NetDocuments lagringsplats. Du kan kontrollera den [NetDocuments federerad identitet stöddokument](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) för mer information. Du kan också kontakta [NetDocuments klienten supportteamet](https://support.netdocuments.com/hc/) att hämta dessa värden om du har problem med konfiguration med hjälp av ovanstående information. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **konfigurera NetDocuments** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-netdocuments-single-sign-on"></a>Konfigurera NetDocuments Single Sign-On

1. Logga in på webbplatsen NetDocuments företag som en administratör i ett annat webbläsarfönster.

2. Gå till **Admin**.

3. Klicka på **Lägg till och ta bort användare och grupper**.
   
    ![Databasen](./media/netdocuments-tutorial/ic795047.png "lagringsplats")

4. Klicka på **konfigurera avancerade autentiseringsalternativ**.
    
    ![Konfigurera avancerade autentiseringsalternativ](./media/netdocuments-tutorial/ic795048.png "konfigurera avancerade autentiseringsalternativ")

5. På den **federerad identitet** dialogrutan utför följande steg:
   
    ![Federerad Identitty](./media/netdocuments-tutorial/ic795049.png "federerad Identitty")
   
    a. Som **federerad identitet servertyp**väljer **Active Directory Federation Services**.
   
    b. Klicka på **Välj fil**, för att ladda upp den hämtade metadatafilen som du har hämtat från Azure-portalen.
   
    c. Klicka på **OK**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till NetDocuments.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **NetDocuments**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **NetDocuments**.

    ![Länken NetDocuments i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-netdocuments-test-user"></a>Skapa NetDocuments testanvändare

Om du vill aktivera Azure AD-användare att logga in på NetDocuments, måste de etableras i NetDocuments.  
När det gäller NetDocuments är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Registrerar in på din **NetDocuments** företagets plats som administratör.

2. På menyn längst upp klickar du på **Admin**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

3. Klicka på **Lägg till och ta bort användare och grupper**.
   
    ![Databasen](./media/netdocuments-tutorial/ic795047.png "lagringsplats")

4. I den **e-postadress** textrutan skriver du ett giltigt Azure Active Directory-konto du vill etablera och klicka sedan på e-postadress **Lägg till användare**.
   
    ![E-postadress](./media/netdocuments-tutorial/ic795053.png "e-postadress")
   
    >[!NOTE]
    >Azure Active Directory-kontoinnehavare får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktiv. Du kan använda alla andra NetDocuments användare konto verktyg för att skapa eller API: er som tillhandahålls av NetDocuments för att etablera Azure Active Directory användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen NetDocuments i åtkomstpanelen, bör det vara loggas in automatiskt till NetDocuments som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

