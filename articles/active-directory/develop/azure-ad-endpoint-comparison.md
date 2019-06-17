---
title: Varför uppdateras till Microsoft identity-plattformen (v2.0) | Azure
description: Vet skillnaderna mellan Microsoft identity-plattformen (v2.0) slutpunkt och Azure Active Directory (Azure AD) v1.0 slutpunkt och Läs om fördelarna med att uppdatera till version 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ccac719c78ce2844a8dd37a80445e11baa4a488
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962871"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Varför ska du uppdatera till Microsoft identity-plattformen (v2.0)?

När du utvecklar ett nytt program är det viktigt att veta skillnaderna mellan Microsoft identity-plattformen (v2.0) och Azure Active Directory (v1.0)-slutpunkter. Den här artikeln beskriver de viktigaste skillnaderna mellan slutpunkterna och vissa befintliga begränsningar för Microsoft identity-plattformen.

> [!NOTE]
> Microsoft identity-plattformen slutpunkt stöder inte alla Azure AD-scenarier och funktioner. Läs mer om för att avgöra om du ska använda Microsoft identity-plattformen endpoint, [plattformsbegränsningar för Microsoft identity](#limitations).

## <a name="who-can-sign-in"></a>Vem som kan logga in

![Vem som kan logga in med v1.0 och v2.0-slutpunkter](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* V1.0-slutpunkten tillåter bara arbets- och skolkonton konton att logga in på ditt program (Azure AD)
* Microsoft identity-plattformen endpoint kan arbete och skola konton från Azure AD och personliga Microsoft-konton (MSA), till exempel hotmail.com, outlook.com och msn.com, logga in.
* Båda slutpunkterna också acceptera inloggningar för *[gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* av Azure AD-katalog för program som har konfigurerats som *[enda klient](single-and-multi-tenant-apps.md)* eller för *flera innehavare* program som har konfigurerats för att peka på den klientspecifik-slutpunkten (`https://login.microsoftonline.com/{TenantId_or_Name}`).

Microsoft identity-plattformen slutpunkten kan du skriva appar som har stöd för inloggningar från personliga Microsoft-konton och arbets-och skolkonton. Det gör att du kan skriva din app helt konto-oberoende. Exempel: om appen anropar den [Microsoft Graph](https://graph.microsoft.io), vissa ytterligare funktioner och data kommer att kunna arbeta konton, till exempel sina SharePoint-webbplatser eller katalogdata. Men för många åtgärder, till exempel [läsning av en användares e-post](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), samma kod kan komma åt e-post för både personliga och arbets och skolkonton.

Du kan använda Microsoft Authentication Library (MSAL) för Microsoft identity-plattformen slutpunkten för att få tillgång till konsumenten, högskolor, och enterprise världar. Azure AD v1.0 slutpunkten accepterar inloggningar från arbets- och skolkonton konton.

## <a name="incremental-and-dynamic-consent"></a>Inkrementell och dynamiska medgivande

Appar med hjälp av Azure AD v1.0 slutpunkten krävs för att ange sina OAuth 2.0-behörigheter som krävs i förväg, till exempel:

![Behörigheter för registrering av Användargränssnittet](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Behörighetsinställningar direkt från programregistrering är **Statiska**. Medan statiska behörigheterna för den app som definierats i Azure-portalen Håll koden bra och enkelt, anger möjliga problem för utvecklare:

* Appen måste begära alla behörigheter som den skulle behöver vid användarens första inloggning. Detta kan leda till en lång lista med behörigheter som förhindrar användare från att godkänna appens åtkomst på första inloggning.

* Appen måste känner till alla de resurser som den skulle någonsin åt förbereds i förväg. Det var svårt att skapa appar som kan komma åt ett valfritt antal resurser.

Med Microsoft identity-plattformen slutpunkt kan du ignorera statiska behörigheter som definierats i registreringsinformation app i Azure portal och begäran behörigheterna stegvis i stället vilket innebär att be om en utan minsta uppsättning behörigheter förskott och växande mer med tiden då kunden använder ytterligare funktioner. Om du vill göra det, du kan ange omfång som din app behöver när som helst genom att inkludera de nya scope i den `scope` parameter när du begär en åtkomsttoken - utan att behöva förväg definiera dem i registreringsinformation för programmet. Om användaren ännu inte har samtyckt till nya scope som lagts till i begäran, uppmanas de att endast godkänna de nya behörigheterna. Mer information finns i [behörigheter och samtycke scope](v2-permissions-and-consent.md).

Så att en app för att begära behörighet dynamiskt via den `scope` parametern ger utvecklarna fullständig kontroll över din användarupplevelse. Du kan också klientdelens ditt medgivande upplevelse och be om alla behörigheter i en inledande auktoriseringsbegäran. Om din app kräver ett stort antal behörigheter, du kan samla in dessa behörigheter från användaren stegvis när de försöker att använda vissa funktioner i appen med tiden.

Administratörens godkännande klar på ett annat företag kräver fortfarande de statiska behörigheter som har registrerats för appen, så du bör ange dessa behörigheter för appar i portalen för registrering av appen om du behöver en administratör ge ditt medgivande för hela organisationen. Detta minskar cykler som krävs av administratören organisation för att konfigurera programmet.

## <a name="scopes-not-resources"></a>Scope, inte resurser

För appar som använder v1.0 slutpunkten kan en app kan fungera som en **resource**, eller en mottagare av token. En resurs kan definiera ett antal **scope** eller **oAuth2Permissions** som den förstår, vilket gör att klientdatorer appar att begära token från den resursen för en viss uppsättning omfång. Överväg att Azure AD Graph-API som ett exempel på en resurs:

* Resurs-ID eller `AppID URI`: `https://graph.windows.net/`
* Scope, eller `oAuth2Permissions`: `Directory.Read`, `Directory.Write`och så vidare.

Detta gäller för Microsoft identity-plattformen slutpunkten. En app kan fortfarande fungera som en resurs, definiera omfång och identifieras av en URI. Klientappar kan fortfarande att begära åtkomst till dessa omfattningar. Men det sätt som en klient begär att dessa behörigheter har ändrats.

För att auktorisera v1.0 slutpunkten, en OAuth 2.0 kan begäran till Azure AD har tittat som:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Här är den **resource** parametern anges vilken resurs som klientappen begär auktorisering. Azure AD beräknade de behörigheter som krävs av appen utifrån statisk konfiguration i Azure portal och därefter utfärdade token.

För program som använder Microsoft identity-plattformen endpoint, auktorisera samma OAuth 2.0 begäran ut:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Här är den **omfång** parametern anger vilka resurser och behörigheter appen begär auktorisering. Önskad resurs finns kvar i begäran – det är finns i vart och ett av värdena för omfångets parameter. Använda omfattningsparametern i det här sättet kan Microsoft identity-plattformen slutpunkten ska vara mer kompatibel med OAuth 2.0-specifikationen och överensstämmer bättre med vanliga metoder i branschen. Dessutom kan appar för att göra [inkrementella medgivande](#incremental-and-dynamic-consent) – endast begär behörigheter när programmet kräver dem inte direkt.

## <a name="well-known-scopes"></a>Välkända scope

### <a name="offline-access"></a>Offline-åtkomst

Appar med hjälp av Microsoft identity-plattformen slutpunkt kan kräva användning av en ny välkända behörighet för appar - den `offline_access` omfång. Alla appar måste begära denna behörighet om de behöver för att komma åt resurser på uppdrag av en användare under en långvarig tid, även om användaren inte kanske aktivt använder appen. Den `offline_access` omfattning visas för användaren i dialogrutor för användarmedgivande som **komma åt dina data när som helst**, som användaren måste samtycka till. Begär den `offline_access` behörighet kan din webbapp tar emot OAuth 2.0-refresh_tokens från Microsoft identity-plattformen slutpunkten. Uppdatera token är långlivade och kan utbyta för nya OAuth 2.0-åtkomsttoken under långa perioder av åtkomst.

Om din app inte begär det `offline_access` omfattning, det inte tar emot uppdateringstoken. Det innebär att när du lösa in en auktoriseringskod i OAuth 2.0-auktoriseringskodflödet endast får du tillbaka en åtkomsttoken från den `/token` slutpunkt. Som komma åt token är giltig för en kort tidsperiod (vanligtvis en timme), men till slut upphör att gälla. AT tidpunkt, din app behöver du därefter skickas användarna tillbaka till den `/authorize` slutpunkt för att hämta en ny auktoriseringskod. Under den här omdirigering, användaren kanske eller kanske inte behöver ange dem igen eller reconsent behörigheter, beroende på vilken typ av app.

Mer information om OAuth 2.0 `refresh_tokens`, och `access_tokens`, Kolla in den [protokollreferens för Microsoft identity-plattformen](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID-profil och e-post

Historiskt sett har mest grundläggande OpenID Connect-inloggning flödet med Microsofts identitetsplattform skulle uppge en mängd information om användaren i den resulterande *id_token*. Anspråk i en id_token kan innehålla användarens namn, önskad användarnamn, e-postadress, objekt-ID och mer.

Informationen som den `openid` omfång får din app åtkomst till är nu begränsade. Den `openid` omfång kan din app för att logga in användaren och ta emot en appspecifika identifierare för användaren. Om du vill hämta personliga data om användaren i din app, måste din app och begär ytterligare behörighet från användaren. Två nya scope `email` och `profile`, gör att du kan begära ytterligare behörighet.

* Den `email` omfånget gör din appåtkomst till användarens primära e-postadress via den `email` anspråk i id_token, förutsatt att användaren har en adresserbara e-postadress.
* Den `profile` omfång får din appåtkomst till andra grundläggande information om användare, till exempel deras namn måste önskade användarnamn, objekt-ID och så vidare i id_token.

Dessa scope kan du koda din app på en minimal avslöjande sätt så att du kan bara be användaren ange mängden information som din app måste göra sitt jobb. Mer information om dessa scope finns [omfattningsreferens för Microsoft identity-plattformen](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Tokenanspråken

Microsoft identity-plattformen endpoint utfärdar en mindre uppsättning anspråk i token som standard att hålla nyttolaster små. Om du har appar och tjänster som är beroende av en viss anspråk i en v1.0-token som inte längre som standard i en token för Microsoft identity-plattformen kan du använda den [valfria anspråk](active-directory-optional-claims.md) funktionen för att inkludera det vidare.

## <a name="limitations"></a>Begränsningar

Det finns några begränsningar vara medvetna om när du använder Microsoft identity-plattformen.

När du skapar program som integreras med Microsoft identity-plattformen måste du bestämma om Microsoft identity-plattformen endpoint autentisering protokollen och uppfyller dina behov. V1.0 slutpunkt och plattformen stöds fortfarande fullt ut och i vissa avseenden är fler funktioner än Microsoft identity-plattformen. Men Microsoft identity-plattformen [introducerar stora fördelar](azure-ad-endpoint-comparison.md) för utvecklare.

Här är nu en förenklad rekommendation för utvecklare:

* Om du vill ha eller behöver stöd för personliga Microsoft-konton i ditt program, eller du skriver ett nytt program kan du använda Microsoft identity-plattformen. Men innan du gör, kontrollera att du förstår de begränsningar som beskrivs i den här artikeln.
* Om du migrerar eller uppdatera ett program som förlitar sig på SAML, kan du inte använda Microsoft identity-plattformen. I stället referera till den [för för Azure AD-v1.0](v1-overview.md).

Microsoft identity-plattformen slutpunkten kommer att utvecklas för att ta bort de begränsningar som anges här, så att du behöver bara använda Microsoft identity-plattformen slutpunkten. Under tiden kan använda den här artikeln för att avgöra om Microsoft identity-plattformen slutpunkten är rätt för dig. Vi fortsätter att uppdatera den här artikeln för att återspegla det aktuella tillståndet för Microsoft identity-plattformen slutpunkten. Kontrollera att gå tillbaka till dina krav mot Microsoft identity-plattformsfunktioner för att omvärdera.

### <a name="restrictions-on-app-registrations"></a>Begränsningar för app-registreringar

För varje app som du vill integrera med Microsoft identity-plattformen slutpunkt, kan du skapa en appregistrering i den nya [ **appregistreringar** uppleva](https://aka.ms/appregistrations) i Azure-portalen. Befintliga appar för Microsoft-konto är inte kompatibla med portalen, men alla Azure AD-appar finns, oavsett var och när de har registrerats.

App-registreringar som har stöd för arbete och skola konton och personliga konton har följande varningar:

* Bara två apphemligheter tillåts per program-ID.
* Ett program som inte har registrerats i en klient kan bara hanteras av det konto som registrerade källan. Det kan inte delas med andra utvecklare. Detta är fallet för de flesta appar som registrerades med ett personligt microsoftkonto i portalen för registrering av appen. Om du vill dela dina appregistrering med flera utvecklare registrera programmet i en klient med den nya **appregistreringar** på Azure portal.
* Det finns flera begränsningar på formatet för omdirigerings-URL som är tillåtet. Mer information om omdirigerings-URL finns i nästa avsnitt.

### <a name="restrictions-on-redirect-urls"></a>Begränsningar för omdirigera URL: er

Appar som har registrerats för Microsoft identity-plattformen är begränsade till en begränsad uppsättning omdirigerings-URL-värden. Omdirigerings-URL för webbappar och tjänster måste börja med schemat `https`, och alla omdirigerings-URL-värden måste dela en enda DNS-domän.  Registreringssystemet jämför hela DNS-namnet på DNS-namnet på omdirigerings-URL som du vill lägga till befintliga omdirigerings-URL. `http://localhost` kan också användas som en omdirigerings-URL.  

Begäran om att lägga till DNS-namnet kommer att misslyckas om något av följande villkor föreligger:  

* Hela DNS-namnet på den nya omdirigerings-URL: en matchar inte den befintliga omdirigerings-URL: en DNS-namn.
* Hela DNS-namnet på den nya omdirigerings-URL är inte en underdomän till befintliga omdirigerings-URL.

#### <a name="example-1"></a>Exempel 1

Om appen har en omdirigerings-URL för `https://login.contoso.com`, du kan lägga till en omdirigerings-URL där matchar DNS-namnet exakt som de visas i följande exempel:

`https://login.contoso.com/new`

Alternativt kan du referera till DNS-underdomänen login.contoso.com, som visas i följande exempel:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Exempel 2

Om du vill ha en app som har `login-east.contoso.com` och `login-west.contoso.com` som omdirigerings-URL: er, måste du lägga till de omdirigera URL: er i följande ordning:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Du kan lägga till två senare eftersom de är underdomäner i första omdirigerings-URL: en, contoso.com.

Du kan ha högst 20 svars-URL för ett visst program – den här gränsen gäller för alla typer av appar att registreringen har stöd för (ensidesapplikation (SPA), inbyggd klient, webbprogram och tjänsten).  

Läs hur du registrerar en app för användning med Microsoft identity-plattformen i [registrera en app med den nya upplevelsen för App-registreringar](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Begränsningar för bibliotek och SDK: er

Stöd för Microsoft identity-plattformen slutpunkten är för närvarande begränsad. Om du vill använda Microsoft identity-plattformen slutpunkten i ett produktionsprogram finns följande alternativ:

* Du kan på ett säkert sätt använda allmänt tillgängligt från serversidan mellanprogram till inloggning och tokenverifiering om du bygger ett webbprogram. Dessa inkluderar OWIN OpenID Connect-mellanprogram för ASP.NET och Node.js Passport-plugin-programmet. Kodexempel som använder Microsoft mellanprogram, finns det [Microsoft identity-plattformen har börjat](v2-overview.md#getting-started) avsnittet.
* Om du skapar ett skrivbord eller mobila program, kan du använda en av Microsoft Authentication Libraries (MSAL). Dessa bibliotek är allmänt tillgängliga eller i en förhandsversion av produktion som stöds, så det är säkert att använda dem i produktionsprogram. Du kan läsa mer om villkoren för förhandsversionen och tillgängliga bibliotek i [autentisering bibliotek referens](reference-v2-libraries.md).
* För plattformar som inte omfattas av Microsoft-bibliotek, kan du integrera med Microsoft identity-plattformen slutpunkten genom att skicka och ta emot protokollmeddelanden i din programkod direkt. OpenID Connect och OAuth-protokoll [dokumenteras uttryckligen](active-directory-v2-protocols.md) som hjälper dig sådan integration.
* Slutligen kan du använda OpenID Connect och OAuth bibliotek med öppen källkod för integrering med Microsoft identity-plattformen slutpunkten. Microsoft identity-plattformen slutpunkten ska vara kompatibel med många bibliotek för öppen källkod-protokollet utan ändringar. Dessa typer av bibliotek varierar efter språk och plattform. Den [OpenID Connect](https://openid.net/connect/) och [OAuth 2.0](https://oauth.net/2/) webbplatser underhålla en lista över populära implementeringar. Mer information finns i [Microsoft identity-plattformen och autentisering bibliotek](reference-v2-libraries.md), och en lista över klientbibliotek med öppen källkod och exempel som har testats med Microsoft identity-plattformen slutpunkt.
* För referens anger den `.well-known` slutpunkten för Microsoft identity-plattformen common slutpunkten är `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Ersätt `common` med klient-ID för att hämta data som är specifika för din klient.  

### <a name="protocol-changes"></a>Protokollet ändringar

Microsoft identity-plattformen slutpunkt stöder inte SAML eller WS-Federation; Det stöder bara OpenID Connect och OAuth 2.0.  Viktiga ändringar till OAuth 2.0-protokoll från v1.0-slutpunkten är: 

* Den `email` anspråk returneras om ett valfritt anspråk konfigureras **eller** omfånget = e-post har angetts i begäran. 
* Den `scope` parametern stöds nu i stället för den `resource` parametern.  
* Många svar har ändrats så att de blir mer kompatibel med OAuth 2.0-specifikationen, till exempel returnera korrekt `expires_in` som ett heltal i stället för en sträng.  

För att bättre förstå omfattning protocol-funktioner som stöds i Microsoft identity-plattformen endpoint, se [referens för OpenID Connect och OAuth 2.0-protokollet](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-begränsningar

Om du har använt Active Directory Authentication Library (ADAL) i Windows-program, kanske du har dragit nytta av Windows-integrerad autentisering, som använder Security Assertion Markup Language (SAML) assertion bevilja. Med det här beviljandet federerade användare av Azure AD klienter tyst kan autentiseras med deras lokala Active Directory-instans utan att ange autentiseringsuppgifter. SAML assertion bevilja stöds inte på slutpunkten för Microsoft identity-plattformen.
