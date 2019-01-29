---
title: 'Självstudier: Azure Active Directory-integrering med Cisco Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco-molnet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db1cea1d-ff0a-4f0d-b5fd-50ca32702d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: c5809426da95e2295c5bf318b6f17345002f536d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197950"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-cloud"></a>Självstudier: Azure Active Directory-integrering med Cisco molnet

Lär dig hur du integrerar Cisco molnet med Azure Active Directory (AD Azure) i de här självstudierna.

Integrera Cisco molnet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cisco moln.
- Du kan aktivera användarna att automatiskt få loggat in på Cisco Cloud (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cisco molnet, behöver du följande objekt:

- En Azure AD-prenumeration
- En Cisco-molnet enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Cisco molnet från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cisco-cloud-from-the-gallery"></a>Att lägga till Cisco molnet från galleriet
Om du vill konfigurera integreringen av Cisco-moln till Azure AD, som du behöver lägga till Cisco molnet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cisco molnet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Cisco molnet**väljer **Cisco molnet** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Cisco molnet i resultatlistan](./media/ciscocloud-tutorial/tutorial_ciscocloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cisco molnet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Cisco molnet är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Cisco molnet upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco moln, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Cisco molnet](#create-a-cisco-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Cisco-moln som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Cisco molnprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cisco molnet:**

1. I Azure-portalen på den **Cisco molnet** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/ciscocloud-tutorial/tutorial_ciscocloud_samlbase.png)

1. På den **Cisco molnet domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Cisco molnet domän och URL: er med enkel inloggning för information](./media/ciscocloud-tutorial/tutorial_ciscocloud_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `<subdomain>.cisco.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.cisco.com/sp/ACS.saml2`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Cisco molnet domän och URL: er med enkel inloggning för information](./media/ciscocloud-tutorial/tutorial_ciscocloud_url1.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://<subdomain>.cloudapps.cisco.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och URL: en inloggning. Kontakta [Cisco Cloud klienten supportteamet](mailto:cpr-ops@cisco.com) att hämta dessa värden.

1. Cisco molnprogram förväntas SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering.
 Följande skärmbild visar ett exempel på hur den.

    ![Konfigurera enkel inloggning](./media/ciscocloud-tutorial/attribute.png)

1. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | Land      |user.country |
    | Företagets      |user.companyname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ciscocloud-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/ciscocloud-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **Namespace** värdet som anges.

    e. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/ciscocloud-tutorial/tutorial_ciscocloud_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/ciscocloud-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Cisco molnet** sida, som du behöver skicka den **Appfederationsmetadata** till [Cisco molnet supportteamet](mailto:cpr-ops@cisco.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ciscocloud-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ciscocloud-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ciscocloud-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ciscocloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-cisco-cloud-test-user"></a>Skapa en testanvändare Cisco molnet

I det här avsnittet skapar du en användare som kallas Britta Simon i Cisco-molnet. Arbeta med [Cisco molnet supportteamet](mailto:cpr-ops@cisco.com) att lägga till användare i Cisco molnplattform. Användare måste skapas och aktiveras innan du använder enkel inloggning

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Cisco-molnet.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Cisco molnet, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Cisco molnet**.

    ![Cisco Cloud-länk i listan med program](./media/ciscocloud-tutorial/tutorial_ciscocloud_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Cisco molnet i åtkomstpanelen du bör få automatiskt loggat in på ditt Cisco molnprogram.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ciscocloud-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloud-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloud-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloud-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloud-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloud-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloud-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloud-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloud-tutorial/tutorial_general_203.png

