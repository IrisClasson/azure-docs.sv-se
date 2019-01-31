---
title: Självstudiekurs – Aktivera en webbapp för autentisering med konton med hjälp av Azure Active Directory B2C | Microsoft Docs
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ASP.NET-webbapp.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 714d733e765f28d1244f6ee1c7b1cb237c0c4b1f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198358"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>Självstudier: Aktivera en webbapp för autentisering med konton med hjälp av Azure Active Directory B2C

Den här självstudien lär dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ASP.NET webbapp. Med Azure AD B2C kan appar autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med öppna protokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera en ASP.NET exempelwebbapp i en Azure AD B2C-klientorganisation.
> * Skapa användarflöden för registrering av användare, inloggning, redigering av profil och återställning av lösenord.
> * Konfigurera exempelwebbappen så att den använder din Azure AD B2C-klientorganisation. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Skapa en egen [Azure AD B2C-klientorganisation](active-directory-b2c-get-started.md)
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**.

## <a name="register-web-app"></a>Registrera webbappen

Program måste [registreras](../active-directory/develop/developer-glossary.md#application-registration) i klientorganisationen innan de kan ta emot [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) från Azure Active Directory. Programregistrering skapar ett [program-ID](../active-directory/develop/developer-glossary.md#application-id-client-id) för appen i din klientorganisation. 

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**. Du bör nu använda den klient som du skapade i den föregående självstudien. 

2. I B2C-inställningarna klickar du på **Program** och sedan på **Lägg till**. 

    Registrera exempelwebbappen i klientorganisationen med följande inställningar:

    ![Lägg till en ny app](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | Min exempelwebbapp | Ange ett **Namn** som beskriver appen för konsumenterna. | 
    | **Ta med webbapp/webb-API** | Ja | Välj **Ja** om det är en webbapp. |
    | **Tillåt implicit flöde** | Ja | Välj **Ja** eftersom appen använder [OpenID Connect-inloggning](active-directory-b2c-reference-oidc.md). |
    | **Svarswebbadress** | `https://localhost:44316` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som appen begär. I den här självstudien körs exemplet lokalt (lokal värd) och lyssnar på port 44316. |
    | **Inkludera intern klient** | Nej | Eftersom det här är en webbapp och inte en intern klient väljer du Nej. |
    
3. Klicka på **Skapa** för att registrera din app.

Registrerade appar visas i programlistan för Azure AD B2C-klientorganisationen. Välj webbapp i listan. Webbappens egenskapsruta visas.

![Webbappegenskaper](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Anteckna **Program-ID**. Detta ID identifierar appen och behövs när appen konfigureras senare under självstudierna.

### <a name="create-a-client-password"></a>Skapa ett klientlösenord

Azure AD B2C använder OAuth2-auktorisering för [klientprogram](../active-directory/develop/developer-glossary.md#client-application). Webbappar är [konfidentiella klienter](../active-directory/develop/developer-glossary.md#web-client) och kräver ett klient-ID eller program-ID och en klienthemlighet, ett klientlösenord eller en programnyckel.

1. Välj sidan Nycklar för den registrerade webbappen och klicka på **Generera nyckel**.

2. Klicka på **Spara** så visas appnyckeln.

    ![sidan nycklar för allmänna appar](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

Nyckeln visas en gång i portalen. Det är viktigt att nyckelvärdet kopieras och sparas. Värdet behövs när appen konfigureras. Förvara nyckeln säkert. Dela inte nyckeln offentligt.

## <a name="create-user-flows"></a>Skapa användarflöden

Azure AD B2C-användarflöden definierar användargränssnittet för en identitetsuppgift. Till exempel är vanliga användarflöden att logga in, registrera sig, byta lösenord och redigera profiler.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Skapa ett användarflöde för registrering eller inloggning

Skapa en **användarflöde för registrering eller inloggning** om du vill registrera användare och sedan låta dem logga in på webbappen.

1. På Azure AD B2C-portalsidan väljer du **Användarflöden** och klickar på **Nytt användarflöde**.
2. På fliken **Rekommenderat** klickar du på **Sign up and sign in** (Registrera och logga in).

    Konfigurera användarflödet med följande inställningar:

    ![Lägga till ett användarflöde för registrering eller inloggning](media/active-directory-b2c-tutorials-web-app/add-susi-user-flow.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiUpIn | Ange ett **Namn** för användarflödet. Användarflödets namn har prefixet **b2c_1_**. Använd det fullständiga användarflödesnamnet **b2c_1_SiUpIn** i exempelkoden. | 
    | **Identitetsprovidrar** | E-postregistrering | Den identitetsprovider som används för att unikt identifiera användaren. |

3. Under **Användarattribut och anspråk** klickar du på **Visa mer** och väljer följande inställningar:

    ![Lägga till ett användarflöde för registrering eller inloggning](media/active-directory-b2c-tutorials-web-app/add-attributes-and-claims.png)

    | Kolumn      | Föreslagna värden  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Samla in attribut** | Visningsnamn och Postnummer | Välj vilka attribut som samlas in från användaren under registreringen. |
    | **Returanspråk** | Visningsnamn, postnummer, användare är ny, användarens objekt-ID | Välj [anspråk](../active-directory/develop/developer-glossary.md#claim) som ska tas med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token). |

4. Klicka på **OK**.
5. Klicka på **Skapa** för att skapa användarflödet. 

### <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna själva ska kunna återställa informationen i sin användarprofil skapar du ett **användarflöde för profilredigering**.

1. På Azure AD B2C-portalsidan väljer du **Användarflöden** och klickar på **Nytt användarflöde**.
2. På fliken **Rekommenderat** klickar du på **Profilredigering**.

    Konfigurera användarflödet med följande inställningar:

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SiPe | Ange ett **Namn** för användarflödet. Användarflödets namn har prefixet **b2c_1_**. Använd det fullständiga användarflödesnamnet **b2c_1_SiPe** i exempelkoden. | 
    | **Identitetsprovidrar** | Inloggning på lokalt konto | Den identitetsprovider som används för att unikt identifiera användaren. |

3. Under **Användarattribut** klickar du på **Visa mer** och väljer följande inställningar:

    | Kolumn      | Föreslagna värden  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Samla in attribut** | Visningsnamn och Postnummer | Välj de attribut en användare kan ändra under profilredigering. |
    | **Returanspråk** | Visningsnamn, postnummer, användarens objekt-ID | Välj de [anspråk](../active-directory/develop/developer-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) efter en lyckad profilredigering. |

4. Klicka på **OK**.
5. Klicka på **Skapa** för att skapa användarflödet. 

### <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill kunna aktivera lösenordsåterställning i programmet behöver du skapa ett **användarflöde för lösenordsåterställning**. Det här användarflödet beskriver hur lösenordsåterställningen går till för konsumenterna och innehållet i de token som programmet tar emot när återställningen genomförts.

1. I Azure AD B2C-portalsidan väljer du **Princip för lösenordsåterställning** och klickar på **Lägg till**.
2. På fliken **Rekommenderat** klickar du på **Återställning av lösenord**.

    Konfigurera användarflödet med följande inställningar.

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | SSPR | Ange ett **Namn** för användarflödet. Användarflödets namn har prefixet **b2c_1_**. Använd det fullständiga användarflödesnamnet **b2c_1_SSPR** i exempelkoden. | 
    | **Identitetsprovidrar** | Återställ lösenord med e-postadress | Den identitetsprovider som används för att unikt identifiera användaren. |

3. Under **Programanspråk** klickar du på **Visa mer** och väljer följande inställning:
    | Kolumn      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Returanspråk** | Användarens objekt-ID | Välj de [anspråk](../active-directory/develop/developer-glossary.md#claim) du vill ta med i [åtkomsttoken](../active-directory/develop/developer-glossary.md#access-token) efter en lyckad lösenordsåterställning. |

4. Klicka på **OK**.
5. Klicka på **Skapa** för att skapa användarflödet. 

## <a name="update-web-app-code"></a>Uppdatera webbappens kod

Nu när du har registrerat en webbapp och skapat användarflöden behöver du konfigurera appen så att den använder din Azure AD B2C-klientorganisation. I den här självstudien får konfigurera en exempelwebbapp som du kan ladda ned från GitHub. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub. Se till att extrahera exempelfilen i en mapp där sökvägens totala teckenlängd är mindre än 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

ASP.NET exempelwebbappen är en enkel app som kan skapa och uppdatera en uppgiftslista. Appen använder [Microsoft OWIN mellanprogramkomponenter](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/) och låter användare registrera sig för att använda appen i din Azure AD B2C-klientorganisation. Genom att ett Azure AD B2C-användarflöde skapas kan användare använda ett konto för sociala media eller skapa ett konto för att kunna använda appen. 

Exempellösningen innehåller två projekt:

**Exempelwebbapp (TaskWebApp):** Webbapp för att skapa och redigera en uppgiftslista. Webbappen använder användarflödet för **registrering eller inloggning** för att registrera eller logga in användare.

**Exempelapp med webb-API (TaskWebApp):** Ett webb-API med stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. Webb-API:n skyddas av Azure AD B2C och anropas av webbappen.

Du måste ändra appen om du vill använda appregistreringen i din klientorganisation, som innehåller det program-ID och den nyckel som du tidigare registrerade. Du behöver även konfigurera de användarflöden som du skapade. Exempelwebbappen definierar konfigurationsvärdena som appinställningar i Web.config-filen. Så här ändrar du appinställningarna:

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.

2. I webbapprojektet **TaskWebApp** öppnar du **Web.config**-filen. Ersätt värdet för `ida:Tenant` med namnet på den klientorganisation som du skapade. Ersätt värdet för `ida:ClientId` med det program-ID som du registrerade. Ersätt värdet för `ida:ClientSecret` med den nyckel som du registrerade.

3. I filen **Web.config** ersätter du värdet för `ida:SignUpSignInPolicyId` med `b2c_1_SiUpIn`. Ersätt värdet för `ida:EditProfilePolicyId` med `b2c_1_SiPe`. Ersätt värdet för `ida:ResetPasswordPolicyId` med `b2c_1_SSPR`.

## <a name="run-the-sample-web-app"></a>Kör exempelwebbappen

Högerklicka på **TaskWebApp**-projektet i Solution Explorer och klicka på **Ställ in som startprojekt**

Starta webbappen genom att trycka på **F5**. Standardwebbläsaren öppnas med den lokala webbplatsadressen `https://localhost:44316/`. 

Exempelappen har stöd för registrering av användare, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig för att använda programmet med en e-postadress. Du kan själv prova andra scenarion.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på länken **Registrera dig eller Logga in** längst upp och registrera dig som användare. Då används användarflödet **b2c_1_SiUpIn**, som du definierade i ett tidigare steg.

2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 

3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-klientorganisationen.

Användaren kan nu använda e-postadressen och logga in och använda webbappen.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en Azure AD B2C-klientorganisation, skapa användarflöden och uppdatera exempelwebbappen så den använder din Azure AD B2C-klientorganisation. Fortsätt till nästa självstudie och lär dig registrera, konfigurera och anropa ett ASP.NET webb-API som skyddas av din Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Självstudier: Använda Azure Active Directory B2C för att skydda ett ASP.NET webb-API](active-directory-b2c-tutorials-web-api.md)
