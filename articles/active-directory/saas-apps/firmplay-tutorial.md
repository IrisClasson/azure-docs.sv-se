---
title: 'Självstudier: Azure Active Directory-integrering med FirmPlay - medarbetare kundstöd för rekrytering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FirmPlay - medarbetare kundstöd för rekrytering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 929494d5d802dbc545c750386a286029c4bf962d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809810"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Självstudier: Azure Active Directory-integrering med FirmPlay - medarbetare kundstöd för rekrytering

I den här självstudien får du lära dig hur du integrerar FirmPlay - medarbetare kundstöd för rekrytering med Azure Active Directory (AD Azure).

Integrera FirmPlay - medarbetare kundstöd för rekrytering med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FirmPlay - medarbetare kundstöd för rekrytering
- Du kan aktivera användarna att automatiskt få loggat in på FirmPlay - medarbetare kundstöd för rekrytering (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FirmPlay - medarbetare kundstöd för rekrytering, behöver du följande objekt:

- En Azure AD-prenumeration
- En FirmPlay - medarbetare kundstöd för rekrytering enkel inloggning aktiverad prenumeration


> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.


Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FirmPlay - medarbetare kundstöd för rekrytering från galleriet
1. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Att lägga till FirmPlay - medarbetare kundstöd för rekrytering från galleriet
Om du vill konfigurera integreringen av FirmPlay - medarbetare kundstöd för rekrytering i Azure AD som du behöver lägga till FirmPlay - medarbetare kundstöd för rekrytering från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FirmPlay - medarbetare kundstöd för rekrytering från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **FirmPlay - medarbetare kundstöd för rekrytering**.

    ![Skapa en Azure AD-användare för testning](./media/firmplay-tutorial/tutorial_firmplay_001.png)

1. I resultatpanelen väljer **FirmPlay - medarbetare kundstöd för rekrytering**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FirmPlay - medarbetare kundstöd för rekrytering baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD behöver du veta vilka motsvarande användaren i FirmPlay - medarbetare kundstöd för rekrytering är att en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FirmPlay - medarbetare kundstöd för rekrytering upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i FirmPlay - medarbetare kundstöd för rekrytering.

Om du vill konfigurera och testa Azure AD enkel inloggning med FirmPlay - medarbetare kundstöd för rekrytering, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en FirmPlay - medarbetare kundstöd för rekrytering testanvändare](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  – du har en motsvarighet för Britta Simon i FirmPlay: Medarbetaren kundstöd för rekrytering som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i din FirmPlay - medarbetare kundstöd för rekrytering program.

**Om du vill konfigurera Azure AD enkel inloggning med FirmPlay - medarbetare kundstöd för rekrytering, utför du följande steg:**

1. I hanteringsportalen för Azure på den **FirmPlay - medarbetare kundstöd för rekrytering** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_01.png)

1. På den **FirmPlay - medarbetare kundstöd rekrytering domän och URL: er** avsnittet i den **inloggning på URL: en** textrutan anger du ett URL med hjälp av följande mönster: `https://<your-subdomain>.firmplay.com/`

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera det här värdet med faktiska logga på URL: en. Kontakta [FirmPlay - medarbetare kundstöd för rekrytering supportteamet](mailto:engineering@firmplay.com) att hämta det här värdet. 

1. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_03.png)     

1. På den **Skapa nytt certifikat** dialogrutan klickar du på kalenderikonen och väljer en **förfallodatum**. Klicka sedan på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_general_300.png)

1. På den **SAML-signeringscertifikat** väljer **gör nytt certifikat aktivt** och klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_04.png)

1. På popup-fönstret **förnyelsecertifikatet** fönstret klickar du på **OK**.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_general_400.png)

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (base64)** och spara certifikatfilen på datorn. 

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_05.png) 

1. På den **FirmPlay - medarbetare kundstöd för konfiguration av rekrytering** klickar du på **konfigurera FirmPlay - medarbetare kundstöd för rekrytering** att öppna **konfigurera inloggning** dialogrutan.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_07.png)

1. För att få SSO konfigurerats för ditt program kan kontakta [FirmPlay - medarbetare kundstöd för rekrytering supportteamet](mailto:engineering@firmplay.com) och ge dem med följande: 

    • De hämtade **certifikatfil**

    • Det **URL för SAML enkel inloggning**

    • Det **SAML entitets-ID**

    • Det **utloggnings-URL**
  

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/firmplay-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/firmplay-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/firmplay-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/firmplay-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Skapa en FirmPlay - medarbetare kundstöd för rekrytering testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i FirmPlay - medarbetare kundstöd för rekrytering. Kontakta [FirmPlay - medarbetare kundstöd för rekrytering supportteamet](mailto:engineering@firmplay.com) att lägga till användare i FirmPlay - medarbetare kundstöd för rekrytering plattform.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst FirmPlay - medarbetare kundstöd för rekrytering.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon FirmPlay - medarbetare kundstöd för rekrytering, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **FirmPlay - medarbetare kundstöd för rekrytering**.

    ![Konfigurera enkel inloggning](./media/firmplay-tutorial/tutorial_firmplay_50.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på FirmPlay - medarbetare kundstöd för rekrytering panel i åtkomstpanelen, du bör få automatiskt loggat in på ditt FirmPlay - medarbetare kundstöd för rekrytering program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/firmplay-tutorial/tutorial_general_01.png
[2]: ./media/firmplay-tutorial/tutorial_general_02.png
[3]: ./media/firmplay-tutorial/tutorial_general_03.png
[4]: ./media/firmplay-tutorial/tutorial_general_04.png

[100]: ./media/firmplay-tutorial/tutorial_general_100.png

[200]: ./media/firmplay-tutorial/tutorial_general_200.png
[201]: ./media/firmplay-tutorial/tutorial_general_201.png
[202]: ./media/firmplay-tutorial/tutorial_general_202.png
[203]: ./media/firmplay-tutorial/tutorial_general_203.png