---
title: 'Självstudier: Azure Active Directory-integrering med bra jobbat av | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bra jobbat av.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 5cc6873ff1c823aad5165c89572c7a9f50e30e1c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55204141"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Självstudier: Azure Active Directory-integrering med bra jobbat av

I den här självstudien får du lära dig hur du integrerar Bra jobbat med Azure Active Directory (AD Azure).

Bra jobbat av integrerar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till Bra jobbat av
- Du kan aktivera användarna att automatiskt få loggat in på Bra jobbat av (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med bra jobbat av, behöver du följande objekt:

- En Azure AD-prenumeration
- En bra jobbat av enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Bra jobbat av för att lägga till från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kudos-from-the-gallery"></a>Bra jobbat av för att lägga till från galleriet
För att konfigurera integrering av Bra jobbat av i Azure AD, som du behöver lägga till Bra jobbat av från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bra jobbat av från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Bra jobbat av**.

    ![Skapa en Azure AD-användare för testning](./media/kudos-tutorial/tutorial_kudos_search.png)

1. I resultatpanelen väljer **Bra jobbat av**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med bra jobbat av baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Bra jobbat av är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Bra jobbat av upprättas.

I Bra jobbat av, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med bra jobbat av, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en bra jobbat av testanvändare](#creating-a-kudos-test-user)**  – du har en motsvarighet för Britta Simon i Bra jobbat av som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Bra jobbat av programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bra jobbat av:**

1. I Azure-portalen på den **Bra jobbat av** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

1. På den **Bra jobbat av domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<company>.kudosnow.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Bra jobbat av klienten supportteamet](http://success.kudosnow.com/home) att hämta det här värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_general_400.png)

1. På den **Bra jobbat av konfigurationen** klickar du på **konfigurera Bra jobbat av** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_configure.png) 

1. Logga in på webbplatsen Bra jobbat av företag som en administratör i ett annat webbläsarfönster.

1. Klicka på menyn längst upp **inställningar**.
   
    ![Inställningar](./media/kudos-tutorial/ic787806.png "Inställningar")

1. Klicka på **integreringar \> SSO**.

1. I den **SSO** avsnittet, utför följande steg:
   
    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")
   
    a. I **inloggnings-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen. 

    b. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan
   
    c. I **URL för utloggning till**, klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
   
    d. I den **din Bra jobbat av URL** textrutan skriver du namnet på ditt företag.
   
    e. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/kudos-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/kudos-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/kudos-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/kudos-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-kudos-test-user"></a>Skapa en bra jobbat av användare

För att aktivera Azure AD-användare att logga in på Bra jobbat av etableras de i Bra jobbat av. 

När det gäller Bra jobbat av är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Bra jobbat av** företagets plats som administratör.

1. Klicka på menyn längst upp **inställningar**.
   
   ![Inställningar](./media/kudos-tutorial/ic787806.png "Inställningar")

1. Klicka på **Användaradministration**.

1. Klicka på den **användare** fliken och klicka sedan på **lägga till en användare**.
   
   ![Användaradministration](./media/kudos-tutorial/ic787809.png "Användaradministration")

1. I den **lägga till en användare** avsnittet, utför följande steg:
   
    ![Lägga till en användare](./media/kudos-tutorial/ic787810.png "lägga till en användare")
   
    a. Skriv den **Förnamn**, **efternamn**, **e-post** och annan information om ett giltigt Azure Active Directory-konto som du vill etablera till relaterade textrutor.
   
    b. Klicka på **skapa användare**.

>[!NOTE]
>Du kan använda alla andra Bra jobbat av användarens konto verktyg för att skapa eller API: er som tillhandahålls av Bra jobbat av att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bra jobbat av.

![Tilldela användare][200] 

**Om du vill tilldela Bra jobbat av Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Bra jobbat av**.

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bra jobbat av åtkomstpanelen du bör få automatiskt loggat in på Bra jobbat av programmet. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png

