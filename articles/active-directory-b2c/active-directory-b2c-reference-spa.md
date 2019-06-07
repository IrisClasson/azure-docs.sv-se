---
title: Enkelsidigt loggar in med implicit flöde – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till enkelsidiga logga in med det implicita flödet för OAuth 2.0 med Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a66fa70f6f5615257554e98e40e605d6a7e981fe
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508963"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Enkelsidigt loggar in med det implicita flödet för OAuth 2.0 i Azure Active Directory B2C

Många moderna program har en ensidesapp klientdel som främst är skriven i JavaScript. Ofta skrivs appen med hjälp av ett ramverk som AngularJS, Ember.js eller Durandal. Har några ytterligare utmaningar för autentisering enkelsidiga appar och andra JavaScript-appar som körs i första hand i en webbläsare:

- Security-egenskaperna för dessa appar skiljer sig från traditionella server-baserade webbprogram.
- Många servrar för auktorisering och identitetsprovidrar har inte stöd för cross-origin resource sharing (CORS) begäranden.
- Hela sidan webbläsaren omdirigeras från appen kan vara inkräktande användarupplevelsen.

Stöd för dessa program, Azure Active Directory B2C (Azure AD B2C) använder det implicita flödet för OAuth 2.0. Implicit beviljande flöde för OAuth 2.0-auktorisering beskrivs i [avsnittet 4.2 av OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). I implicit flöde appen tar emot token direkt från Azure Active Directory (Azure AD) tillåta slutpunkt utan någon exchange server-till-server. Alla autentiseringslogiken och sessionshantering utförs helt i JavaScript-klient, utan ytterligare sidomdirigeringar.

Azure AD B2C utökar OAuth 2.0 standard implicit flöde som är högre än enkel autentisering och auktorisering. Azure AD B2C introducerar den [principparametern](active-directory-b2c-reference-policies.md). Med principparametern kan du använda OAuth 2.0 att lägga till principer till din app, till exempel registrering, inloggning, och profilera management användarflöden. I exemplet HTTP-begäranden i den här artikeln **fabrikamb2c.onmicrosoft.com** används som ett exempel. Du kan ersätta `fabrikamb2c` med namnet på din klient om du har en och har skapat ett användarflöde.

Det implicita flödet inloggning ser ut ungefär som följande bild. Varje steg beskrivs i detalj i artikeln.

![OpenID Connect spaltformat](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Skicka begäranden om autentisering

När ditt webbprogram måste autentisera användaren och kör ett användarflöde, det kan dirigera användare till den `/authorize` slutpunkt. Användaren vidtar åtgärder beroende på användarflödet.

I den här begäran klienten anger de behörigheter som krävs för att hämta för användaren i den `scope` parametern och användarflödet ska köras den `p` parametern. Tre exempel finns i avsnitten nedan (med radbrytningar för läsbarhet), med en annan användare-flöde. Få en bild för hur varje begäran fungerar, försök att klistra in begäran i en webbläsare och kör den. Du kan ersätta `fabrikamb2c` med namnet på din klient om du har en och har skapat ett användarflöde.

### <a name="use-a-sign-in-user-flow"></a>Använd en inloggning användarflödet

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Använd en registrerings användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Använda en Redigera profil användarflödet
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parameter | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Programmet med ID som den [Azure-portalen](https://portal.azure.com/) tilldelats till ditt program. |
| response_type | Ja | Måste innehålla `id_token` för OpenID Connect-inloggning. Den kan även innehålla svarstypen `token`. Om du använder `token`, din app kan omedelbart ta emot en åtkomst-token från slutpunkten för auktorisering, utan att göra en andra begäran till slutpunkten för auktorisering.  Om du använder den `token` svarstypen den `scope` parametern måste innehålla en omfattning som anger vilken resurs för att utfärda token för. |
| redirect_uri | Nej | Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodade. |
| response_mode | Nej | Anger metoden du använder för att skicka den resulterande token tillbaka till din app.  Implicit flöden, använda `fragment`. |
| scope | Ja | En blankstegsavgränsad lista med omfattningar. Ett enda scope-värde som anger till Azure AD båda av de behörigheter som tas emot. Den `openid` omfång anger en behörighet att logga in användaren och hämta data om användaren i form av ID-token. Den `offline_access` omfånget är valfritt för web apps. Det innebär att din app måste en uppdateringstoken för långlivade åtkomst till resurser. |
| tillstånd | Nej | Ett värde i begäran som också returneras i token-svaret. Det kan vara en sträng med innehåll som du vill använda. Vanligtvis en slumpmässigt genererad, unika värdet används, att förhindra attacker med förfalskning av begäran. Tillstånd används också för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade som de var på sidan. |
| nonce | Ja | Ett värde i begäran (genereras av appen) som ingår i den resulterande ID-token som ett anspråk. Appen kan sedan att verifiera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig, unik sträng som kan användas för att fastställa ursprunget för begäran. |
| p | Ja | Principen för att köra. Det är namnet på en princip (användarflödet) som skapas i din Azure AD B2C-klient. Namnet principvärdet måste inledas med **b2c\_1\_** . |
| fråga | Nej | Typ av interaktion från användaren som krävs. Det enda giltiga värdet är för närvarande `login`. Den här parametern Tvingar användaren att ange sina autentiseringsuppgifter i begäran. Enkel inloggning ska börja gälla. |

Nu kan uppmanas användaren att slutföra principens arbetsflöde. Användaren kan behöva ange sina användarnamn och lösenord, logga in med en sociala identitet, logga för katalogen, eller en annan siffra steg. Användaråtgärder beror på hur användarflödet har definierats.

När du är klar användarflödet Azure AD returnerar ett svar till din app på värdet som du använde för `redirect_uri`. Den använder den metod som beskrivs i den `response_mode` parametern. Svaret är exakt detsamma för alla användare åtgärd scenarion, oberoende av det användarflöde som kördes.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar som använder `response_mode=fragment` och `response_type=id_token+token` ser ut som följande, med radbrytningar för anpassning:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parameter | Beskrivning |
| --------- | ----------- |
| access_token | Den åtkomst-token som appen har begärt. |
| token_type | Typ tokenu-värde. Den enda typen som har stöd för Azure AD är ägar. |
| expires_in | Hur lång tid som den åtkomst-token är giltig (i sekunder). |
| scope | Scope som token är giltig för. Du kan också använda omfång cache-tokens för senare användning. |
| id_token | ID-token som appen har begärt. Du kan använda ID-token för att verifiera användarens identitet och starta en session med användaren. Mer information om ID-token och deras innehåll finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| tillstånd | Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigeringen-URI så att appen kan hantera dem på rätt sätt:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivning |
| --------- | ----------- |
| error | En kod som används för att klassificera typer av fel som uppstår. |
| error_description | Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |
| tillstånd | Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska.|

## <a name="validate-the-id-token"></a>Verifiera ID-token

Ta emot ett ID-token är inte tillräckligt för att autentisera användaren. Verifiera signaturen för ID-token och kontrollera anspråken i token enligt krav för din app. Azure AD B2C använder [JSON Web token (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) och kryptering med offentlig nyckel att signera token och kontrollera att de är giltiga.

Många bibliotek med öppen källkod är tillgängliga för att verifiera JWTs, beroende på vilket språk du föredrar att använda. Överväg att utforska tillgängliga bibliotek med öppen källkod i stället för att implementera dina egna validering av logik. Du kan använda informationen i den här artikeln för att lära dig hur du använder dessa bibliotek korrekt.

Azure AD B2C har en slutpunkt för OpenID Connect metadata. En app kan använda slutpunkten för att hämta information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Det finns ett JSON-dokument för metadata för varje användarflöde i din Azure AD B2C-klient. Till exempel finns metadatadokument för b2c_1_sign_in användarflödet i fabrikamb2c.onmicrosoft.com klienten på:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

En av egenskaperna för den här konfigurationsdokumentet är den `jwks_uri`. Värdet för samma användarflödet skulle bli:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Att fastställa vilka användarflödet har använts för att logga en ID-token (och var du vill hämta metadata från), har du två alternativ. Först userjourney-namnet som ingår i den `acr` anspråk i `id_token`. Information om hur du Parsar anspråk från en ID-token som finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md). Ett annat alternativ är att koda användarflödet i värdet för den `state` parameter när du skickar ut begäran. Avkoda sedan den `state` parametern för att avgöra vilka användarflödet har använts. Någon av metoderna är giltig.

När du har köpt metadatadokument från metadataslutpunkt OpenID Connect kan använda du de offentliga nycklarna för RSA-256 (som finns i den här slutpunkten) för att verifiera signaturen för ID-token. Det kan finnas flera tangenterna som anges i den här slutpunkten vid en given tidpunkt, var och en har identifierats av en `kid`. Rubriken för `id_token` innehåller också en `kid` anspråk. Anger vilka av dessa nycklar används för att logga ID-token. Mer information, inklusive lära dig mer om [verifiera token](active-directory-b2c-reference-tokens.md), finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

När du har validerat signaturen för ID-token, Kräv verifiering av flera anspråk. Exempel:

* Verifiera den `nonce` anspråk som ska förhindra token repetitionsattacker. Värdet ska vara du angav i inloggning-begäran.
* Verifiera den `aud` anspråk som ska se till att ID-token har utfärdats för din app. Dess värde bör vara program-ID för din app.
* Verifiera den `iat` och `exp` utger sig för att se till att ID-token inte har gått ut.

Flera mer verifieringar som du bör utföra beskrivs i detalj i de [OpenID Connect Core Spec](https://openid.net/specs/openid-connect-core-1_0.html). Du kanske också vill validera ytterligare anspråk, beroende på ditt scenario. Vissa vanliga verifieringar är:

* Se till att användaren eller organisationen har registrerat dig för appen.
* Se till att användaren har rätt behörighet och behörigheter.
* Se till att en viss styrkan hos autentisering har inträffat, till exempel genom att använda Azure Multi-Factor Authentication.

Mer information om anspråk i en ID-token finns i den [tokenreferens för Azure AD B2C](active-directory-b2c-reference-tokens.md).

När du har godkänt ID-token, kan du börja en session med användaren. I din app att använda anspråk i ID-token för att hämta information om användaren. Den här informationen kan användas för visning, poster, auktorisering och så vidare.

## <a name="get-access-tokens"></a>Få åtkomst-token
Om det enda som web apps behöver göra är att köra användarflöden, kan du hoppa över nästa avsnitt. Informationen i följande avsnitt gäller endast för webbappar som måste göra autentiserade anrop till ett webb-API och som skyddas av Azure AD B2C.

Nu när du har registrerat användaren i ditt program kan kan du hämta åtkomsttoken för anropande webb-API: er som skyddas av Azure AD. Även om du redan fått en token med hjälp av den `token` svarstypen, du kan använda den här metoden för att hämta token för ytterligare resurser utan att omdirigera användaren att logga in igen.

I en typisk web app-flöde, skulle du gör en begäran om att den `/token` slutpunkt. Slutpunkten stöder dock inte CORS-förfrågningar, så gör AJAX-anrop för att hämta och uppdatera token inte är ett alternativ. Du kan i stället använda det implicita flödet i en dold HTML iframe-element för att hämta nya token för andra webb-API: er. Här är ett exempel med radbrytning för anpassning:

```

https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| client_id |Obligatoriskt |Program-ID som tilldelats din app i den [Azure-portalen](https://portal.azure.com). |
| response_type |Obligatoriskt |Måste innehålla `id_token` för OpenID Connect-inloggning.  Det kan även innefatta svarstypen `token`. Om du använder `token` här är din app kan omedelbart ta emot en åtkomst-token från slutpunkten för auktorisering, utan att göra en andra begäran till slutpunkten för auktorisering. Om du använder den `token` svarstypen den `scope` parametern måste innehålla en omfattning som anger vilken resurs för att utfärda token för. |
| redirect_uri |Rekommenderas |Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodade. |
| scope |Obligatoriskt |En blankstegsavgränsad lista med omfattningar.  Inkludera alla omfång som du behöver för den avsedda resursen för att hämta token. |
| response_mode |Rekommenderas |Anger den metod som används för att skicka den resulterande token tillbaka till din app.  Kan vara `query`, `form_post`, eller `fragment`. |
| tillstånd |Rekommenderas |Ett värde i begäran som returneras i token-svaret.  Det kan vara en sträng med innehåll som du vill använda.  Vanligtvis en slumpmässigt genererad, unika värdet används, att förhindra attacker med förfalskning av begäran.  Tillståndet också används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade. Till exempel sidan eller vyn kunde användaren på. |
| nonce |Obligatoriskt |Ett värde som ingår i den begäran som skapats av appen, som ingår i den resulterande ID-token som ett anspråk.  Appen kan sedan att verifiera det här värdet om du vill lösa token repetitionsattacker. Värdet är vanligtvis en slumpmässig, unik sträng som identifierar ursprunget för begäran. |
| fråga |Obligatoriskt |Om du vill uppdatera och hämta token i en dold iframe måste använda `prompt=none` så att iframe inte fastnar på sidan logga in och returnerar omedelbart. |
| login_hint |Obligatoriskt |Om du vill uppdatera och hämta token i en dold iframe måste innehålla användarnamnet för användaren i den här tipset att skilja mellan olika sessioner som användaren kan ha vid en given tidpunkt. Du kan extrahera användarnamnet från en tidigare logga in med hjälp av den `preferred_username` anspråk. |
| domain_hint |Obligatoriskt |Det kan vara `consumers` eller `organizations`.  För att uppdatera och hämta token i en dold iframe, innehåller den `domain_hint` värde i begäran.  Extrahera den `tid` anspråk från ID-token för en tidigare logga in för att avgöra vilket värde som ska användas.  Om den `tid` anspråk värdet är `9188040d-6c67-4c5b-b112-36a304b66dad`, använda `domain_hint=consumers`.  Annars kan du använda `domain_hint=organizations`. |

Genom att ange den `prompt=none` parametern denna begäran antingen lyckas eller misslyckas omedelbart och återgår till ditt program.  Ett lyckat svar skickas till din app på den angivna omdirigeringen-URI, med hjälp av den metod som beskrivs i den `response_mode` parametern.

### <a name="successful-response"></a>Lyckat svar
Ett lyckat svar med hjälp av `response_mode=fragment` ser ut som i det här exemplet:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parameter | Beskrivning |
| --- | --- |
| access_token |Den token som appen har begärt. |
| token_type |Vydat typ kommer alltid att ägar. |
| tillstånd |Om en `state` parametern ingår i begäran, samma värde som ska visas i svaret. Appen bör kontrollera att den `state` värden i begäran och svar är identiska. |
| expires_in |Hur länge den åtkomst-token är giltig (i sekunder). |
| scope |Scope som den åtkomst-token är giltig för. |

### <a name="error-response"></a>Felsvar
Felsvar kan också skickas till omdirigeringen-URI så att appen kan hantera dem på rätt sätt.  För `prompt=none`, ett oväntat fel ser ut som i följande exempel:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivning |
| --- | --- |
| error |En felkodsträngen som kan användas för att klassificera typer av fel som uppstår. Du kan också använda strängen för att reagera på fel. |
| error_description |Ett felmeddelande som kan hjälpa dig att identifiera de grundläggande orsakerna till ett autentiseringsfel. |

Om du får detta felmeddelande i iframe-begäran måste användaren interaktivt logga in igen att hämta en ny token.

## <a name="refresh-tokens"></a>Uppdatera token
ID-token och åtkomst-token upphör att gälla efter en kort tidsperiod. Appen måste förberedas för att uppdatera dessa token med jämna mellanrum.  Uppdatera valfri typ av token genom att utföra samma dolda iframe-begäran som vi använde i ett tidigare exempel med hjälp av den `prompt=none` parametern för att styra stegen i Azure AD.  Att ta emot en ny `id_token` värde, se till att använda `response_type=id_token` och `scope=openid`, och en `nonce` parametern.

## <a name="send-a-sign-out-request"></a>Skicka en förfrågan om utloggning
När du vill logga ut användaren från appen omdirigera användaren till Azure AD för att logga ut. Om du inte att omdirigera, kan de eventuellt att autentiseras på nytt till din app utan att behöva ange sina autentiseringsuppgifter igen eftersom de har en giltig inloggnings-session med Azure AD.

Du kan helt enkelt omdirigera användaren till den `end_session_endpoint` som visas i den samma OpenID att ansluta metadatadokument beskrivs i [verifiera ID-token](#validate-the-id-token). Exempel:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Krävs? | Beskrivning |
| --- | --- | --- |
| p |Obligatoriskt |Principen du använder för att logga ut användaren från ditt program. |
| post_logout_redirect_uri |Rekommenderas |Den URL som användaren ska omdirigeras till efter lyckad utloggning. Om det inte finns, visar Azure AD B2C ett allmänt meddelande för användaren. |

> [!NOTE]
> Dirigera användare till den `end_session_endpoint` tar bort några av användarens inloggning tillstånd med Azure AD B2C. Men Logga det inte ut användaren från sociala providern användarsessionen. Om användaren väljer samma identifiera providern under en efterföljande inloggning, användaren måste autentiseras, utan att behöva ange sina autentiseringsuppgifter. Om en användare vill logga ut från ditt Azure AD B2C-program, betyder det inte att helt logga ut från deras Facebook-konto, till exempel. Men för lokala konton avslutas användarens session korrekt.
> 

