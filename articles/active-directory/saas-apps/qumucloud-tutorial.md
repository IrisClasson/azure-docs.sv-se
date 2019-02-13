---
title: 'Självstudier: Azure Active Directory-integrering med Qumu Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Qumu molnet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8c4a97b-4de6-49d4-b64e-42222c2ec6c9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59766ce6572ef9ccd767122676667ad44ea58f6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179662"
---
# <a name="tutorial-azure-active-directory-integration-with-qumu-cloud"></a>Självstudier: Azure Active Directory-integrering med Qumu molnet

I den här självstudien får du lära dig hur du integrerar Qumu molnet med Azure Active Directory (AD Azure).

Integrera Qumu molnet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Qumu moln.
- Du kan aktivera användarna att automatiskt få loggat in på Qumu Cloud (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Qumu molnet, behöver du följande objekt:

- En Azure AD-prenumeration
- En Qumu enkel inloggning aktiverat molnprenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Qumu molnet från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-qumu-cloud-from-the-gallery"></a>Att lägga till Qumu molnet från galleriet
Om du vill konfigurera integreringen av Qumu moln till Azure AD, som du behöver lägga till Qumu molnet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Qumu molnet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Qumu molnet**väljer **Qumu molnet** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Qumu molnet i resultatlistan](./media/qumucloud-tutorial/tutorial_qumucloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Qumu molnet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Qumu molnet är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Qumu molnet upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Qumu moln, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Qumu molnet](#create-a-qumu-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Qumu moln som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Qumu molnprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Qumu molnet:**

1. I Azure-portalen på den **Qumu molnet** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/qumucloud-tutorial/tutorial_qumucloud_samlbase.png)

1. På den **Qumu molnet domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Qumu molnet domän och URL: er med enkel inloggning för information](./media/qumucloud-tutorial/tutorial_qumucloud_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.qumucloud.com/saml/SSO`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.qumucloud.com/saml/SSO`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Qumu molnet domän och URL: er med enkel inloggning för information](./media/qumucloud-tutorial/tutorial_qumucloud_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.qumucloud.com`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Qumu Cloud klienten supportteamet](mailto:support@qumu.com) att hämta dessa värden.

1. Qumu molnprogram förväntas SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning](./media/qumucloud-tutorial/attribute.png)
    
1. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | urn:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |

    a. Klicka på ett attribut för att öppna den **redigera attributet** fönster.

    ![Konfigurera enkel inloggning](./media/qumucloud-tutorial/tutorial_attribute_04.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    ![Konfigurera enkel inloggning](./media/qumucloud-tutorial/tutorial_attribute_05.png)

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Behåll den **Namespace** textrutan som är tom.
    
    e. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/qumucloud-tutorial/tutorial_qumucloud_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/qumucloud-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på **Qumu molnet** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Qumu molnet supportteamet](mailto:support@qumu.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/qumucloud-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/qumucloud-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/qumucloud-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/qumucloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-qumu-cloud-test-user"></a>Skapa en testanvändare Qumu molnet

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Qumu molnet. Qumu molnet stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Qumu molnet om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Qumu Cloud klienten supportteamet](mailto:support@qumu.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Qumu moln.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Qumu molnet, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Qumu molnet**.

    ![Länken Qumu molnet i listan med program](./media/qumucloud-tutorial/tutorial_qumucloud_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Qumu molnet i åtkomstpanelen du bör få automatiskt loggat in på ditt Qumu molnprogram.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/qumucloud-tutorial/tutorial_general_01.png
[2]: ./media/qumucloud-tutorial/tutorial_general_02.png
[3]: ./media/qumucloud-tutorial/tutorial_general_03.png
[4]: ./media/qumucloud-tutorial/tutorial_general_04.png

[100]: ./media/qumucloud-tutorial/tutorial_general_100.png

[200]: ./media/qumucloud-tutorial/tutorial_general_200.png
[201]: ./media/qumucloud-tutorial/tutorial_general_201.png
[202]: ./media/qumucloud-tutorial/tutorial_general_202.png
[203]: ./media/qumucloud-tutorial/tutorial_general_203.png

