---
title: 'Självstudier: Azure Active Directory-integrering med Cornerstone OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CornerStone OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 550ffb146db9bb644b46467a7feb8c30792f6ad8
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900201"
---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Självstudier: Azure Active Directory-integrering med Cornerstone OnDemand

I den här självstudien får du lära dig hur du integrerar Cornerstone OnDemand med Azure Active Directory (AD Azure).
När du integrerar Cornerstone OnDemand med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Cornerstone OnDemand.
* Du kan göra så att dina användare automatiskt loggas in på Cornerstone OnDemand (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Cornerstone OnDemand behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Cornerstone OnDemand-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cornerstone OnDemand har stöd för **SP**-initierad enkel inloggning
* Cornerstone OnDemand har stöd för [automatisk användaretablering](cornerstone-ondemand-provisioning-tutorial.md)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Lägga till Cornerstone OnDemand från galleriet

För att konfigurera integrering av Cornerstone OnDemand i Azure AD behöver du lägga till Cornerstone OnDemand från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cornerstone OnDemand från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cornerstone OnDemand**, väljer **Cornerstone OnDemand** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Cornerstone OnDemand i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Cornerstone OnDemand baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cornerstone OnDemand upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cornerstone OnDemand måste du utföra följande komponenter:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cornerstone OnDemand](#configure-cornerstone-ondemand-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare för Cornerstone OnDemand](#create-cornerstone-ondemand-test-user)** – för att skapa en motsvarighet till Britta Simon i Cornerstone OnDemand som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Cornerstone OnDemand:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **Cornerstone OnDemand**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Cornerstone OnDemand-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.csod.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company>.csod.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Kontakta [Cornerstone OnDemand-klientens supportteam](mailto:moreinfo@csod.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Cornerstone OnDemand** kopierar du en eller flera lämpliga URL:er, enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cornerstone-ondemand-single-sign-on"></a>Konfigurera enkel inloggning för Cornerstone OnDemand

För att kunna konfigurera enkel inloggning på **Cornerstone OnDemand**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Cornerstone OnDemand-supporten](mailto:moreinfo@csod.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Cornerstone OnDemand.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cornerstone OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Cornerstone OnDemand**.

    ![Länken för Cornerstone OnDemand i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cornerstone-ondemand-test-user"></a>Skapa testanvändare för Cornerstone OnDemand

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i Cornerstone OnDemand. Cornerstone OnDemand stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

För att konfigurera användaretablering skickar du information (t.ex.: namn, e-post) om Azure AD-användaren du vill etablera till [Cornerstone OnDemand-supportteamet](mailto:moreinfo@csod.com).

>[!NOTE]
>Du kan använda andra verktyg eller API:er för att skapa Cornerstone OnDemand-användarkonton som tillhandahålls av Cornerstone OnDemand för att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Cornerstone OnDemand-panelen i åtkomstpanelen bör du automatiskt loggas in på Cornerstone OnDemand som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](https://docs.microsoft.com/azure/active-directory/saas-apps/cornerstone-ondemand-provisioning-tutorial) 

