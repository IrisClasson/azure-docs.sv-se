---
title: 'Självstudier: Azure Active Directory-integrering med Mixpanel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4595202aa60a2b8888487d505aa8981c6f45bd8d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189029"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Självstudier: Azure Active Directory-integrering med Mixpanel

I den här självstudien får du lära dig hur du integrerar Mixpanel med Azure Active Directory (AD Azure).

Integrera Mixpanel med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mixpanel
- Du kan aktivera användarna att automatiskt få loggat in på Mixpanel (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mixpanel, behöver du följande objekt:

- En Azure AD-prenumeration
- En Mixpanel enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Mixpanel från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mixpanel-from-the-gallery"></a>Att lägga till Mixpanel från galleriet
För att konfigurera integrering av Mixpanel i Azure AD, som du behöver lägga till Mixpanel från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Mixpanel från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Mixpanel**.

    ![Skapa en Azure AD-användare för testning](./media/mixpanel-tutorial/tutorial_mixpanel_search.png)

1. I resultatpanelen väljer **Mixpanel**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Mixpanel baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Mixpanel är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Mixpanel upprättas.

I Mixpanel, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mixpanel, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare i Mixpanel](#creating-a-mixpanel-test-user)**  – du har en motsvarighet för Britta Simon i Mixpanel som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program i Mixpanel.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mixpanel:**

1. I Azure-portalen på den **Mixpanel** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

1. På den **Mixpanel domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/mixpanel-tutorial/tutorial_mixpanel_url.png)

     I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://mixpanel.com/login/`

    > [!NOTE] 
    > Registrera dig på [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) att ställa in dina inloggningsuppgifter och kontakta den [Mixpanel supportteamet](mailto:support@mixpanel.com) att aktivera SSO-inställningar för din klient. Du kan också få logga på URL-värdet om det behövs från din Mixpanel supportteam. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/mixpanel-tutorial/tutorial_general_400.png)

1. På den **Mixpanel Configuration** klickar du på **konfigurera Mixpanel** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/mixpanel-tutorial/tutorial_mixpanel_configure.png) 

1. I ett annat webbläsarfönster inloggning till Mixpanel-programmet som en administratör.

1. Längst ned på sidan, klicka på den lilla **kugghjulsikonen** i det vänstra hörnet. 
   
    ![Mixpanel Single Sign-On](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

1. Klicka på den **programåtkomst** fliken och klicka sedan på **ändra inställningarna för**.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

1. På den **ändra certifikatet** dialogrutan sidan, klickar du på **Välj fil** ladda upp nedladdade certifikatet och klicka sedan på **nästa**.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

1.  I URL-textrutan för autentisering på den **ändra autentiserings-URL** dialogrutan klistrar du in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen och klicka sedan på **Nästa**.
   
   ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

1. Klicka på **Klar**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/mixpanel-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/mixpanel-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/mixpanel-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/mixpanel-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-mixpanel-test-user"></a>Skapa en testanvändare i Mixpanel

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Mixpanel. 

1. Logga in på webbplatsen Mixpanel företag som administratör.

1. Längst ned på sidan klickar du på knappen lite gear i det vänstra hörnet för att öppna den **inställningar** fönster.

1. Klicka på den **Team** fliken.

1. I den **teammedlem** textrutan skriver Brittas e-postadress i Azure.
   
    ![Mixpanel inställningar](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

1. Klicka på **Bjud in**. 

> [!Note]
> Användaren får ett e-postmeddelande för att konfigurera profilen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mixpanel.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Mixpanel, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Mixpanel**.

    ![Konfigurera enkel inloggning](./media/mixpanel-tutorial/tutorial_mixpanel_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mixpanel i åtkomstpanelen du bör få automatiskt loggat in på ditt program i Mixpanel.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/mixpanel-tutorial/tutorial_general_203.png

