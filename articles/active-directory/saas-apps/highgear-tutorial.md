---
title: 'Självstudier: Azure Active Directory-integrering med HighGear | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7aedf90ead4b21bab661bdaf85316cd648fd75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101259"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Självstudier: Azure Active Directory-integrering med HighGear

I den här självstudien lär du dig att integrera HighGear med Azure Active Directory (AD Azure).
Integreringen av HighGear med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till HighGear.
* Du kan göra så att dina användare automatiskt loggas in på HighGear (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med HighGear behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Ett HighGear-system med en Enterprise-licens eller en obegränsad licens

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien lär du dig att konfigurera och testa enkel inloggning med Azure AD i en testmiljö.

* HighGear stöder **SP- and IdP**-initierad enkel inloggning

## <a name="adding-highgear-from-the-gallery"></a>Lägga till HighGear från galleriet

För att konfigurera integreringen av HighGear med Azure AD måste du lägga till SAML SSO for HighGear från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HighGear från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **HighGear**, väljer **HighGear** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![HighGear i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet lär du dig att konfigurera och testa enkel inloggning för Azure AD med HighGear-systemet baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i HighGear-systemet upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med HighGear-systemet behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för HighGear](#configure-highgear-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på HighGear-programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa HighGear-testanvändare](#create-highgear-test-user)** – för att ha en motsvarighet för Britta Simon i HighGear som är länkad till en Azure AD-representation av användaren. 
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet lär du dig att aktivera enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med HighGear-systemet:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **HighGear**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen **Redigeringa** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med HighGear-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** klistrar du in det värde för **entitets-ID för tjänstleverantör** som finns på sidan Inställningar för enkel inloggning i HighGear-systemet.

    ![Fält med entitets-ID för tjänstleverantör](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Du måste logga in på ditt HighGear-system för att komma åt sidan Inställningar för enkel inloggning. När du har loggat in för du musen över fliken Administration i HighGear och klickar på menykommandot Inställningar för enkel inloggning.
    
    ![Menyalternativet Inställningar för enkel inloggning](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. I textrutan **svars-URL** klistrar du in värdet för **URL:en för konsumenttjänst för försäkran (ACS)** från sidan Inställningar för enkel inloggning i HighGear-systemet.

    ![Fältet med URL för konsumenttjänst för försäkran (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     ![Information om enkel inloggning med HighGear-domäner och -URL:er](common/metadata-upload-additional-signon.png)

     I textrutan **Inloggnings-URL** klistrar du in det värde för **entitets-ID för tjänstleverantör** som finns på sidan Inställningar för enkel inloggning i HighGear-systemet. (Detta entitets-ID är även bas-URL för det HighGear-system som ska användas för SP-initierad inloggning.)

    ![Fält med entitets-ID för tjänstleverantör](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk identifierare, svars-URL och inloggnings-URL från sidan **Inställningar för enkel inloggning** i HighGear-systemet. Om du behöver hjälp kan du kontakta [supportteamet för HighGear](mailto:support@highgear.com).

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **Certifikat (Base64)** och spara det på datorn. Du behöver det i ett senare steg i konfigurationen av enkel inloggning.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera HighGear** antecknar du platsen för följande URL:er.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL. Du behöver det här värdet i steg 2 under **Konfigurera enkel inloggning för HighGear** nedan.

    b. Azure AD-identifierare. Du behöver det här värdet i steg 3 under **Konfigurera enkel inloggning för HighGear** nedan.

    c. Utloggnings-URL. Du behöver det här värdet i steg 4 under **Konfigurera enkel inloggning för HighGear** nedan.

### <a name="configure-highgear-single-sign-on"></a>Konfigurera enkel inloggning för HighGear

För att konfigurera HighGear för enkel inloggning loggar du in på HighGear-systemet. När du har loggat in för du musen över fliken Administration i HighGear och klickar på menykommandot Inställningar för enkel inloggning.

![Menyalternativet Inställningar för enkel inloggning](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. I **Namn på identitetsprovider** anger du en kort beskrivning som ska visas i knappen för enkel inloggning för HighGear på inloggningssidan. Exempel: Azure AD

2. I fältet med **URL för enkel inloggning (SSO)** klistrar du in värdet från det fält med **inloggnings-URL** som finns i avsnittet **Konfigurera HighGear** i Azure.

3. I fältet med **SAML-entitets-ID** klistrar du in värdet från det fält med **Azure AD-identifierare** som finns i avsnittet **Konfigurera HighGear** i Azure.

4. I fältet med **URL för enkel utloggning (SLO)** klistrar du in värdet från det fält med **utloggnings-URL** som finns i avsnittet **Konfigurera HighGear** i Azure.

5. Använd Anteckningar för att öppna det certifikat som du laddade ned från avsnittet **SAML-signeringscertifikat** i Azure. Du bör ha laddat ned **Certifikat (Base64)** -formatet. Kopiera innehållet i certifikatet från Anteckningar och klistra in det i fältet **Certifikat för identitetsprovider** i HighGear.

6. Skicka ett e-postmeddelande till [supportteamet för HighGear](mailto:support@highgear.com) och begär ett HighGear-certifikat. Följ de instruktioner som du får från dem och fyll i fälten **HighGear-certifikat** och **HighGear-certifikatlösenord**.

7. Klicka på knappen **Spara** för att spara konfigurationen av enkel inloggning för HighGear.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till HighGear.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **G HighGear**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **HighGear**.

    ![HighGear-länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-highgear-test-user"></a>Skapa HighGear-testanvändare

För att skapa en HighGear-testanvändare för testning av din konfiguration av enkel inloggning loggar du in på HighGear-systemet.

1. Klicka på knappen **Skapa ny kontakt**.

    ![Knappen Skapa ny kontakt](media/highgear-tutorial/create-new-contact-button.png)

    En meny visas där du kan välja vilken typ av kontakt som du vill skapa.

2. Klicka på menykommandot **Individual** (Enskild) för att skapa en HighGear-användare.

    Ett fönster dras ut till höger där du kan skriva in uppgifterna för den nya användaren.  
    ![Formuläret Ny kontakt](media/highgear-tutorial/new-contact-form.png)

3. I fältet **Namn** skriver du ett namn för kontakten. Exempel: Britta Simon

4. Klicka på menyn **Fler alternativ** och välj menykommandot **Kontoinformation**.

    ![Klicka på menykommandot Kontoinformation](media/highgear-tutorial/account-info-menu-item.png)

5. Ange fältet **Can Log In** (Kan logga in) till Ja.

    Även fältet **Aktivera enkel inloggning** anges automatiskt till Ja.

6. I fältet **Single Sign-On User Id** (Användar-ID för enkel inloggning) skriver du ID för användaren. Exempel: BrittaSimon@contoso.com

    Avsnittet Kontoinformation bör nu se ut ungefär så här:  
    ![Det färdiga avsnittet Kontoinformation](media/highgear-tutorial/finished-account-info-section.png)

7. För att spara kontakten klickar du på knappen **Spara** längst ned i fönstret.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på HighGear-panelen i åtkomstpanelen bör du automatiskt loggas in på HighGear som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

