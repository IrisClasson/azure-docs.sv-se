---
title: 'Självstudie: Azure Active Directory integrering med SpaceIQ | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpaceIQ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 643d2481c171ae58a9d105d3dd7c53c251c2c41f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545037"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Självstudie: Azure Active Directory integrering med SpaceIQ

I den här självstudien får du lära dig hur du integrerar SpaceIQ med Azure Active Directory (Azure AD).
Genom att integrera SpaceIQ med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SpaceIQ.
* Du kan göra det möjligt för användarna att logga in automatiskt till SpaceIQ (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SpaceIQ behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SpaceIQ-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SpaceIQ stöder **IDP** INITIERAd SSO

## <a name="adding-spaceiq-from-the-gallery"></a>Lägga till SpaceIQ från galleriet

Om du vill konfigurera integreringen av SpaceIQ i Azure AD måste du lägga till SpaceIQ från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SpaceIQ från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SpaceIQ**, väljer **SpaceIQ** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![SpaceIQ i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SpaceIQ baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SpaceIQ upprättas.

Om du vill konfigurera och testa enkel inloggning med SpaceIQ i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SpaceIQ-enkel inloggning](#configure-spaceiq-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SpaceIQ test User](#create-spaceiq-test-user)** – om du vill ha en motsvarighet till Britta Simon i SpaceIQ som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SpaceIQ i Azure AD:

1. Välj **enkel inloggning**på sidan **SpaceIQ** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning för SpaceIQ-domän och URL: er](common/idp-intiated.png)

    a. Skriv webb adressen i text rutan **identifierare** : `https://api.spaceiq.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Uppdatera värdena med den faktiska svars-URL: en och identifieraren som beskrivs senare i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SpaceIQ** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-spaceiq-single-sign-on"></a>Konfigurera SpaceIQ enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga sedan in på din SpaceIQ-miljö som administratör.

1. När du är inloggad klickar du på pussl-tecknet längst upp till höger och sedan på **integreringar**

    ![Kontoinställningar](./media/spaceiq-tutorial/setting1.png) 

1. Under **all etablering & SSO**, klickar du på panelen **Azure** för att lägga till en instans av Azure som IDP.

    ![SAML-ikon](./media/spaceiq-tutorial/setting2.png)

1. I dialog rutan **SSO** utför du följande steg:

    ![Inställningar för SAML-autentisering](./media/spaceiq-tutorial/setting3.png)

    a. I rutan **URL för SAML-utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som kopierats från fönstret Azure AD Application Configuration.

    b. Kopiera **slut punkts-URL: en för SAML-motanrop (skrivskyddad)** och klistra in värdet i rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    c. Kopiera värdet för **SAML Målgrupps-URI (skrivskyddat)** och klistra in värdet i rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    d. Öppna den hämtade certifikat filen i anteckningar, kopiera innehållet och klistra in det i rutan **X. 509-certifikat** .

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SpaceIQ.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SpaceIQ**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SpaceIQ**.

    ![SpaceIQ-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-spaceiq-test-user"></a>Skapa SpaceIQ test användare

I det här avsnittet skapar du en användare som heter Britta Simon i SpaceIQ. Arbets [SpaceIQ support team](mailto:eng@spaceiq.com)   för att lägga till användare i SpaceIQ-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SpaceIQ på åtkomst panelen, bör du loggas in automatiskt på den SpaceIQ som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

