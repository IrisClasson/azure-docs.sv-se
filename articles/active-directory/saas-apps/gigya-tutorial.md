---
title: 'Självstudier: Azure Active Directory-katalogintegrering med Gigya | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Gigya.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 4c9925e11325c87598f90af1b677246eca805e6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101714"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Självstudier: Azure Active Directory-katalogintegrering med Gigya

I den här självstudien lär du dig att integrera Gigya med Azure Active Directory (AD Azure).
När du integrerar Gigya med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till Gigya från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på Gigya (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Gigya behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Gigya-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Gigya stöder **SP**-initierad enkel inloggning

## <a name="adding-gigya-from-the-gallery"></a>Lägga till Gigya från galleriet

För att konfigurera integreringen av Gigya i Azure AD måste du lägga till Gigya från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Gigya från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Gigya**, väljer **Gigya** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Gigya i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Gigya baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Gigya upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Gigya måste du utföra följande uppgifter:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Gigya](#configure-gigya-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Gigya-testanvändare](#create-gigya-test-user)** – för att ha en motsvarighet för Britta Simon i Gigya som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Gigya:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Gigya** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Gigya-domän och webbadresser med information om enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `http://<companyname>.gigya.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [supportteamet för Gigya](https://www.gigya.com/support-policy/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Gigya** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-gigya-single-sign-on"></a>Konfigurera enkel inloggning för Gigya

1. Logga in på Gigya-företagswebbplatsen som administratör.

2. Gå till **Inställningar \>SAML-inloggning** och klicka sedan på knappen **Lägg till**.
   
    ![SAML-inloggning](./media/gigya-tutorial/ic789532.png "SAML-inloggning")

3. Gå till avsnittet **SAML-inloggning** och utför följande steg:
   
    ![SAML-konfiguration](./media/gigya-tutorial/ic789533.png "SAML-konfiguration")
   
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.
   
    b. I textrutan **Issuer**  (Utfärdare) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen. 
   
    c. I textrutan **URL för enkel inloggning** klistrar du in värdet för **Inloggnings-URL** som du kopierade från Azure-portalen.
   
    d. I textrutan för **format för namn-ID** klistrar du in värdet för **formatet på namnidentifieraren** som du har kopierat från Azure-portalen.
   
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in den i textrutan **X.509 Certificate** (X.509-certifikat).
   
    f. Klicka på **Spara inställningar**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Gigya.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Gigya**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Gigya**.

    ![Gigya-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-gigya-test-user"></a>Skapa Gigya-testanvändare

För att Azure AD-användare ska kunna logga in i Gigya måste de etableras till Gigya. I Gigya görs etablering manuellt.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att tillhandahålla ett användarkonto:

1. Logga in på **Gigya**-företagswebbplatsen som administratör.

2. Gå till **Admin \> Hantera användare** och klicka sedan på **Bjud in användare**.
   
    ![Hantera användare](./media/gigya-tutorial/ic789535.png "Hantera användare")

3. Gör följande i dialogrutan Bjud in användare:
   
    ![Bjud in användare](./media/gigya-tutorial/ic789536.png "Bjud in användare")
   
    a. I textrutan **E-post** skriver du e-postalias för ett giltigt Azure Active Directory-konto som du vill etablera.
    
    b. Klicka på **Bjud in användare**.
      
    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.
    > 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Gigya-panelen i åtkomstpanelen bör du automatiskt loggas in på Gigya som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

