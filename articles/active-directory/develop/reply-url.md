---
title: Begränsningar för omdirigerings-URI (svars-URL) | Azure
titleSuffix: Microsoft identity platform
description: En beskrivning av begränsningarna och begränsningarna i formatet för omdirigerings-URI (svars-URL) som tillämpas av Microsoft Identity Platform.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8be13a299de0fc3de0acaf0001722d8c96a460e6
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205930"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Begränsningar och begränsningar för omdirigerings-URI (svars-URL)

En omdirigerings-URI eller svars-URL är den plats där auktoriseringsservern skickar användaren när appen har godkänts och beviljats en auktoriseringskod eller åtkomsttoken. Auktoriseringsservern skickar koden eller token till omdirigerings-URI: n, så det är viktigt att du registrerar rätt plats som en del av registrerings processen för appar.

 Följande begränsningar gäller för omdirigerings-URI: er:

* Omdirigerings-URI: n måste börja med schemat `https` .

* Omdirigerings-URI: n är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av sökvägen `.../abc/response-oidc` ska du inte ange `.../ABC/response-oidc` i omdirigerings-URI: n. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, kan cookies som är kopplade till `.../abc/response-oidc` uteslutas om de omdirigeras till den Skift läges fel matchnings bara `.../ABC/response-oidc` URL: en.

## <a name="maximum-number-of-redirect-uris"></a>Maximalt antal omdirigerings-URI: er

Den här tabellen visar det maximala antalet omdirigerings-URI: er som du kan lägga till i en app-registrering i Microsoft Identity Platform.

| Konton som är inloggade | Maximalt antal omdirigerings-URI: er | Beskrivning |
|--------------------------|---------------------------------|-------------|
| Microsoft arbets-eller skol konton i en organisations Azure Active Directory-klient (Azure AD) | 256 | `signInAudience` fältet i applikations manifestet har angetts till antingen *AzureADMyOrg* eller *AzureADMultipleOrgs* |
| Personliga Microsoft-konton och arbets-och skol konton | 100 | `signInAudience` fältet i applikations manifestet har angetts till *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximal URI-längd

Du kan använda högst 256 tecken för varje omdirigerings-URI som du lägger till i en app-registrering.

## <a name="supported-schemes"></a>Scheman som stöds

Program modellen för Azure Active Directory (Azure AD) stöder för närvarande både HTTP-och HTTPS-scheman för appar som loggar in på arbets-eller skol konton i en organisations Azure AD-klient. Dessa konto typer anges av `AzureADMyOrg` `AzureADMultipleOrgs` värdena och i-fältet i `signInAudience` applikations manifestet. För appar som loggar in på personliga Microsoft-konton (MSA) *och* arbets-och skol konton (det `signInAudience` vill säga är inställt på `AzureADandPersonalMicrosoftAccount` ) tillåts bara https-schemat.

Om du vill lägga till omdirigerings-URI: er med ett HTTP-schema till app-registreringar som loggar in på arbets-eller skol konton, måste du använda program manifest redigeraren i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) i Azure Portal. Men även om det är möjligt att ställa in en HTTP-baserad omdirigerings-URI med hjälp av manifest redigeraren rekommenderar vi *starkt* att du använder https-schemat för omdirigerings-URI: er.

## <a name="localhost-exceptions"></a>Localhost-undantag

Enligt [RFC 8252 avsnitt 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) och [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), omdirigerings-URI: er för "loopback" eller "localhost" har två särskilda överväganden:

1. `http` URI-scheman är acceptabla eftersom omdirigeringen aldrig lämnar enheten. Detta gör att båda dessa är acceptabla:
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. På grund av tillfälliga port intervall som ofta krävs av interna program, ignoreras port komponenten (till exempel `:5001` eller `:443` ) i syfte att matcha en omdirigerings-URI. Detta innebär att alla dessa betraktas som likvärdiga:
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

I en utvecklings synpunkt innebär detta några saker:

* Registrera inte flera omdirigerings-URI: er där bara porten är annorlunda. Inloggnings servern väljer en godtyckligt och använder beteendet som är kopplat till den omdirigerings-URI: n (till exempel om den är `web` -, `native` -eller `spa` -typ-omdirigering).
* Om du behöver registrera flera omdirigerings-URI: er på localhost för att testa olika flöden under utvecklingen kan du skilja dem åt med hjälp av *Sök vägs* komponenten i URI: n. Matchar till exempel `http://127.0.0.1/MyWebApp` inte `http://127.0.0.1/MyNativeApp` .
* Enligt RFC-vägledningen bör du inte använda `localhost` den omdirigerings-URI: n. Använd i stället den faktiska loopback-IP-adressen `127.0.0.1` . Detta förhindrar att appen bryts av felkonfigurerade brand väggar eller bytt namn på nätverks gränssnitt.

    IPv6 loopback-adressen ( `[::1]` ) stöds inte för närvarande.

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Begränsningar för jokertecken i omdirigerings-URI: er

Jokertecken i jokertecken `https://*.contoso.com` verkar vara praktiska, men bör undvikas på grund av säkerhets aspekter. Enligt OAuth 2,0-specifikationen ([Section 3.1.2 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) måste en URI för omdirigerings slut punkt vara en absolut URI.

URI: er för jokertecken stöds för närvarande inte i appar som kon figurer ATS för att logga in på personliga Microsoft-konton och arbets-eller skol konton. URI: er med jokertecken tillåts dock för appar som har kon figurer ATS för att logga in på arbets-eller skol konton i en organisations Azure AD-klient.

Om du vill lägga till omdirigerings-URI: er med jokertecken till app-registreringar som loggar in på arbets-eller skol konton, måste du använda program manifest redigeraren i [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) i Azure Portal. Även om det är möjligt att ange en omdirigerings-URI med ett jokertecken med hjälp av manifest redigeraren rekommenderar vi *starkt* att du följer [Section 3.1.2 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) och använder endast absoluta URI: er.

Om ditt scenario kräver fler omdirigerings-URI: er än den högsta tillåtna gränsen, bör du tänka på [följande tillvägagångs sätt](#use-a-state-parameter) i stället för att lägga till en omdirigering

### <a name="use-a-state-parameter"></a>Använda en tillstånds parameter

Om du har flera under domäner och ditt scenario kräver att du vid lyckad autentisering omdirigerar användare till samma sida som de startade från, kan det vara bra att använda en tillstånds parameter.

I den här metoden:

1. Skapa en "delad" omdirigerings-URI per program för att bearbeta säkerhetstoken som du tar emot från behörighets slut punkten.
1. Ditt program kan skicka programspecifika parametrar (t. ex. en underdomäns-URL där användaren har sitt ursprung eller något som kallas anpassad information) i status parametern. När du använder en tillstånds parameter skyddar du mot CSRF-skydd enligt vad som anges i [avsnitt 10,12 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)).
1. De programspecifika parametrarna innehåller all information som behövs för att programmet ska återge rätt upplevelse för användaren, det vill säga att skapa rätt program tillstånd. HTML AD Authorization Endpoint stripe-HTML från läges parametern ser till att du inte skickar HTML-innehåll i den här parametern.
1. När Azure AD skickar ett svar till omdirigerings-URI: n skickas status parametern tillbaka till programmet.
1. Programmet kan sedan använda värdet i parametern State för att avgöra vilken URL som användaren kan skicka till. Kontrol lera att du validerar för CSRF-skydd.

> [!WARNING]
> Med den här metoden kan en komprometterad klient ändra de ytterligare parametrar som skickas i parametern State, vilket innebär att användaren omdirigeras till en annan URL, vilket är det [Öppna omdirigeraren](https://tools.ietf.org/html/rfc6819#section-4.2.4) som beskrivs i RFC 6819. Därför måste klienten skydda dessa parametrar genom att kryptera statusen eller verifiera den på annat sätt, t. ex. validera domän namnet i omdirigerings-URI mot token.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om app Registration [applikations manifest](reference-app-manifest.md).
