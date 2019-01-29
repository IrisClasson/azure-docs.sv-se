---
title: 'Självstudier: Azure Active Directory-integrering med OneTrust sekretess hanteringsprogramvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och programvaran för hantering av OneTrust sekretess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: 46480119579513839024d89e7657661e12e5509c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167300"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Självstudier: Azure Active Directory-integrering med hanteringsprogramvara för OneTrust sekretess

Lär dig hur du integrerar OneTrust sekretess hanteringsprogramvara med Azure Active Directory (AD Azure) i den här självstudien.

Integrera OneTrust sekretess hanteringsprogramvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till programvaran för hantering av OneTrust sekretess.
- Du kan aktivera användarna att automatiskt få loggat in på OneTrust sekretess hanteringsprogramvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OneTrust hanteringsprogramvara för sekretess, behöver du följande objekt:

- En Azure AD-prenumeration
- En OneTrust sekretess hanteringsprogramvara enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till OneTrust sekretess hanteringsprogramvara från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Att lägga till OneTrust sekretess hanteringsprogramvara från galleriet
För att konfigurera integrering av hanteringsprogramvara för OneTrust sekretess i Azure AD, som du behöver lägga till OneTrust sekretess hanteringsprogramvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OneTrust sekretess hanteringsprogramvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **OneTrust sekretess hanteringsprogramvara**väljer **OneTrust sekretess hanteringsprogramvara** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![OneTrust sekretess hanteringsprogramvara i resultatlistan](./media/onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OneTrust sekretess hanteringsprogramvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i hanteringsprogramvara för OneTrust sekretess är en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i hanteringsprogramvara för OneTrust sekretess upprättas.

I program för OneTrust sekretess, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med OneTrust hanteringsprogramvara för sekretess, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare OneTrust sekretess hanteringsprogramvara](#create-a-onetrust-privacy-management-software-test-user)**  – du har en motsvarighet för Britta Simon i hanteringsprogramvara för OneTrust sekretess som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt hanteringsprogramvara för OneTrust sekretess-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med hanteringsprogramvara för OneTrust sekretess:**

1. I Azure-portalen på den **OneTrust sekretess hanteringsprogramvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/onetrust-tutorial/tutorial_onetrust_samlbase.png)

1. På den **OneTrust sekretess Hanteringsdomän programvara och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![OneTrust sekretess Hanteringsdomän programvara och URL: er med enkel inloggning för information](./media/onetrust-tutorial/tutorial_onetrust_url.png)

    a. I den **identifierare** textrutan anger du ett URL: `https://www.onetrust.com/saml2`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.onetrust.com/auth/consumerservice`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![OneTrust sekretess Hanteringsdomän programvara och URL: er med enkel inloggning för information](./media/onetrust-tutorial/tutorial_onetrust_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.onetrust.com/auth/login`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL och URL: en inloggning. Kontakta [OneTrust sekretess Management-klientprogrammet supportteamet](mailto:support@onetrust.com) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/onetrust-tutorial/tutorial_onetrust_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/onetrust-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **OneTrust sekretess hanteringsprogramvara** sida, som du behöver skicka de hämtade **XML-Metadata för** till [OneTrust sekretess hanteringsprogramvara supportteamet](mailto:support@onetrust.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/onetrust-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/onetrust-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/onetrust-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/onetrust-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Skapa en testanvändare hanteringsprogramvara för OneTrust sekretess

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i hanteringsprogramvara för OneTrust sekretess. Programvaran för hantering av OneTrust sekretess stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt programvaran för hantering av OneTrust sekretess om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt, kontakta [OneTrust sekretess hanteringsprogramvara supportteamet](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till programvaran för hantering av OneTrust sekretess.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon OneTrust hanteringsprogramvara för sekretess, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **OneTrust sekretess hanteringsprogramvara**.

    ![Länken hanteringsprogramvara för OneTrust sekretess i listan med program](./media/onetrust-tutorial/tutorial_onetrust_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen hanteringsprogramvara för OneTrust sekretess i åtkomstpanelen du bör få automatiskt loggat in på ditt hanteringsprogramvara för OneTrust sekretess-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onetrust-tutorial/tutorial_general_01.png
[2]: ./media/onetrust-tutorial/tutorial_general_02.png
[3]: ./media/onetrust-tutorial/tutorial_general_03.png
[4]: ./media/onetrust-tutorial/tutorial_general_04.png

[100]: ./media/onetrust-tutorial/tutorial_general_100.png

[200]: ./media/onetrust-tutorial/tutorial_general_200.png
[201]: ./media/onetrust-tutorial/tutorial_general_201.png
[202]: ./media/onetrust-tutorial/tutorial_general_202.png
[203]: ./media/onetrust-tutorial/tutorial_general_203.png

