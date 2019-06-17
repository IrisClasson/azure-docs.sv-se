---
title: 'Självstudier: Azure Active Directory-integrering med SAP NetWeaver | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01a70bfbb169090fbdf4b5f8e00af6895f69c964
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091920"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Självstudier: Azure Active Directory-integration med SAP NetWeaver

I den här självstudien får du lära dig hur du integrerar SAP NetWeaver med Azure Active Directory (Azure AD).
När du integrerar SAP NetWeaver med Azure Active Directory innebär det följande fördelar:

* Du kan styra vem som har åtkomst till SAP NetWeaver i Azure Active Directory.
* Du kan göra så att dina användare automatiskt loggas in på SAP NetWeaver (enkel inloggning) med sina Azure Active Directory-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure Active Directory-integrering med SAP NetWeaver behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAP NetWeaver-prenumeration med enkel inloggning aktiverat
* SAP NetWeaver V7.20 krävs minst

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP NetWeaver stöder **SP**-initierad enkel inloggning

## <a name="adding-sap-netweaver-from-the-gallery"></a>Lägga till SAP NetWeaver från galleriet

Om du vill konfigurera integreringen av SAP NetWeaver i Azure Active Directory måste du lägga till SAP NetWeaver från galleriet till din lista över hanterade SaaS-appar.

**Lägg till SAP NetWeaver från galleriet genom att göra följande:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **SAP NetWeaver** i sökrutan, välj **SAP NetWeaver** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

     ![SAP NetWeaver i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure Active Directory med SAP NetWeaver baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure Active Directory-användare och den relaterade användaren i SAP NetWeaver upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure Active Directory med SAP NetWeaver måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAP NetWeaver](#configure-sap-netweaver-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa en testanvändare för SAP NetWeaver](#create-sap-netweaver-test-user)** – så att du får en motsvarighet till Britta Simon i SAP NetWeaver som är länkad till användarens Azure Active Directory-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure Active Directory med SAP NetWeaver genom att göra följande:

1. Öppna ett nytt webbläsarfönster och logga in på SAP NetWeaver-företagssidan som administratör

2. Se till att tjänsterna **http** och **https** är aktiva och att lämpliga portar tilldelas i transaktionskoden **SMICM**.

3. Logga in på SAP-systemets företagsklient (T01), där enkel inloggning krävs och aktivera hantering av HTTP-säkerhetssession.

    a. Gå till transaktionskod **SICF_SESSIONS**. Den visar alla relevanta profilparametrar med aktuella värden. De ser ut så här:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Justera parametrarna ovan enligt organisationens krav. Parametrarna ovan anges här endast som indikation.

    b. Om det behövs justerar du parametrarna i instansen/standardprofilen för SAP-systemet och startar om SAP-systemet.

    c. Dubbelklicka på relevant klient för att aktivera HTTP-säkerhetssessionen.

    ![Länk för hämtning av certifikat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivera nedan SICF-tjänsterna nedan:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Gå till transaktionskod **SAML2** i SAP-systemets företagsklient [T01/122]. Ett användargränssnitt öppnas i en webbläsare. I det här exemplet antog vi att SAP-företagsklienten är 122.

    ![Länk för hämtning av certifikat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Ange ditt användarnamn och lösenord som ska anges i användargränssnittet och klicka på **Redigera**.

    ![Länk för hämtning av certifikat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Ersätt **providernamn** från T01122 till `http://T01122` och klicka på **Spara**.

    > [!NOTE]
    > Som standard providernamn medföljer som `<sid><client>` formatet, men Azure AD förväntar sig namnet i formatet `<protocol>://<name>`rekommendationer att underhålla providernamn som `https://<sid><client>` så att flera SAP NetWeaver ABAP-motorer att konfigurera i Azure AD.

    ![Länk för hämtning av certifikat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generera metadata för tjänstleverantör**: – När vi är klara med att konfigurera inställningarna för den **lokala leverantören** och **betrodda leverantörer** i användargränssnittet för SAML 2.0 är nästa steg att generera tjänstleverantörens metadatafil (som innehåller alla inställningar, när det gäller autentisering och andra konfigurationer i SAP). När den här filen har genererats måste vi ladda upp den i Azure AD.

    ![Länk för hämtning av certifikat](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Gå till **fliken Local Provider** (Lokal provider).

    b. Klicka på **Metadata**.

    c. Spara den genererade **Metadata-XML-filen** på datorn och ladda upp den i **SAML grundkonfiguration** avsnitt för att automatiskt fylla i den **identifierare** och  **Svars-URL** värdena i Azure-portalen.

8. Välj [Enkel inloggning](https://portal.azure.com/) på programintegreringssidan för **SAP NetWeaver** i **Azure-portalen**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

9. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

10. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

11. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Klicka på **Ladda upp metadatafil** för att ladda upp **tjänstleverantörens metadatafil** som du fått tidigare.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i textrutan i avsnittet **Grundläggande SAML-konfiguration** enligt följande:

    ![Information om SAP NetWeaver-domän och URL:er för enkel inloggning](common/sp-identifier-reply.png)

    d. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Vi har fått rapporter från några kunder om ett fel med felaktig svars-URL som har konfigurerats för deras instans. Om du får ett sådant fel kan du använda följande PowerShell-skript som en tillfällig åtgärd för att ange rätt svars-URL för din instans:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal Object ID ska anges av dig först, eller så kan du skicka det också här.

12. SAP NetWeaver-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

13. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Välj **ExtractMailPrefix()** i listan **Transformering**.

    c. Välj **user.userprinicipalname** i listan **Parameter 1**.

    d. Klicka på **Spara**.

14. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

15. I avsnittet **Konfigurera SAP NetWeaver** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sap-netweaver-single-sign-on"></a>Konfigurera enkel inloggning för SAP NetWeaver

1. Logga in till SAP-system och gå till transaktionskod SAML2. Ett nytt webbläsarfönster öppnas med en skärm för konfiguration av SAML.

2. För att konfigurera slutpunkter för betrodda identitetsproviders (Azure AD) går du till fliken **Trusted Providers** (Betrodda providers).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Tryck på **Lägg till** och välj **Ladda upp metadatafil** på snabbmenyn.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Ladda upp metadatafilen som du har laddat ned från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Ange aliasnamnet på nästa skärm, till exempel aadsts, och tryck på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Se till att din **sammandragsalgoritm** ska vara **SHA-256** och att den inte kräver några ändringar och tryck på **Nästa**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. På **Single Sign-On Endpoints** (Slutpunkter för enkel inloggning) använder du **HTTP POST** och klickar på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. På **Single Logout Endpoints** (Slutpunkter för enkel utloggning) använder du **HTTP POST** och klickar på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. På **Artifact Endpoints** (Slutpunkter för artefakter) trycker du på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Vid **Autentiseringskrav** klickar du på **Slutför**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Gå till fliken **Trusted Provider** (Betrodd provider) > **Identitetsfederation** (längst ned på skärmen). Klicka på **Redigera**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klicka på **Lägg till** under fliken **Identitetsfederation** (det nedre fönstret).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. I popup-fönstret väljer du **Ospecificerad** från **Supported NameID formats** (NameID-format som stöds) och klickar på OK.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Observera att värdena för **användar-ID-källa** och **användar-ID-mappningsläge** bestämmer länken mellan SAP-användare och Azure AD-anspråk.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: SAP-användare till Azure AD-mappning.

    a. Skärmbild med NameID-information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Skärmbild som nämner nödvändiga anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Välj SAP-användar-id baserat på konfigurerad e-postadress i SU01. I det här fallet konfigureras e-post-id i su01 för varje användare som kräver enkel inloggning.

    a.  Skärmbild med NameID-information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. skärmbild som nämner nödvändiga anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klicka på **Spara** och klicka sedan på **Aktivera** för att aktivera identitetsprovider.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration1.png)

16. Klicka på **OK** när du uppmanas till detta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet får du aktivera Britta Simon till att använda enkel inloggning i Azure genom att bevilja henne åtkomst till SAP NetWeaver.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **SAP NetWeaver**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program väljer du **SAP NetWeaver**.

    ![SAP NetWeaver-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-netweaver-test-user"></a>Skapa SAP NetWeaver-testanvändare

I det här avsnittet får du skapa en användare som kallas Britta Simon i SAP NetWeaver. Arbeta med ditt interna SAP-expertteam eller med din organisations SAP-partner för att lägga till användare i SAP NetWeaver-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

1. När identitetsprovidern Azure AD aktiverades försöker du öppna nedanstående URL för att kontrollera SSO (det kommer ingen uppmaning för användarnamn och lösenord)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (eller) använd webbadressen nedan

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Ersätt sapurl med själva SAP-värdnamnet.

2. Webbadressen ovan bör ta dig under den nämnda skärmen. Om du kan komma till sidan nedan är Azure AD SSO-installationen klar.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/testingsso.png)

3. Om du ser en uppmaning för användarnamn och lösenord ska du diagnostisera problemet genom att aktivera spårning med hjälp av nedanstående URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
