---
title: Översikt över Microsoft Identity Platform – Azure
description: Lär dig mer om komponenterna i Microsoft Identity Platform och hur de kan hjälpa dig att bygga stöd för identitets-och åtkomst hantering (IAM) i dina program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40
ms.openlocfilehash: e34eeeca3b17087fb5bd43ae353e43b5ab355831
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119563"
---
# <a name="microsoft-identity-platform-overview"></a>Översikt över Microsoft Identity Platform

Med Microsoft Identity Platform kan du skapa program som dina användare och kunder kan logga in på med sina Microsoft-identiteter eller sociala konton, och ge åtkomst till dina egna API: er eller Microsoft-API: er som Microsoft Graph.

Det finns flera komponenter som utgör Microsofts identitets plattform:

- **OAuth 2,0 och OpenID Connect standard-kompatibel autentiseringstjänst** som gör det möjligt för utvecklare att autentisera flera identitets typer, inklusive:
  - Arbets-eller skol konton, etablerade via Azure AD
  - Personliga Microsoft-konto, t. ex. Skype, Xbox och Outlook.com
  - Sociala eller lokala konton, genom att använda Azure AD B2C
- **Bibliotek med öppen källkod**: Microsoft Authentication libraries (MSAL) och stöd för andra standard bibliotek
- **Program hanterings Portal**: en registrerings-och konfigurations upplevelse i Azure Portal, tillsammans med de andra hanterings funktionerna för Azure.
- **API för program konfiguration och PowerShell**: program mässigt konfiguration av dina program via Microsoft Graph API och PowerShell så att du kan automatisera dina DevOps-uppgifter.
- **Utvecklarens innehåll**: teknisk dokumentation inklusive snabb starter, självstudier, instruktions guider och kod exempel.

För utvecklare erbjuder Microsoft Identity Platform integrering av moderna innovationer i identitets-och säkerhets utrymmet som lösenordsbaserad autentisering, stegvis autentisering och villkorlig åtkomst. Du behöver inte implementera sådana funktioner själv: program som är integrerade med Microsofts identitets plattform har internt möjlighet att utnyttja sådana innovationer.

Med Microsoft Identity Platform kan du skriva kod en gång och komma åt alla användare. Du kan bygga en app en gång och låta den fungera på flera plattformar, eller bygga en app som fungerar som en klient samt ett resurs program (API).

## <a name="getting-started"></a>Komma igång

Välj det [program scenario](authentication-flows-app-scenarios.md) som du vill bygga. Var och en av dessa scenarion börjar med en översikt och länkar till en snabb start för att hjälpa dig att komma igång:

- [Ensidesapplikation (SPA)](scenario-spa-overview.md)
- [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)
- [Webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md)
- [Skyddat webb-API](scenario-protected-web-api-overview.md)
- [Webb-API som anropar webb-API:er](scenario-web-api-call-api-overview.md)
- [Skrivbordsapp](scenario-desktop-overview.md)
- [Daemon-app](scenario-daemon-overview.md)
- [Mobilapp](scenario-mobile-overview.md)

När du arbetar med Microsoft Identity Platform för att integrera autentisering och auktorisering i dina appar kan du referera till den här avbildningen som beskriver de vanligaste app-scenarierna och deras identitets komponenter. Markera bilden om du vill visa den i full storlek.

[![Metro-karta som visar flera program scenarier i Microsoft Identity Platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Lär dig begrepp för autentisering

Lär dig hur grundläggande autentisering och Azure AD-koncept gäller för Microsoft Identity Platform i följande rekommenderade artiklar:

- [Grundläggande om autentisering](./authentication-vs-authorization.md)
- [Program-och tjänst huvud namn](app-objects-and-service-principals.md)
- [Mål grupper](v2-supported-account-types.md)
- [Behörigheter och samtycke](v2-permissions-and-consent.md)
- [ID-token](id-tokens.md)
- [Åtkomsttokens](access-tokens.md)
- [Autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Fler alternativ för identitets-och åtkomst hantering

[Azure AD B2C](../../active-directory-b2c/overview.md) – Bygg kund program som användarna kan logga in på att använda sina sociala konton som Facebook eller Google, eller genom att använda en e-postadress och ett lösen ord.

[Azure AD B2B](../external-identities/what-is-b2b.md) – Bjud in externa användare i Azure AD-klienten som "gäst användare" och tilldela behörigheter för auktorisering medan de använder sina befintliga autentiseringsuppgifter för autentisering.

> [!TIP]
> Letar du efter dokumentation om *Azure Active Directory Developer Platform (v 1.0)* ? Se [Översikt över Azure Active Directory för utvecklare (v 1.0)](../azuread-dev/v1-overview.md).

## <a name="next-steps"></a>Nästa steg

Om du har ett Azure-konto har du redan åtkomst till en Azure Active Directory-klient, men de flesta Microsoft Identity Platform-utvecklare behöver sin egen Azure AD-klient för att kunna använda samtidigt som de utvecklar program, en "dev-klient".

Lär dig hur du skapar en egen klient som du kan använda när du skapar dina program:

[Snabb start: Konfigurera en Azure AD-klient](quickstart-create-new-tenant.md)