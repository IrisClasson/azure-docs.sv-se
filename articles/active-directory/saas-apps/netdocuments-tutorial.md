---
title: 'Självstudier: Azure Active Directory-integrering med NetDocuments | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: f85fb6a628692fc5c0054ac6047980a5ea66cbe3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152510"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Självstudier: Azure Active Directory-integrering med NetDocuments

I den här självstudien får du lära dig hur du integrerar NetDocuments med Azure Active Directory (AD Azure).

Integrera NetDocuments med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till NetDocuments
- Du kan aktivera användarna att automatiskt få loggat in på NetDocuments (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med NetDocuments, behöver du följande objekt:

- En Azure AD-prenumeration
- En NetDocuments enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till NetDocuments från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-netdocuments-from-the-gallery"></a>Att lägga till NetDocuments från galleriet
För att konfigurera integrering av NetDocuments i Azure AD, som du behöver lägga till NetDocuments från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till NetDocuments från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **NetDocuments**.

    ![Skapa en Azure AD-användare för testning](./media/netdocuments-tutorial/tutorial_netdocuments_search.png)

1. I resultatpanelen väljer **NetDocuments**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med NetDocuments baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i NetDocuments är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i NetDocuments upprättas.

I NetDocuments, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med NetDocuments, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare NetDocuments](#creating-a-netdocuments-test-user)**  – du har en motsvarighet för Britta Simon i NetDocuments som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt NetDocuments program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med NetDocuments:**

1. I Azure-portalen på den **NetDocuments** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

1. På den **NetDocuments domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och svars-URL. Kontakta [NetDocuments supportteam](https://support.netdocuments.com/hc/) att hämta dessa värden.
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/netdocuments-tutorial/tutorial_general_400.png)

1. Logga in på webbplatsen NetDocuments företag som en administratör i ett annat webbläsarfönster.

1. Gå till **Admin**.

1. Klicka på **Lägg till och ta bort användare och grupper**.
   
    ![Databasen](./media/netdocuments-tutorial/ic795047.png "lagringsplats")

1. Klicka på **konfigurera avancerade autentiseringsalternativ**.
    
    ![Konfigurera avancerade autentiseringsalternativ](./media/netdocuments-tutorial/ic795048.png "konfigurera avancerade autentiseringsalternativ")

1. På den **federerad identitet** dialogrutan utför följande steg:
   
    ![Federerad Identitty](./media/netdocuments-tutorial/ic795049.png "federerad Identitty")
   
    a. Som **federerad identitet servertyp**väljer **Active Directory Federation Services**.
   
    b. Klicka på **Välj fil**, för att ladda upp den hämtade metadatafilen som du har hämtat från Azure-portalen.
   
    c. Klicka på **OK**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/netdocuments-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/netdocuments-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/netdocuments-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/netdocuments-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-netdocuments-test-user"></a>Skapa en NetDocuments testanvändare

Om du vill aktivera Azure AD-användare att logga in på NetDocuments, måste de etableras i NetDocuments.  
När det gäller NetDocuments är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Registrerar in på din **NetDocuments** företagets plats som administratör.

1. På menyn längst upp klickar du på **Admin**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

1. Klicka på **Lägg till och ta bort användare och grupper**.
   
    ![Databasen](./media/netdocuments-tutorial/ic795047.png "lagringsplats")

1. I den **e-postadress** textrutan skriver du ett giltigt Azure Active Directory-konto du vill etablera och klicka sedan på e-postadress **Lägg till användare**.
   
    ![E-postadress](./media/netdocuments-tutorial/ic795053.png "e-postadress")
   
   >[!NOTE]
   >Azure Active Directory-kontoinnehavare får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktiv. Du kan använda alla andra NetDocuments användare konto verktyg för att skapa eller API: er som tillhandahålls av NetDocuments för att etablera Azure Active Directory användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till NetDocuments.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon NetDocuments, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **NetDocuments**.

    ![Konfigurera enkel inloggning](./media/netdocuments-tutorial/tutorial_netdocuments_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen NetDocuments i åtkomstpanelen du bör få automatiskt loggat in på ditt NetDocuments program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/netdocuments-tutorial/tutorial_general_203.png

