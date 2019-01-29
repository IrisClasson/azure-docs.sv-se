---
title: 'Självstudier: Azure Active Directory-integrering med Tangoe kommandot Premiumenheter för mobila | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tangoe kommandot Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: ea272a6d7d045a01c72a7c88c048340b8d11ba83
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152305"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Självstudier: Azure Active Directory-integrering med Tangoe kommandot Premium Mobile

I den här självstudien får du lära dig hur du integrerar Tangoe kommandot Premiumenheter för mobila med Azure Active Directory (AD Azure).

Integrera Tangoe kommandot Premiumenheter för mobila med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tangoe kommandot Premium Mobile
- Du kan aktivera användarna att automatiskt få loggat in på Tangoe kommandot Premium Mobile (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tangoe kommandot Premium Mobile, behöver du följande objekt:

- En Azure AD-prenumeration
- En Tangoe kommandot Premiumenheter för mobila enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Tangoe kommandot Premiumenheter för mobila från galleriet
1. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Lägg till Tangoe kommandot Premiumenheter för mobila från galleriet
För att konfigurera integrering av Tangoe kommandot Premiumenheter för mobila i Azure AD, som du behöver lägga till Tangoe kommandot Premiumenheter för mobila från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Tangoe kommandot Premiumenheter för mobila från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Tangoe kommandot Premiumenheter för mobila**väljer **Tangoe kommandot Premiumenheter för mobila** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Lägg till Tangoe kommandot Premiumenheter för mobila från galleriet ](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tangoe kommandot Premiumenheter för mobila baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Tangoe kommandot Premium Mobile är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Tangoe kommandot Premiumenheter för mobila upprättas.

I Tangoe kommandot Premium Mobile, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tangoe kommandot Premium Mobile, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Tangoe kommandot Premiumenheter för mobila](#create-a-tangoe-command-premium-mobile-test-user)**  – du har en motsvarighet för Britta Simon i Tangoe kommandot Premiumenheter för mobila som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Tangoe kommandot Premiumenheter för mobila program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tangoe kommandot Premium Mobile:**

1. I Azure-portalen på den **Tangoe kommandot Premiumenheter för mobila** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserad inloggning](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. På den **Tangoe kommandot Premium Mobile domän och URL: er** avsnittet, utför följande steg:

    ![Tangoe kommandot Premium mobila domän och URL: er](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och inloggnings-URL. Kontakta [Tangoe kommandot Premium mobila klienten supportteamet](https://www.tangoe.com/contact-us/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Avsnittet för SAML-signeringscertifikat](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Knappen Spara](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. På den **Tangoe Premium Mobile konfiguration** klickar du på **konfigurera Tangoe kommandot Premiumenheter för mobila** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurationsavsnittet för Tangoe kommandot Premium Mobile](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. För att få SSO konfigurerats för ditt program kan du kontakta din [Tangoe kommandot Premium mobila klienten supportteamet](https://www.tangoe.com/contact-us/) och uppge följande:

   - Hämtade metadatafilen
   - Den **SAML entitets-ID**
   - Den **URL för SAML enkel inloggning**
   - Den **utloggnings-URL**

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/tangoe-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Användare och grupper -> alla användare](./media/tangoe-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Lägga till användare](./media/tangoe-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användarsidan](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Skapa en Tangoe kommandot Premiumenheter för mobila användare

I det här avsnittet skapar du en användare som kallas Britta Simon i Tangoe kommandot Premium Mobile. 

Tangoe kommandot Premiumenheter för mobila program måste alla användare som ska etableras i programmet innan du gör för enkel inloggning. Så du arbetet med den [Tangoe kommandot Premium mobila klienten supportteamet](https://www.tangoe.com/contact-us/) att etablera alla användare i programmet. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tangoe kommandot Premium Mobile.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Tangoe kommandot Premium Mobile, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Tangoe kommandot Premiumenheter för mobila**.

    ![Tangoe kommandot Premiumenheter för mobila i applistan](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Tangoe kommandot Premiumenheter för mobila i åtkomstpanelen du bör få automatiskt loggat in på ditt Tangoe kommandot Premiumenheter för mobila program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

