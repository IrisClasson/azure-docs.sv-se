---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187854"
---
### <a name="how-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Gör så här för att: autentisera med en provider (Server Flow)
För att använda Mobile Apps för att hantera autentiseringsprocessen i din app måste du registrera din app med din identitetsprovider. Sedan måste du konfigurera program-ID och -hemligheten som du fått av din provider i Azure App Service.
Mer information finns i självstudierna [Lägg till autentisering till din app](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

När du har registrerat din identitetsprovider anropar du `.login()`-metoden med namnet på din provider. Om du till exempel vill logga in med Facebook använder du följande kod:

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Giltiga värden för providern är 'aad', 'facebook', 'google', 'microsoftaccount' och 'twitter'.

> [!NOTE]
> Google-autentisering fungerar för närvarande inte via Server Flow.  Om du vill autentisera med Google måste du använda en [klientflödesmetod](#client-auth).

I det här fallet hanterar Azure App Service OAuth 2.0-autentiseringsflödet.  Den visar inloggningssidan för den valda leverantören och genererar en App Service-autentiseringstoken efter lyckad inloggning med identitetsprovidern. När inloggningsfunktionen är färdig returnerar den ett JSON-objekt som både visar användar-ID och App Service-autentiseringstoken i fälten userId respektive authenticationToken. Den här token kan cachelagras och återanvändas tills den upphör att gälla.

### <a name="how-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Gör så här för att: autentisera med en provider (Client Flow)

Din app kan även oberoende kontakta identitetsprovidern och sedan tillhandahålla den returnerade token till App Service för autentisering. Det här klientflödet gör det möjligt för dig att tillhandahålla enkel inloggning för användare eller hämta ytterligare användardata från identitetsprovidern.

#### <a name="social-authentication-basic-example"></a>Grundläggande exempel på social autentisering

I det här exemplet används Facebook-klientens SDK för autentisering:

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Det här exemplet förutsätter att den token som tillhandahålls av respektive provider-SDK lagras i token-variabeln.

### <a name="how-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Gör så här för att: Få mer information om den autentiserade användaren

Autentiseringsinformationen kan hämtas från `/.auth/me`-slutpunkten med hjälp av HTTP-anrop med ett AJAX-bibliotek.  Se till att du ställer in `X-ZUMO-AUTH`-rubriken till din autentiseringstoken.  Autentiseringstoken lagras i `client.currentUser.mobileServiceAuthenticationToken`.  För att till exempel använda Fetch API:

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Fetch är tillgängligt som [ett npm-paket](https://www.npmjs.com/package/whatwg-fetch) eller som nedladdning via en webbläsare från [CDNJS](https://cdnjs.com/libraries/fetch). Du kan även använda jQuery eller en annan AJAX API för att hämta informationen.  Data tas emot som ett JSON-objekt.
