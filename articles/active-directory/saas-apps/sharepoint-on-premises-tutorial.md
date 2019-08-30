---
title: 'Självstudier: Azure Active Directory-integrering med lokal SharePoint | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och lokal SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 9c956f89d890f93a887d2412c74c906095acf4db
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164363"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Självstudier: Azure Active Directory-integrering med SharePoint lokalt

I den här självstudien får du lära dig hur du integrerar lokal SharePoint med Azure Active Directory (Azure AD).
Om du integrerar lokal SharePoint med Azure AD så får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till lokal SharePoint.
* Du kan låta dina användare loggas in automatiskt på lokal SharePoint (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med lokal SharePoint så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Lokal SharePoint-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Lokal SharePoint-stöder **SP** -initierad enkel inloggning

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Lägga till lokal SharePoint från galleriet

Om du vill konfigurera integrering av lokal SharePoint i Azure AD så behöver du lägga till lokal SharePoint från galleriet i din lista över hanterade SaaS-appar.

**Lägg till lokal SharePoint från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

    > [!NOTE]   
    > Om elementet inte ska vara tillgängligt kan det också öppnas via länken fasta **alla tjänster** överst i den vänstra navigerings panelen. I följande översikt finns **Azure Active Directory** länken i avsnittet **identitet** eller också kan den sökas i med hjälp av text rutan filter.

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du in **lokal SharePoint**, väljer **lokal SharePoint-** från resultatpanelen och klickar därefter på **Lägg till** för att lägga till programmet.

    ![Lokal SharePoint i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med lokal SharePoint baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation etableras mellan en Azure AD-användare och den relaterade användaren i lokal SharePoint.

Om du vill konfigurera och testa Azure AD enkel inloggning med lokal SharePoint så måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för lokal SharePoint](#configure-sharepoint-on-premises-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en Azure AD-säkerhetsgrupp i Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** -om du vill aktivera en ny säkerhets grupp i Azure AD för enkel inloggning.
5. **[Bevilja åtkomst till SharePoint lokal säkerhets grupp](#grant-access-to-sharepoint-on-premises-security-group)** – bevilja åtkomst för viss grupp till Azure AD.
6. **[Tilldela Azure AD-säkerhetsgruppen i Azure Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** – för att tilldela den aktuella gruppen till Azure AD för autentisering.
7. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med lokal SharePoint:

1. I [Azure Portal](https://portal.azure.com/), på programintegreringssidan för **lokal SharePoint** så väljer du **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Enkel inloggningsinformation för lokal SharePoint-domän och URL:er](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `urn:sharepoint:federation`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [supportteamet för den lokala SharePoint-klienten](https://support.office.com/) för att få de här värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

    > [!Note]
    > Skriv ned sökvägen till filen som du har hämtat certifikatfilen för eftersom du behöver använda den senare i PowerShell-skriptet för konfigurationen.

6. I avsnittet **Konfigurera lokal SharePoint** kopierar du en eller flera lämpliga URL:er efter behov. Som **URL för enkel inloggningstjänsten** använder du ett värde med följande mönster: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-id för Azure Ad-prenumerationen.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    > [!NOTE]
    > Lokal SharePoint-programmet använder en SAML 1.1-token så Azure AD förväntar sig en WS Fed-begäran från SharePoint-servern och efter autentisering så utfärdar den SAML 1.1. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurera enkel inloggning för lokal SharePoint

1. Logga in på din lokala företags webbplats som administratör i ett annat webbläsarfönster.

2. **Konfigurera en ny betrodd identitetsprovider i SharePoint Server 2016**

    Logga in på SharePoint Server 2016-servern och öppna hanteringsgränssnittet för SharePoint 2016. Fyll i värdena för $realm (Identifierarvärde från den lokala SharePoint-domänen och URL:er-avsnittet i Azure portal), $wsfedurl (URL för enkel inloggningstjänsten) och $filepath (sökväg som du har hämtat certifikatfilen till) från Azure-portalen och kör följande kommandon för att konfigurera en ny betrodd identitetsprovider.

    > [!TIP]
    > Om PowerShell är nytt för dig eller om du vill veta mer om hur PowerShell fungerar, se [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Därefter följer du de här stegen för att aktivera den betrodda identitetsprovidern för ditt program:

    a. I Central Administration går du till **Hantera webbprogram** och väljer det webbprogram som du vill skydda med Azure AD.

    b. I menyfliksområdet klickar du på **Autentiseringsproviders** och väljer den zon som du vill använda.

    c. Välj **Betrodd identitetsprovider** och välj den identitetsprovider som du just registrerade med namnet *AzureAD*.

    d. På inloggningssidans URL-inställningen väljer du **Anpassad inloggningssida** och anger värdet ”/_trust/”.

    e. Klicka på **OK**.

    ![Konfigurera din autentiseringsprovider](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Vissa av de externa användarna kommer inte att kunna använda den här integreringen för enkel inloggning eftersom deras UPN kommer att ha ett manglat värde som liknar `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Snart kommer vi att tillåta kunders appkonfiguration om hur UPN ska hanteras beroende på användartyp. Efter det borde alla dina gästanvändare kunna använda enkel inloggning sömlöst som anställda inom organisationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** anger du`brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Skapa en Azure AD-säkerhetsgrupp i Azure Portal

1. Klicka på **Azure Active Directory > alla grupper**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klicka på **ny grupp**:

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Fyll i **grupp typ**, **grupp namn**, **grupp Beskrivning**, **medlemskaps typ**. Klicka på pilen för att välja medlemmar och Sök sedan efter eller klicka på den medlem som du vill lägga till i gruppen. Klicka på **Välj** för att lägga till de valda medlemmarna och klicka sedan på **skapa**.

    ![Skapa en Azure AD-säkerhetsgrupp](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > För att kunna tilldela Azure Active Directory säkerhets grupper till SharePoint lokalt är det nödvändigt att installera och konfigurera [AzureCP](https://yvand.github.io/AzureCP/) i den lokala SharePoint-servergruppen eller utveckla och konfigurera en alternativ anpassad anspråks leverantör för SharePoint.  Mer information finns i avsnittet Mer information i slutet av dokumentet för att skapa en egen kund anspråks leverantör, om du inte använder AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Bevilja åtkomst till SharePoint lokal säkerhets grupp

**Konfigurera säkerhets grupper och behörigheter för appens registrering**

1. I Azure Portal väljer du **Azure Active Directory**och väljer sedan **Appregistreringar**.

    ![Bladet Företagsprogram](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Skriv och välj **SharePoint lokalt**i rutan Sök.

    ![Lokal SharePoint i resultatlistan](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klicka på **manifest**.

    ![Manifest alternativ](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Ändra `groupMembershipClaims`: `NULL`, till `groupMembershipClaims`: .`SecurityGroup` Klicka sedan på Spara

    ![Redigera manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klicka på **Inställningar**och klicka sedan på **nödvändiga behörigheter**.

    ![Nödvändiga behörigheter](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klicka på **Lägg till** och **Välj sedan ett API**.

    ![API-åtkomst](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Lägg till både **Windows Azure Active Directory** och **Microsoft Graph API**, men det går bara att välja ett i taget.

    ![API-val](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Välj Windows Azure Active Directory, kontrol lera Läs katalog data och klicka på Välj. Gå tillbaka och Lägg till Microsoft Graph och välj Läs katalog data för den även.  Klicka på Välj och på färdig.

    ![Aktivera åtkomst](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Klicka på **bevilja behörigheter** under obligatoriska inställningar och klicka sedan på Ja för att bevilja behörigheter.

    ![Bevilja behörigheter](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Kontrol lera under aviseringar för att avgöra om behörigheterna har beviljats.  Om de inte gör det fungerar inte AzureCP korrekt och det går inte att konfigurera SharePoint lokalt med Azure Active Directory säkerhets grupper.

10. Konfigurera AzureCP på den lokala SharePoint-gruppen eller en annan lösning för anspråks leverantörer.  I det här exemplet använder vi AzureCP.

    > [!NOTE]
    > Observera att AzureCP inte är en Microsoft-produkt eller som stöds av Microsofts tekniska support. Hämta, installera och konfigurera AzureCP i den lokala SharePoint-servergruppen per https://yvand.github.io/AzureCP/ 

11. **Bevilja åtkomst till Azure Active Directory säkerhets grupp i den lokala SharePoint-gruppen** :-grupperna måste beviljas åtkomst till programmet i SharePoint lokalt.  Använd följande steg för att ange åtkomst behörighet till webb programmet.

12. I Central Administration klickar du på program hantering, hanterar webb program och väljer sedan webb programmet för att aktivera menyfliksområdet och klickar på användar princip.

    ![Central administration](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Under princip för webb program klickar du på Lägg till användare och väljer sedan zonen. Klicka sedan på Nästa.  Klicka på adress boken.

    ![Princip för webb program](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Sök sedan efter och Lägg till säkerhets gruppen Azure Active Directory och klicka på OK.

    ![Lägger till säkerhets grupp](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Välj behörigheter och klicka sedan på Slutför.

    ![Lägger till säkerhets grupp](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. I under princip för webb program läggs gruppen Azure Active Directory till.  Grupp anspråket visar Azure Active Directory säkerhets grupp objekt-ID för användar namnet.

    ![Lägger till säkerhets grupp](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Bläddra till SharePoint-webbplatssamling och Lägg till gruppen där. Klicka på plats inställningar och klicka sedan på webbplats behörigheter och bevilja behörigheter.  Sök efter grupp Rolls anspråket, tilldela behörighets nivån och klicka på dela.

    ![Lägger till säkerhets grupp](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurera en betrodd identitetsprovider för flera webbprogram

Konfigurationen fungerar för en enkel webbapp, men ytterligare konfiguration krävs om du planerar att använda samma betrodda identitetsprovider för flera webbprogram. Anta exempelvis att vi hade utökat ett webbprogram till att använda URL:en `https://portal.contoso.local` och nu också vill autentisera användare till `https://sales.contoso.local`. För att göra det så måste vi uppdatera identitetsprovider för att respektera parametern WReply och uppdatera programregistreringen i Azure AD för att lägga till en svars-URL.

1. Öppna Azure AD-katalogen i Azure Portal. Klicka på **Appregistreringar** och därefter på **Visa alla program**. Klicka på det program som du skapade tidigare (SharePoint SAML-integrering).

2. Klicka på **Inställningar**.

3. På inställningar-bladet, klickar du på **Svars-URL:er**.

4. Lägg till URL:en för ytterligare webbprogram med `/_trust/default.aspx` tillagt till URL:en (till exempel `https://sales.contoso.local/_trust/default.aspx`) och klicka på **Spara**.

5. På SharePoint-servern öppnar du **Hanteringsgränssnittet för SharePoint 2016** och kör följande kommandon med namnet på den betrodda identitetstokenutfärdare som du använde tidigare.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. I Central Administration går du till webbprogrammet och aktiverar den befintliga betrodda identitetsprovidern. Kom ihåg att även konfigurera inloggningssidans URL som en anpassad inloggningssida `/_trust/`.

7. I Central Administration, klickar du på webbprogrammet och väljer **Användarprincip**. Lägg till en användare med lämplig behörighet som det visas tidigare i den här artikeln.

### <a name="fixing-people-picker"></a>Åtgärda personväljaren

Användare kan nu logga in på SharePoint 2016 med identiteter från Azure AD, men det finns fortfarande möjligheter att förbättra användar upplevelsen. Om du till exempel söker efter en användare så visas flera sökresultat i personväljaren. Det finns ett sökresultat för var och en av de 3 anspråkstyperna som skapades i anspråksmappningen. Om du vill välja en användare med personväljaren så måste du ange deras användarnamn exakt och välja anspråksresultatet **Name**.

![Anspråkssökresultat](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Det finns ingen validering på de värden som du söker efter, vilket kan leda till felstavningar eller att användare väljer fel anspråkstyp att tilldela som anspråket **SurName**. Det kan förhindra att användare får åtkomst till resurserna.

För att hjälpa till vid det här scenariot så finns det en öppen källkodslösning som heter [AzureCP](https://yvand.github.io/AzureCP/) som ger en anpassad anspråksprovider för SharePoint 2016. Den använder Azure AD Graph för att matcha vad användare skriver in och utföra validering. Läs mer på [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Tilldela Azure AD-säkerhetsgruppen i Azure Portal

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **lokal SharePoint**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver och väljer du **lokal SharePoint**.

    ![Länken till lokal SharePoint i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. Sök efter den säkerhets grupp du vill använda och klicka sedan på gruppen för att lägga till den i avsnittet Välj medlemmar. Klicka på **Välj**och sedan på **tilldela**.

    ![Sök säkerhets grupp](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Kontrol lera meddelandena i meny raden för att meddelas att gruppen har tilldelats till företags programmet i Azure Portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Skapa lokal SharePoint-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i lokal SharePoint. Arbeta med [supportteamet för lokal SharePoint](https://support.office.com/) för att lägga till användarna i den lokala SharePoint-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på lokal SharePoint-panelen i Åtkomstpanelen så bör du automatiskt loggas in på den lokala SharePoint för vilken du konfigurerade enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
