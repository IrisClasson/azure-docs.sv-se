---
title: 'Självstudier: Azure Active Directory-integrering med Bime | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 5d1aa3ebf089cd7024792be35f31d9febc19b0c5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189059"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Självstudier: Azure Active Directory-integrering med Bime

I den här självstudien får du lära dig hur du integrerar Bime med Azure Active Directory (AD Azure).

Integrera Bime med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bime
- Du kan aktivera användarna att automatiskt få loggat in på Bime (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bime, behöver du följande objekt:

- En Azure AD-prenumeration
- En Bime enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du få en tre månaders kostnadsfri utvärdering här: [Utvärderingserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Bime från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bime-from-the-gallery"></a>Att lägga till Bime från galleriet
För att konfigurera integrering av Bime i Azure AD, som du behöver lägga till Bime från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bime från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Bime**.

    ![Skapa en Azure AD-användare för testning](./media/bime-tutorial/tutorial_bime_search.png)

1. I resultatpanelen väljer **Bime**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/bime-tutorial/tutorial_bime_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Bime baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Bime är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Bime upprättas.

I Bime, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bime, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Bime](#creating-a-bime-test-user)**  – du har en motsvarighet för Britta Simon i Bime som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bime program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bime:**

1. I Azure-portalen på den **Bime** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_samlbase.png)

1. På den **Bime domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenant-name>.Bimeapp.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Bime klienten supportteamet](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet från certifikatet.

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_general_400.png)

1. På den **Bime Configuration** klickar du på **konfigurera Bime** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_configure.png) 

1. Logga in på webbplatsen Bime företag som en administratör i ett annat webbläsarfönster.

1. I verktygsfältet klickar du på **Admin**, och sedan **konto**.
   
    ![Admin](./media/bime-tutorial/ic775558.png "Admin")

1. Utför följande steg på sidan för konfiguration:
   
    ![Konfigurera enkel inloggning](./media/bime-tutorial/ic775559.png "Konfigurera enkel inloggning")
   
    a. Välj **aktivera SAML-autentisering**.

    b. I den **Remote inloggnings-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c.  Klistra in den **tumavtryck** värdet från Azure-portalen till den **certifikat fingeravtryck** textrutan.       
   
    d. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/bime-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/bime-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/bime-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/bime-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-bime-test-user"></a>Skapa en Bime testanvändare

För att aktivera Azure AD-användare att logga in på Bime, måste de etableras i Bime. När det gäller Bime är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Bime** klient.

1. I verktygsfältet klickar du på **Admin**, och sedan **användare**.
   
    ![Admin](./media/bime-tutorial/ic775561.png "Admin")

1. I den **användarlistan**, klickar du på **Lägg till ny användare** (”+”).
   
    ![Användare](./media/bime-tutorial/ic775562.png "Användare")

1. På den **användarinformation** dialogrutan utför följande steg:
   
    ![Information om användare](./media/bime-tutorial/ic775563.png "användarinformation")
   
    a. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textrutan Ange efternamn för användaren som **Simon**.
 
    c. I textrutan **E-post** anger du e-postadressen för användaren, t.ex. **brittasimon@contoso.com**.

    d. Klicka på **Spara**.

>[!NOTE]
>Du kan använda alla andra Bime användare konto verktyg för att skapa eller API: er som tillhandahålls av Bime att etablera AAD-användarkonton.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bime.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Bime, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Bime**.

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bime i åtkomstpanelen du bör få automatiskt loggat in på ditt Bime program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bime-tutorial/tutorial_general_01.png
[2]: ./media/bime-tutorial/tutorial_general_02.png
[3]: ./media/bime-tutorial/tutorial_general_03.png
[4]: ./media/bime-tutorial/tutorial_general_04.png

[100]: ./media/bime-tutorial/tutorial_general_100.png

[200]: ./media/bime-tutorial/tutorial_general_200.png
[201]: ./media/bime-tutorial/tutorial_general_201.png
[202]: ./media/bime-tutorial/tutorial_general_202.png
[203]: ./media/bime-tutorial/tutorial_general_203.png

