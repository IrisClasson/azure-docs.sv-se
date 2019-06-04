---
title: 'Självstudier: Azure Active Directory-integrering med JIRA SAML SSO från Microsoft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och JIRA SAML SSO från Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554a4c63c8492d69ad0899176cdc58895c5146f5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474131"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>Självstudier: Azure Active Directory-integrering med JIRA SAML SSO från Microsoft

I den här självstudien får du lära dig hur du integrerar JIRA SAML SSO från Microsoft med Azure Active Directory (Azure AD).
Genom att integrera JIRA SAML SSO från Microsoft med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till JIRA SAML SSO från Microsoft.
* Du kan göra så att dina användare automatiskt loggas in på JIRA SAML SSO från Microsoft (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="description"></a>Beskrivning

Använd ditt Microsoft Azure Active Directory-konto med Atlassian JIRA-servern för att aktivera enkel inloggning. På så sätt alla organisationens användare kan använda Azure AD-autentiseringsuppgifter för inloggning i JIRA-programmet. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med JIRA SAML SSO från Microsoft behöver du följande:

- En Azure AD-prenumeration
- JIRA Core- och programvara 6.4 till 8.0 eller JIRA Service Desk 3.0 till 3.5 ska installeras och konfigureras på Windows 64-bitars version
- JIRA-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för JIRA-plugin-programmet anges i avsnittet nedan.
- JIRA-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i JIRA
- WebSudo är inaktiverat i JIRA
- Testanvändare har skapats i JIRA-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en JIRA-produktionsmiljö. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du få en tre månaders kostnadsfri utvärdering här: [Utvärderingserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Versioner av JIRA som stöds

* JIRA Core och programvara: 6.4 till 8.0
* JIRA-support 3.0.0 till 3.5.0
* JIRA stöder också 5.2. Om du vill ha mer information klickar du på [Microsoft Azure Active Directory single sign-on for JIRA 5.2](jira52microsoft-tutorial.md) (Microsoft Azure Active Directory enkel inloggning för JIRA 5.2)

> [!NOTE]
> Observera att våra JIRA-plugin-programmet även fungerar på Ubuntu 16.04 för Version

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Jira SAML SSO från Microsoft stöder **SP**-initierad enkel inloggning

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Lägga till Jira SAML SSO från Microsoft från galleriet

För att konfigurera integreringen av Jira SAML SSO från Microsoft med Azure AD måste du lägga till JIRA SAML SSO från Microsoft från galleriet i din lista över hanterade SaaS-appar.

**Lägg till Jira SAML SSO från Microsoft från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Jira SAML SSO från Microsoft**, väljer **Jira SAML SSO från Microsoft** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![JIRA SAML SSO från Microsoft i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med JIRA SAML SSO från Microsoft baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i JIRA SAML SSO från Microsoft upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med JIRA SAML SSO från Microsoft behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för JIRA SAML SSO från Microsoft](#configure-jira-saml-sso-by-microsoft-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa JIRA SAML SSO från Microsoft-testanvändare](#create-jira-saml-sso-by-microsoft-test-user)** – för att ha en motsvarighet för Britta Simon i JIRA SAML SSO från Microsoft som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med JIRA SAML SSO från Microsoft:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **JIRA SAML SSO från Microsoft**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![JIRA SAML SSO från Microsoft-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<domain:port>/`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfritt ifall det är en namngiven URL. Dessa värden tas emot under konfigurationen av Jira-pluginprogrammet, som beskrivs senare i självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-single-sign-on"></a>Konfigurera enkel inloggning för JIRA SAML SSO från Microsoft

1. I ett annat webbläsarfönster, loggar du in din JIRA-instans som administratör.

2. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon1.png)

3. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon12.png)

4. Utför följande steg för att köra scenariot med omvänd JIRA-proxy eller lastbalanseringsscenariot:

    > [!NOTE]
    > Du bör konfigurera servern först med instruktionerna nedan och därefter installera plugin-programmet.

    a. Lägg till attributen nedan i **anslutningsappens** port i filen **server.xml** för JIRA-serverprogram.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Ändra **grundläggande URL** i **Systeminställningar** enligt proxy/lastbalanserare.

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. När plugin-programmet har installerats visas det i avsnittet för **användarinstallerade** tillägg i avsnittet **Manage Add-on** (Hantera tillägg). Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon13.png)

6. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/jiramicrosoft-tutorial/addon53.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    1. I textrutan **Metadata URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    1. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **JIRA SAML SSO by Microsoft Domain and URL** (JIRA SAML SSO från Microsoft-domän och information om URL:er) i Azure-portalen.

    1. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.

    1. I **SAML User ID Locations** (Platser för SAML-användar-ID) väljer du antingen **User ID is in the NameIdentifier element of the Subject statement** (Användar-ID finns i elementet NameIdentifier-instruktionen Ämne) eller **User ID is in an Attribute element** (Användar-ID finns i ett Attribut-element).  Det här ID: T måste vara JIRA användar-ID. Om det användar-ID inte matchas sedan kan inte användare att logga in.

       > [!Note]
       > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.

    1. Om du väljer **användar-ID är i ett attributelement** alternativet i **attributnamnet** textrutan skriver du namnet på attributet som där användar-ID förväntas.

    1. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.

    1. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    1. Kontrollera **aktivera enkelinloggning ut** om du vill logga ut från Azure AD när en användare loggar från JIRA.
    
    1. Aktivera **kraft Azure inloggningen** markerar du kryssrutan om du vill logga in via Azure AD-autentiseringsuppgifter endast.
    
       > [!Note]
       > Aktivera inloggning standardformuläret för användarnamn på inloggningssidan när force azure inloggning är aktiverat, lägga till Frågeparametern i webbläsarens URL-Adressen.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Klicka på knappen **Spara** för att spara inställningarna.

       > [!NOTE]
       > Mer information om installation och felsökning finns [administratörshandboken för MS JIRA SSO Connector](../ms-confluence-jira-plugin-adminguide.md). Det finns också en [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md) på förhand.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon\@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till JIRA SAML SSO från Microsoft.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **JIRA SAML SSO från Microsoft**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **JIRA SAML SSO från Microsoft**.

    ![JIRA SAML SSO från Microsoft-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Skapa JIRA SAML SSO genom Microsoft-testanvändare

Om du vill aktivera Azure AD-användare att logga in på JIRA den lokala servern, måste de etableras i JIRA SAML SSO av Microsoft. När det gäller JIRA SAML SSO från Microsoft är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din JIRA lokal server som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user1.png)

3. Du omdirigeras till administratörsåtkomstsidan där du kan ange **lösenord** och klicka på knappen **Bekräfta**.

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user2.png)

4. På avsnittsfliken **Användarhantering** klickar du på **skapa användare**.

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user3.png) 

5. I dialogrutan **”Skapa ny användare”** utför du följande steg:

    ![Lägga till medarbetare](./media/jiramicrosoft-tutorial/user4.png) 

    a. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Klicka på **Skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på JIRA SAML SSO från Microsoft-panelen i åtkomstpanelen bör du automatiskt loggas in på JIRA SAML SSO från Microsoft som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
