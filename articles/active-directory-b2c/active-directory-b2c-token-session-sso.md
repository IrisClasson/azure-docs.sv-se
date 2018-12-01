---
title: Token, session och enkel inloggning-konfiguration i Azure Active Directory B2C | Microsoft Docs
description: Token, session och enkel inloggning-konfiguration i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 456e32e2f5194417f004f80feef1852dd3d0befd
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723286"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Token, session och enkel inloggning-konfiguration i Azure Active Directory B2C

Den här funktionen ger dig detaljerad kontroll på en [flow användarnivå](active-directory-b2c-reference-policies.md), av:

- Livslängd för säkerhetstoken som orsakats av Azure Active Directory (Azure AD) B2C.
- Livslängd för web application sessioner som hanteras av Azure AD B2C.
- Format för viktiga anspråk i säkerhetstoken som orsakats av Azure AD B2C.
- Enkel inloggning (SSO) beteende för flera appar och användarflöden i din Azure AD B2C-klient.

Du kan använda den här funktionen på alla principtypen, men det här exemplet visar hur du använder funktionen med en registrering eller inloggning användarflödet. För användarflöden, kan du använda den här funktionen i din Azure AD B2C-katalog på följande sätt:

1. Klicka på **användarflöden**.
2. Öppna ett användarflöde genom att klicka på den. Klicka exempelvis på **B2C_1_SiUpIn**.
3. Klicka på **Egenskaper**.
4. Under **Token kompatibilitetsinställningar**, gör dina önskade ändringar. Läs mer om tillgängliga egenskaper i följande avsnitt.
5. Klicka på **spara** överst på menyn.

## <a name="token-lifetimes-configuration"></a>Konfiguration av tokenlivslängder

Azure AD-B2C stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-b2c-reference-protocols.md) för att aktivera säker åtkomst till skyddade resurser. För att implementera det här stödet, genererar Azure AD B2C olika [säkerhetstoken](active-directory-b2c-reference-tokens.md). 

Följande egenskaper används för att hantera livslängd av säkerhetstoken som orsakats av Azure AD B2C:

- **Åtkomst och ID-token livstid (minuter)** – livslängden för OAuth 2.0-ägartoken som används för att få åtkomst till en skyddad resurs.
    - Standard = 60 minuter.
    - Minsta (inklusivt) = 5 minuter.
    - Maximalt (inklusivt) = 1 440 minuter.
- **Livslängd för uppdateringstoken (dagar)** – den maximala tidsperiod innan vilken en uppdateringstoken kan användas för att hämta en ny åtkomst- eller ID-token (och eventuellt en ny uppdateringstoken, om ditt program har beviljats den `offline_access` omfattning).
    - Standard = 14 dagar.
    - Minsta (inklusivt) = 1 dag.
    - Maximalt (inklusivt) = 90 dagar.
- **Token livslängd för skjutfönster (dagar)** – när den här perioden tid är slut om användaren tvingas återautentisera, oavsett giltighetsperioden hos den senaste uppdateringstoken som införskaffats av programmet. Det kan endast anges om växeln har angetts till **begränsad**. Det måste vara större än eller lika med den **livslängd för uppdateringstoken (dagar)** värde. Om växeln har angetts till **Ramavgränsare**, du kan inte ange ett specifikt värde.
    - Standard = 90 dagar.
    - Minsta (inklusivt) = 1 dag.
    - Maximalt (inklusivt) = 365 dagar.

Följande användningsfall aktiveras med hjälp av dessa egenskaper:

- Tillåt en användare att förbli inloggad till en hanteringsprincip för mobilprogram på obestämd tid, så länge användaren är aktiv hela tiden på programmet. Du kan ange **glidande fönstret livslängd för uppdateringstoken (dagar)** till **Ramavgränsare** i ditt flöde på användarens.
- Uppfyll din bransch säkerhets- och efterlevnadskrav genom att ange lämplig åtkomst tokenlivslängder.

De här inställningarna är inte tillgängliga för att återställa lösenord användarflöden. 

## <a name="token-compatibility-settings"></a>Tokenkompatibilitetsinställningar

Följande egenskaper gör att kunderna kan välja efter behov:

- **Utfärdaren (iss) anspråk** -den här egensapen identifierar den Azure AD B2C-klient som utfärdade token.
    - `https://<domain>/{B2C tenant GUID}/v2.0/` -Detta är standardvärdet.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` – Det här värdet innehåller ID: N för både B2C-klient och användarflödet som används i token-begäran. Om din app eller -biblioteket måste Azure AD B2C ska vara kompatibel med den [OpenID Connect Discovery 1.0-specifikationen](http://openid.net/specs/openid-connect-discovery-1_0.html), Använd det här värdet.
- **Anspråk för ämne (sub)** – den här egenskapen identifierar den entitet som token kontrollerar information.
    - **ObjectID** – den här egenskapen är standardvärdet. Fylls i objekt-ID för användaren i katalogen i den `sub` anspråk i token.
    - **Stöds inte** – den här egenskapen finns bara för bakåtkompatibilitet och vi rekommenderar att du växlar till **ObjectID** så fort du.
- **Anspråk som representerar princip-ID** -den här egensapen identifierar Anspråkstypen som princip-ID som används i token begäran har fyllts i.
    - **tfp** -den här egenskapen är standardvärdet.
    - **ACR** -den här egenskapen finns bara för bakåtkompatibilitet.

## <a name="session-behavior"></a>Sessionsbeteende

Azure AD-B2C stöder den [autentiseringsprotokollet OpenID Connect](active-directory-b2c-reference-oidc.md) för att aktivera säker inloggning till webbprogram. Du kan använda följande egenskaper för att hantera web application sessioner:

- **Webbappssession (minuter)** – livslängden för Azure AD B2C-sessioncookie som lagras i användarens webbläsare efter lyckad autentisering.
    - Standard = 1 440 minuter.
    - Minsta (inklusivt) = 15 minuter.
    - Maximalt (inklusivt) = 1 440 minuter.
- **Webbappssession** – om den här växeln anges till **absolut**, om användaren tvingas att autentisera igen efter hur lång tid som anges av **webbappssession (minuter)** nätverksförbindelse. Om den här växeln anges till **rullande** (standardinställningen), användaren förblir inloggad så länge användaren är aktiv kontinuerligt i webbprogrammet.

Följande användningsfall aktiveras med hjälp av dessa egenskaper:

- Uppfyller kraven för din bransch säkerhet och efterlevnad genom att ange rätt program webbsessionen livslängd.
- Tvinga autentisering efter en viss tidsperiod under användaren interagerar med en hög säkerhet som en del av ditt webbprogram. 

De här inställningarna är inte tillgängliga för att återställa lösenord användarflöden.

## <a name="single-sign-on-sso-configuration"></a>Enkel inloggning (SSO) konfiguration

Om du har flera program och användarflöden i din B2C-klient kan du kan hantera användarinteraktioner över dem med hjälp av den **konfigurationen för enkel inloggning** egenskapen. Du kan ange egenskapen till någon av följande inställningar:

- **Klient** – det här är standardinställningen. Med den här inställningen kan flera program och användare flöden i din B2C-klient för att dela samma användarsessionen. Till exempel när en användare loggar in i ett program, användaren kan även sömlöst logga in på en annan en, Contoso-farmaci, vid åtkomst till den.
- **Programmet** – den här inställningen kan du behålla en användarsession exklusivt för ett program, oberoende av andra program. Till exempel om du vill att användaren att logga in på Contoso farmaci (med samma autentiseringsuppgifter), även om användaren är redan inloggad på Contoso Shopping, ett annat program på samma B2C-klient. 
- **Principen** – den här inställningen kan du behålla en användarsession exklusivt för ett användarflöde, oberoende av de program som använder. Till exempel om användaren har redan loggat in och slutföra ett steg med multi factor authentication (MFA), kan användaren få tillgång till högre säkerhet delar av flera program så länge som den session som är kopplad till användarflödet inte upphör att gälla.
- **Inaktiverad** – den här inställningen Tvingar användaren att köra genom hela användarflödet på varje körning av principen. Till exempel på så sätt kan flera användare att logga in på ditt program (i ett delat skrivbord scenario), även när en enskild användare förblir inloggade under hela tiden.

De här inställningarna är inte tillgängliga för att återställa lösenord användarflöden. 

