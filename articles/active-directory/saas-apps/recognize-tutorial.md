---
title: 'Självstudier: Azure Active Directory-integrering med identifiera | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och identifiera.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 94e0b365d159ef18d7c0e6216ac9f5babb0d6231
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890436"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Självstudier: Azure Active Directory-integrering med identifiera

Lär dig hur du integrerar identifiera med Azure Active Directory (AD Azure) i den här självstudien.
Integrera identifiera med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till identifiera.
* Du kan aktivera användarna att vara automatiskt inloggad att identifiera (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med identifiera, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Identifiera enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Identifiera stöder **SP** -initierad SSO

## <a name="adding-recognize-from-the-gallery"></a>Att lägga till identifiera från galleriet

För att konfigurera integrering av identifiera i Azure AD, som du behöver lägga till identifiera från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till identifiera från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **identifiera**väljer **identifiera** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Identifiera i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med identifiera baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i identifiera upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med identifiera, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera identifiera enkel inloggning](#configure-recognize-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa identifiera testanvändare](#create-recognize-test-user)**  – du har en motsvarighet för Britta Simon i identifiera som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med identifiera:

1. I den [Azure-portalen](https://portal.azure.com/)på den **identifiera** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** om du har **tjänstleverantör metadatafil**, utför följande steg:

    >[!NOTE]
    >Du får den **tjänstleverantör metadatafil** från den **konfigurera identifiera enkel inloggning** avsnittet av självstudiekursen.

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts den **identifierare** värdet hämta fylls i i konfigurationsavsnittet för grundläggande SAML automatiskt.

    ![Identifiera domän och URL: er enkel inloggning för information](common/sp-identifier.png)

     I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Om den **identifierare** värde får inte fylls i automatiskt, du får ID-värde genom att öppna URL: en för Service Provider Metadata från avsnittet Inställningar för enkel inloggning som beskrivs senare i den **konfigurera identifiera enda Inloggning** avsnittet av självstudiekursen. Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [identifiera klienten supportteamet](mailto:support@recognizeapp.com) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. På den **konfigurera identifiera** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-recognize-single-sign-on"></a>Konfigurera identifiera enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din identifiera klient som administratör.

2. I det övre högra hörnet, klickar du på **menyn**. Gå till **företagets administratör**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_000.png)

3. I det vänstra navigeringsfönstret klickar du på **Inställningar**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Utför följande steg på **inställningar för enkel inloggning** avsnittet.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Som **aktivera SSO**väljer **på**.

    b. I den **IDP entitets-ID** textrutan klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
    
    c. I den **mål-url för enkel inloggning** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.
    
    d. I den **Slo-mål-url** textrutan klistra in värdet för **URL för utloggning** som du har kopierat från Azure-portalen. 
    
    e. Öppna din hämtade **certifikat (Base64)** filen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **certifikat** textrutan.
    
    f. Klicka på den **spara inställningarna för** knappen. 

5. Bredvid den **inställningar för enkel inloggning** avsnittet, Kopiera URL-Adressen under **Service Provider Metadata-url**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Öppna den **Metadata-URL-länk** under en tom webbläsare för att hämta för Metadatadokumentet. Kopiera sedan EntityDescriptor value(entityID) från filen och klistra in det i **identifierare** textrutan i **SAML grundkonfiguration** på Azure-portalen.
    
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_004.png)

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till identifiera.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **identifiera**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **identifiera**.

    ![Länken identifiera i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-recognize-test-user"></a>Skapa identifiera testanvändare

För att aktivera Azure AD-användare att logga in på identifiera etableras de i identifiera. När det gäller identifiera är etablering en manuell aktivitet.

Den här appen har inte stöd för SCIM-etablering men har en annan användare sync som etablerar användare. 

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen för Företagsportalen identifiera som administratör.

2. I det övre högra hörnet, klickar du på **menyn**. Gå till **företagets administratör**.

3. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

4. Utför följande steg på **användaren synkronisering** avsnittet.
   
    ![Ny användare](./media/recognize-tutorial/tutorial_recognize_005.png "Ny användare")
   
    a. Som **aktiverad för synkronisering av**väljer **på**.
   
    b. Som **Välj synkronisering providern**väljer **Microsoft / Office 365**.
   
    c. Klicka på **kör synkronisering av användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen identifiera i åtkomstpanelen, bör det vara loggas in automatiskt att identifiera som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

