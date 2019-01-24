---
title: 'Självstudier: Azure Active Directory-integrering med Lecorpio | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lecorpio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: 7ac08c7bb076e501f917126be19127e2c7a0fa7e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808263"
---
# <a name="tutorial-azure-active-directory-integration-with-lecorpio"></a>Självstudier: Azure Active Directory-integrering med Lecorpio

I den här självstudien får du lära dig hur du integrerar Lecorpio med Azure Active Directory (AD Azure).

Integrera Lecorpio med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Lecorpio
- Du kan aktivera användarna att automatiskt få loggat in på Lecorpio (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Lecorpio, behöver du följande objekt:

- En Azure AD-prenumeration
- En Lecorpio enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Lecorpio från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-lecorpio-from-the-gallery"></a>Att lägga till Lecorpio från galleriet
För att konfigurera integrering av Lecorpio i Azure AD, som du behöver lägga till Lecorpio från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Lecorpio från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Lecorpio**.

    ![Skapa en Azure AD-användare för testning](./media/lecorpio-tutorial/tutorial_lecorpio_search.png)

1. I resultatpanelen väljer **Lecorpio**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/lecorpio-tutorial/tutorial_lecorpio_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Lecorpio baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Lecorpio är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Lecorpio upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Lecorpio.

Om du vill konfigurera och testa Azure AD enkel inloggning med Lecorpio, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Lecorpio](#creating-a-lecorpio-test-user)**  – du har en motsvarighet för Britta Simon i Lecorpio som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Lecorpio program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Lecorpio:**

1. I Azure-portalen på den **Lecorpio** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/lecorpio-tutorial/tutorial_lecorpio_samlbase.png)

1. På den **Lecorpio domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/lecorpio-tutorial/tutorial_lecorpio_url.png)

    a. I den **inloggnings-URL** textrutan skriver du värdet med följande mönster: `https://<instance name>.lecorpio.com/<customer name>`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<instance name>.lecorpio.com/<customer name>`

    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Kontakta [Lecorpio klienten supportteamet](mailto:info@lecorpio.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/lecorpio-tutorial/tutorial_lecorpio_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/lecorpio-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Lecorpio** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Lecorpio supportteamet](mailto:info@lecorpio.com).

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/lecorpio-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/lecorpio-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/lecorpio-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/lecorpio-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-lecorpio-test-user"></a>Skapa en Lecorpio testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Lecorpio. 

Kontakta [Lecorpio klienten supportteamet](mailto:info@lecorpio.com) att lägga till användare i Lecorpio-programmet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Lecorpio.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Lecorpio, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Lecorpio**.

    ![Konfigurera enkel inloggning](./media/lecorpio-tutorial/tutorial_lecorpio_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Lecorpio i åtkomstpanelen du bör få automatiskt loggat in på ditt Lecorpio program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lecorpio-tutorial/tutorial_general_01.png
[2]: ./media/lecorpio-tutorial/tutorial_general_02.png
[3]: ./media/lecorpio-tutorial/tutorial_general_03.png
[4]: ./media/lecorpio-tutorial/tutorial_general_04.png

[100]: ./media/lecorpio-tutorial/tutorial_general_100.png

[200]: ./media/lecorpio-tutorial/tutorial_general_200.png
[201]: ./media/lecorpio-tutorial/tutorial_general_201.png
[202]: ./media/lecorpio-tutorial/tutorial_general_202.png
[203]: ./media/lecorpio-tutorial/tutorial_general_203.png

