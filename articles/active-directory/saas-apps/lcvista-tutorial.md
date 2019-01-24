---
title: 'Självstudier: Azure Active Directory-integrering med LCVista | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LCVista.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8db80d6e-3275-419f-aa39-6115a7bc9800
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 12e3719cb292ef0f2132597e6b849f79f596ff7c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827813"
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Självstudier: Azure Active Directory-integrering med LCVista

I den här självstudien får du lära dig hur du integrerar LCVista med Azure Active Directory (AD Azure).

Integrera LCVista med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LCVista
- Du kan aktivera användarna att automatiskt få loggat in på LCVista (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LCVista, behöver du följande objekt:

- En Azure AD-prenumeration
- En LCVista enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LCVista från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lcvista-from-the-gallery"></a>Att lägga till LCVista från galleriet
För att konfigurera integrering av LCVista i Azure AD, som du behöver lägga till LCVista från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LCVista från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **LCVista**.

    ![Skapa en Azure AD-användare för testning](./media/lcvista-tutorial/tutorial_lcvista_search.png)

1. I resultatpanelen väljer **LCVista**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/lcvista-tutorial/tutorial_lcvista_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LCVista baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i LCVista är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i LCVista upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i LCVista.

Om du vill konfigurera och testa Azure AD enkel inloggning med LCVista, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare LCVista](#creating-a-lcvista-test-user)**  – du har en motsvarighet för Britta Simon i LCVista som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LCVista program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LCVista:**

1. I Azure-portalen på den **LCVista** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_samlbase.png)

1. På den **LCVista domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.lcvista.com/rainier/login`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.lcvista.com` 
     
    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och inloggnings-URL. Kontakta [LCVista klienten supportteamet](https://lcvista.com/contact) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_general_400.png)
    
1. På den **LCVista Configuration** klickar du på **konfigurera LCVista** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** och **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_configure.png) 

1.  Logga in på ditt LCVista program som administratör.

1. I den **SAML-Config** avsnittet, kontrollera den **aktivera SAML-inloggningen** och ange information som vi nämnde i bilden nedan. 

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_config.png)

    a. Klistra in den **utfärdar-URL** som du har kopierat från Azure AD i den **entitets-ID** avsnittet. 

    b. Klistra in den **enkel inloggnings-URL för** som du har kopierat från Azure AD i den **URL** avsnittet.

    c. Från Metadata (XML) som du har hämtat från Azure-portalen, Kopiera värdet **X509Certificate** och klistra in den i den **x509 certifikat** avsnittet.

    d. I den **förnamn attributet** textrutan klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. I den **senaste namnattributet** textrutan klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. I den **e-attributet** textrutan klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. I den **användarnamnattribut** textrutan klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Spara inställningarna genom att klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/lcvista-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/lcvista-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/lcvista-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/lcvista-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lcvista-test-user"></a>Skapa en LCVista testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i LCVista. Du måste be [LCVista klienten supportteamet](https://lcvista.com/contact) att lägga till användare i LCVista-programmet. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LCVista.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon LCVista, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **LCVista**.

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen. Klicka på panelen LCVista i åtkomstpanelen, omdirigeras du till organisation logga på sidan. Efter genomförd inloggning du kommer att loggat in på ditt LCVista program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lcvista-tutorial/tutorial_general_01.png
[2]: ./media/lcvista-tutorial/tutorial_general_02.png
[3]: ./media/lcvista-tutorial/tutorial_general_03.png
[4]: ./media/lcvista-tutorial/tutorial_general_04.png

[100]: ./media/lcvista-tutorial/tutorial_general_100.png

[200]: ./media/lcvista-tutorial/tutorial_general_200.png
[201]: ./media/lcvista-tutorial/tutorial_general_201.png
[202]: ./media/lcvista-tutorial/tutorial_general_202.png
[203]: ./media/lcvista-tutorial/tutorial_general_203.png

