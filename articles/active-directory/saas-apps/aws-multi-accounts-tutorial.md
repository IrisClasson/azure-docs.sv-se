---
title: 'Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) att ansluta flera konton | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure AD och flera konton för Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 322615203d188581dd04aadeff2a08307b733d06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65738041"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Självstudier: Azure Active Directory-integrering med flera konton för Amazon Web Services (AWS)

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD) med flera konton för Amazon Web Services (AWS).

Integreringen av Amazon Web Services (AWS) med Azure AD medför följande fördelar:

- Du kan i Azure AD styra vem som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att automatiskt få loggat in till Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Observera att ansluta ett AWS-app till din AWS-konton inte är den rekommenderade metoden. I stället rekommenderar vi att du använder [detta](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metod för att konfigurera flera instanser av AWS-konto till flera instanser av AWS-appar i Azure AD.

**Observera att vi inte rekommenderar för att använda den här metoden följande skäl:**

* Du måste använda Graph-testaren-metoden för att korrigera alla roller för appen. Vi rekommenderar inte manifestfilen-metoden.

* Vi har sett kunder reporting att när du lägger till ~ 1200 roller för en enda AWS-app, igång en åtgärd på appen i fel relaterade till storlek. Det finns en hård gräns på storleken för programobjektet.

* Du måste manuellt uppdatera rollen som rollerna läggs i någon av kontona som är en Ersätt metod och inte Append tyvärr. Även om dina konton växer sedan detta blir n x n-relation med konton och roller.

* AWS-konton kommer att använda samma Federation Metadata-XML-filen och vid tidpunkten för förnya certifikatet du genomför den här omfattande Övning för att uppdatera certifikatet på AWS-konton på samma gång

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS)-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Amazon Web Services (AWS)** , väljer **Amazon Web Services (AWS)** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Amazon Web Services (AWS) i resultatlistan](common/search-new-app.png)

5. När programmet har lagts till, går du till **egenskaper** sida och kopiera den **objekt-ID**.

    ![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Amazon Web Services (AWS) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

I Amazon Web Services (AWS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

För att konfigurera och testa enkel inloggning med Azure AD med Amazon Web Services (AWS) behöver du utföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Amazon Web Services (AWS)** -programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![image](common/preintegrated.png)

5. Amazon Web Services (AWS)-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn  | Källattribut  | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Roll            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  https://aws.amazon.com/SAML/Attributes |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I textrutan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **Federation Metadata-XML**  och spara den på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurera enkel inloggning för Amazon Web Services (AWS)

1. I ett annat webbläsarfönster loggar du in på din Amazon Web Services (AWS)-företagsplats som administratör.

2. Klicka på **AWS Home**.

    ![Konfigurera start för enkel inloggning][11]

3. Klicka på **Identitets- och åtkomsthantering**.

    ![Konfigurera identitet för enkel inloggning][12]

4. Klicka på **Identitetsprovidrar** och sedan på **Skapa provider**.

    ![Konfigurera provider för enkel inloggning][13]

5. På dialogsidan **Konfigurera provider** utför du följande steg:

    ![Dialogruta för att konfigurera enkel inloggning][14]

    a. För **Providertyp** väljer du **SAML**.

    b. I textrutan **Providernamn** skriver du ett providernamn (till exempel: *WAAD*).

    c. För att ladda upp den nedladdade **metadatafilen** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **Nästa steg**.

6. På dialogsidan **Verifiera providerinformation** klickar du på **Skapa**.

    ![Konfigurera verifiering för enkel inloggning][15]

7. Klicka på **Roller** och sedan på **Skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Konfigurera förtroende för enkel inloggning][19]

    a. Välj **SAML 2.0-federation** under **Select type of trusted entity** (Välj typ av betrodd entitet).

    b. I avsnittet **Välj en SAML 2.0-provider** väljer du den **SAML-provider** som du skapade tidigare (till exempel: *WAAD*)

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Klicka på **Nästa: Behörigheter**.

9. I dialogrutan **Attach Permissions Policies** (Koppla behörighetsprinciper) kopplar du lämplig princip enligt din organisation. Klicka på **Nästa: Granskning**.  

    ![Konfigurera princip för enkel inloggning][33]

10. I dialogrutan **Granskning** utför du följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. I textrutan **Rollnamn** anger du ditt rollnamn.

    b. I textrutan **Rollbeskrivning** anger du beskrivningen.

    c. Klicka på **Skapa roll**.

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

11. Logga ut från aktuella AWS-konto och logga in med andra konto där du vill konfigurera enkel inloggning på med Azure AD.

12. Utför steg-2-steg-10 för att skapa flera roller som du vill att installationsprogrammet för det här kontot. Om du har fler än två konton, utför samma steg för alla konton för att skapa roller för dessa.

13. När alla roller har skapats i konton som de visas i den **roller** för dessa konton.

    ![Installationen av roller](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Vi behöver att avbilda alla rollen ARN och betrodda entiteter för alla roller för samtliga konton, vilket vi behöver för att mappa manuellt med Azure AD-program.

15. Klicka på rollerna som ska kopiera **rollen ARN** och **betrodda entiteter** värden. Du behöver dessa värden för alla roller som du behöver skapa i Azure AD.

    ![Installationen av roller](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Utför ovanstående steg för alla roller i alla konton och lagra dem alla i formatet **rollen ARN, betrodda entiteter** i anteckningar.

17. Öppna [Azure AD Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på webbplatsen Graph-testaren med autentiseringsuppgifterna som Global administratör/medadministratör för din klient.

    b. Du måste ha tillräcklig behörighet för att skapa roller. Klicka på **ändringsbehörigheter** att få behörigheterna som krävs.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte har dessa redan) och klicka på ”Ändra behörigheter” 

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Detta blir du ombedd att logga in igen och acceptera samtycke. Efter att du godkänt medgivande, är du inloggad på Grafutforskaren igen.

    e. Ändra version listrutan till **beta**. Om du vill hämta alla huvudnamn från din klient, använder du följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger kan du använda följande mönster som har din primära domän `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Från listan över tjänstens huvudnamn som hämtas, får du det du behöver ändra. Du kan också använda Ctrl + F för att söka efter programmet från alla listade ServicePrincipals. Du kan använda följande fråga med hjälp av den **objekt-id** som du har kopierat från Azure AD-egenskaper-sidan för att komma till respektive tjänstens huvudnamn.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahera egenskapen appRoles från det tjänstens huvudnamnsobjekt.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nu måste du generera nya roller för programmet. 

    i. Är ett exempel på appRoles objekt nedan JSON. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Du kan bara lägga till nya roller efter den **msiam_access** för patch-åtgärden utfördes. Dessutom kan du lägga till så många roller som du vill ha per behovet av din organisation. Azure AD skickar den **värdet** av dessa roller som anspråksvärdet i SAML-svar.

    j. Gå tillbaka till Graph-testaren och ändra metod från **hämta** till **KORRIGERA**. Korrigera Service Principal-objekt om du vill ha önskad roller genom att uppdatera appRoles egenskapen liknar det ovan i det här exemplet. Klicka på **Kör fråga** att köra patch-åtgärden utfördes. Ett meddelande bekräftar att skapa rollen för Amazon Web Services-program.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. När tjänstens huvudnamn är uppdaterad med flera roller, kan du tilldela olika roller användare/grupper. Detta kan göras genom att gå till portalen och gå till Amazon Web Services-programmet. Klicka på den **användare och grupper** fliken längst upp.

19. Vi rekommenderar att du kan skapa nya grupper för varje roll i AWS så att du kan tilldela specifika rollen i gruppen. Observera att detta är en-till-en-mappning för en grupp till en roll. Du kan sedan lägga till de medlemmar som tillhör denna grupp.

20. När gruppen har skapats, Välj gruppen och tilldela till programmet.

    ![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Kapslade grupper stöds inte när du tilldelar grupper.

21. Om du vill tilldela rollen till gruppen, Välj rollen och klicka på **tilldela** längst ned på sidan.

    ![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observera att du behöver uppdatera din session i Azure portal för att se nya roller.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomstpanelen, bör du få programsidan för Amazon Web Services (AWS) med möjlighet att välja rollen.

![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Du kan också kontrollera SAML-svar för att se de roller som skickas som anspråk.

![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
