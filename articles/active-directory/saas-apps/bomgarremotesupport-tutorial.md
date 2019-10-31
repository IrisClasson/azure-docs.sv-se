---
title: 'Självstudie: Azure Active Directory integration med Bomgar-fjärrsupport | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bomgar Remote Support.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ea2fb15c426da1198c655efb38c9a298a998d0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158799"
---
# <a name="tutorial-azure-active-directory-integration-with-bomgar-remote-support"></a>Självstudie: Azure Active Directory integration med Bomgar-fjärrsupport

I den här självstudien lär du dig att integrera Bomgar Remote Support med Azure Active Directory (AD Azure).
Integreringen av Bomgar Remote Support med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Bomgar Remote Support.
* Du kan göra så att dina användare automatiskt loggas in på Bomgar Remote Support (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver följande för att konfigurera Azure AD-integrering med Bomgar Remote Support:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Bomgar Remote Support-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bomgar Remote Support har stöd för **SP**-initierad enkel inloggning
* Bomgar Remote Support har stöd för **just-in-time**-användaretablering

## <a name="adding-bomgar-remote-support-from-the-gallery"></a>Lägga till Bomgar Remote Support från galleriet

För att konfigurera integrering av Bomgar Remote Support i Azure AD behöver du lägga till Bomgar Remote Support från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bomgar Remote Support från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Bomgar Remote Support**, väljer **Bomgar Remote Support** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Bomgar Remote Support i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Bomgar Remote Support baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Bomgar Remote Support upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Bomgar Remote Support behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Bomgar Remote Support](#configure-bomgar-remote-support-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Bomgar Remote Support-testanvändare](#create-bomgar-remote-support-test-user)** – för att ha en motsvarighet till Britta Simon i Bomgar Remote Support som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Bomgar Remote Support:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Bomgar Remote Support** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Bomgar Remote Support-domän och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.trafficmanager.net/saml`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.trafficmanager.net`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Bomgar Remote Support](https://www.bomgar.com/docs/index.htm#support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Bomgar Remote Support** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bomgar-remote-support-single-sign-on"></a>Konfigurera enkel inloggning för Bomgar Remote Support

För att konfigurera enkel inloggning på **Bomgar Remote Support**-sidan behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Bomgar Remote Support](https://www.bomgar.com/docs/index.htm#support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Bomgar Remote Support.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Bomgar Remote Support**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Bomgar Remote Support**.

    ![Länken för Bomgar Remote Support i programlistan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bomgar-remote-support-test-user"></a>Skapa Bomgar Remote Support-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Bomgar Remote Support. Bomgar Remote Support har stöd för just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Bomgar Remote Support skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Bomgar Remote Support](https://www.bomgar.com/docs/index.htm#support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Bomgar Remote Support-panelen på åtkomstpanelen bör du automatiskt loggas in i Bomgar Remote Support som du har konfigurerat enkel inloggning för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
