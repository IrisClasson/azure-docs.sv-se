---
title: 'Självstudier: Azure Active Directory-integrering med Adobe Captivate Prime | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: aa20e4544fcd78330c0daa15b9aa058ba80af2d5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171957"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Självstudier: Azure Active Directory-integrering med Adobe Captivate Prime

I den här självstudien får du lära dig hur du integrerar Adobe Captivate Prime med Azure Active Directory (AD Azure).

Integrera Adobe Captivate Prime med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe Captivate Prime.
- Du kan aktivera användarna att automatiskt få loggat in på Adobe Captivate Prime (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Adobe Captivate Prime, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett Adobe Captivate Prime enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Adobe Captivate Prime från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Att lägga till Adobe Captivate Prime från galleriet
För att konfigurera integrering av Adobe Captivate Prime i Azure AD, som du behöver lägga till Adobe Captivate Prime från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Adobe Captivate Prime från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Adobe Captivate Prime**väljer **Adobe Captivate Prime** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Adobe Captivate Prime i resultatlistan](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Adobe Captivate Prime baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Adobe Captivate Prime är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Adobe Captivate Prime upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Adobe Captivate Prime, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare för Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)**  – du har en motsvarighet för Britta Simon i Adobe Captivate Prime som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Adobe Captivate Prime-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Adobe Captivate Prime:**

1. I Azure-portalen på den **Adobe Captivate Prime** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. På den **Adobe Captivate Prime domän och URL: er** avsnittet, utför följande steg:

    ![Adobe Captivate Prime domän och URL: er med enkel inloggning för information](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://captivateprime.adobe.com`

    b. I den **svars-URL** textrutan anger du ett URL: `https://captivateprime.adobe.com/saml/SSO`

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Gå till **egenskaper** fliken, kopiera den **URL för användaråtkomst** och klistra in den i anteckningar.

    ![Länken för användaren åtkomst](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Att konfigurera enkel inloggning på **Adobe Captivate Prime** sida, som du behöver skicka de hämtade **XML-Metadata för** och kopierade **URL för användaråtkomst** till [Adobe Captivate Prime supportteamet](mailto:captivateprimesupport@adobe.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Skapa ett Adobe Captivate Prime testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Adobe Captivate Prime. Arbeta med [Adobe Captivate Prime supportteamet](mailto:captivateprimesupport@adobe.com) att lägga till användare i Adobe Captivate Prime-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Adobe Captivate Prime.

![Tilldela rollen][200] 

**Om du vill tilldela Adobe Captivate Prime Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Adobe Captivate Prime**.

    ![Adobe Captivate Prime länken i listan med program](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Adobe Captivate Prime i åtkomstpanelen du bör få automatiskt loggat in på ditt Adobe Captivate Prime-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

