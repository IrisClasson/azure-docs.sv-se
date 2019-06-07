---
title: Delegera minst Privilegierade roller genom att administratören uppgift – Azure Active Directory | Microsoft Docs
description: Roller att delegera för identitet uppgifter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3f21f552add551ac2434618b184eb18c53ad5be
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752230"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Administratörsroller av admin-aktivitet i Azure Active Directory

I den här artikeln hittar du information som behövs för att begränsa en användares administratörsbehörighet genom att tilldela minst Privilegierade roller i Azure Active Directory (AD Azure). Du hittar administratörsåtgärder ordnade efter funktionsområde och minst Privilegierade roller som krävs för att utföra varje uppgift, tillsammans med ytterligare icke - globala administratörsroller som kan utföra uppgiften.

## <a name="application-proxy"></a>Programproxy

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera application proxy-app | Programadministratör | 
Konfigurera egenskaper för anslutning | Programadministratör | 
Skapa programregistrering när möjligheten är inaktiverad för alla användare | Programutvecklare | Molnprogramadministratör, programadministratör
Skapa anslutningsgrupp | Programadministratör | 
Ta bort anslutningsgrupp | Programadministratör | 
Inaktivera programproxy | Programadministratör | 
Hämta anslutningsapptjänsten | Programadministratör | 
Läsa alla konfigurationen | Programadministratör | 

## <a name="b2c"></a>B2C

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD B2C-kataloger | Alla icke-gästanvändare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa B2C-program | Global administratör | 
Skapa företagsprogram | Molnprogramadministratör | Programadministratör
Skapa, läsa, uppdatera och ta bort B2C-principer | Global administratör | 
Skapa, läsa, uppdatera och ta bort identitetsprovidrar | Global administratör | 
Skapa, läsa, uppdatera och ta bort användarflöden med återställning av lösenord | Global administratör | 
Skapa, läsa, uppdatera och ta bort profilredigering användarflöden | Global administratör | 
Skapa, läsa, uppdatera och ta bort på användarens flöden | Global administratör | 
Skapa, läsa, uppdatera och ta bort registrering användarflödet |Global administratör | 
Skapa, läsa, uppdatera och ta bort användarattribut | Global administratör | 
Skapa, läsa, uppdatera och ta bort användare | Global administratör ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Läsa alla konfigurationen | Global administratör | 
Läs B2C-granskningsloggar | Global administratör ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C-globala administratörer har inte samma behörigheter som global Azure AD-administratörer. Om du har behörighet för global administratör för Azure AD B2C kan du kontrollera att du är i en Azure AD B2C-katalog och inte en Azure AD-katalog.

## <a name="company-branding"></a>Företagsanpassning

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera varumärkesexponering | Global administratör | 
Läsa alla konfigurationen | Katalogläsare | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Egenskaper för företag

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera egenskaper för företag | Global administratör | 

## <a name="connect"></a>Anslut

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Direktautentisering | Global administratör | 
Läsa alla konfigurationen | Global administratör | 
Smidig enkel inloggning | Global administratör | 

## <a name="connect-health"></a>Connect Health

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Lägga till eller ta bort tjänster | Ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Tillämpa korrigeringar till fel | Deltagare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera meddelanden | Deltagare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Konfigurera inställningar | Ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurera synkronisering meddelanden | Deltagare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Ägare
Läs ADFS säkerhetsrapporter | Säkerhetsläsare | Deltagare, ägare
Läsa alla konfigurationen | Läsare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Läs synkroniseringsfel | Läsare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Läs synkroniseringstjänster | Läsare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar | Läsare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa mått och aviseringar | Läsare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare
Visa sync-tjänstmått och aviseringar | Läsare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Deltagare, ägare


## <a name="custom-domain-names"></a>Egna domännamn

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Hantera domäner | Global administratör | 
Läsa alla konfigurationen | Katalogläsare | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Skapa Azure AD Domain Services-instans | Global administratör | 
Utföra alla uppgifter i Azure AD Domain Services | Azure AD DC-administratörsgruppen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Läsa alla konfigurationen | Läsare på Azure-prenumeration som innehåller AD DS-tjänsten | 

## <a name="devices"></a>Enheter

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Inaktivera enhet | Molnenhetsadministratör | 
Aktivera enhet | Molnenhetsadministratör | 
Läs grundläggande konfiguration | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Läs BitLocker-nycklar | Säkerhetsläsare | För lösenordsadministratör, säkerhetsadministratör

## <a name="enterprise-applications"></a>Företagsprogram

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Godkänna alla delegerade behörigheter | Molnprogramadministratör | Programadministratör
Godkänna programbehörigheter inte, inklusive Microsoft Graph eller Azure AD Graph | Molnprogramadministratör | Programadministratör
Godkänner du behörigheter för programmet till Microsoft Graph eller Azure AD Graph | Global administratör | 
Samtycka till program som har åtkomst till egna data | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Skapa företagsprogram | Molnprogramadministratör | Programadministratör
Hantera Application Proxy | Programadministratör | 
Hantera användarinställningar | Global administratör | 
Läs åtkomstgranskning för en grupp eller en app | Säkerhetsläsare | Säkerhetsadministratör Användaradministratör
Läsa alla konfigurationen | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Uppdatera enterprise programtilldelningar | Enterprise programmets ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera enterprise programägare | Enterprise programmets ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera egenskaper för enterprise-program | Enterprise programmets ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera enterprise programetablering | Enterprise programmets ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera företagsprogram via självbetjäning | Enterprise programmets ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör
Uppdatera egenskaper för enkel inloggning | Enterprise programmets ägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Molnprogramadministratör, programadministratör

## <a name="groups"></a>Grupper

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Användaradministratör | 
Skapa grupp | Användaradministratör | 
Skapa, uppdatera eller ta bort åtkomstgranskning för en grupp eller en app | Användaradministratör | 
Hantera förfallodatum | Användaradministratör | 
Hantera gruppinställningar | Global administratör | 
Läsa alla konfigurationen (utom dolda medlemskap) | Katalogläsare | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Läsa dolda medlemskap | Gruppmedlem | Gruppägare, lösenordsadministratör, Exchange-administratör, SharePoint-administratör, Teams administratör, Användaradministratör
Läsa medlemskap i grupper med dolda medlemskap | Supportavdelningsadministratör | Användaradministratör, Teams-administratör
Återkalla licens | Licensadministratör | Användaradministratör
Uppdatera medlemskap | Gruppägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör
Uppdatera gruppägare | Gruppägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör
Uppdatera gruppegenskaper | Gruppägare ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Användaradministratör

## <a name="identity-protection"></a>Identity Protection

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera aviseringar| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera MFA-principen| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera riskprincip för inloggning| Säkerhetsadministratör | 
Konfigurera och aktivera eller inaktivera riskprincip för användare | Säkerhetsadministratör | 
Konfigurera veckovisa sammandrag | Säkerhetsadministratör| 
Stäng alla riskhändelser | Säkerhetsadministratör | 
Åtgärda och avvisa säkerhetsproblem | Säkerhetsadministratör | 
Läsa alla konfigurationen | Säkerhetsläsare | 
Läsa alla riskhändelser | Säkerhetsläsare | 
Läsa sårbarheter | Säkerhetsläsare | 

## <a name="licenses"></a>Licenser

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Tilldela licens | Licensadministratör | Användaradministratör
Läsa alla konfigurationen | Katalogläsare | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Återkalla licens | Licensadministratör | Användaradministratör
Försök eller köp prenumeration | Faktureringsadministratör | 


## <a name="monitoring---audit-logs"></a>Övervakning - granskningsloggar

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Läs granskningsloggar | Rapportläsare | Security-läsare, säkerhetsadministratör

## <a name="monitoring---sign-ins"></a>Övervakning - inloggningar

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Läsa inloggning loggar | Rapportläsare | Security-läsare, säkerhetsadministratör

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Ta bort alla befintliga applösenord som de valda användarna | Global administratör | 
Inaktivera MFA | Global administratör | 
Aktivera MFA | Global administratör | 
Hantera MFA-tjänstinställningar | Global administratör | 
Kräv att valda användare ska uppge kontaktmetoder igen | Autentisering-administratör | 
Återställ multifaktorautentisering på alla sparade enheter  | Autentisering-administratör | 

## <a name="mfa-server"></a>MFA-server

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Blockera/avblockera användare | Global administratör | 
Konfigurera kontoutelåsning | Global administratör | 
Konfigurera cachelagringsregler | Global administratör | 
Konfigurera bedrägerivarning | Global administratör
Konfigurera meddelanden | Global administratör | 
Konfigurera engångsförbikoppling | Global administratör | 
Konfigurera inställningarna för telefonsamtal | Global administratör | 
Konfigurera providers | Global administratör | 
Konfigurera serverinställningar för | Global administratör | 
Läsa Aktivitetsrapport | Global administratör | 
Läsa alla konfigurationen | Global administratör | 
Läsa serverstatus | Global administratör |  

## <a name="organizational-relationships"></a>Organisationens relationer

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Hantera Identitetsproviders | Global administratör | 
Hantera inställningar | Global administratör | 
Hantera användningsvillkor | Global administratör | 
Läsa alla konfigurationen | Global administratör | 

## <a name="password-reset"></a>Lösenordsåterställning

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör |
Konfigurera anpassning | Global administratör |
Konfigurera meddelanden | Global administratör |
Konfigurera lokal integrering | Global administratör |
Konfigurera egenskaper för återställning av lösenord | Användaradministratör | Global administratör
Konfigurera registrering | Global administratör |
Läsa alla konfigurationen | Säkerhetsadministratör | Användaradministratör |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Tilldela användare till roller | Privilegierad rolladministratör | 
Konfigurera rollinställningar | Privilegierad rolladministratör | 
Visa aktivitetsrapporter | Säkerhetsläsare | 
Visa rollmedlemskap | Säkerhetsläsare | 

## <a name="roles-and-administrators"></a>Roller och administratörer

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Hantera rolltilldelningar | Privilegierad rolladministratör | 
Läs åtkomstgranskning av en Azure AD-roll  | Säkerhetsläsare | Säkerhetsadministratör privilegierad rolladministratör
Läsa alla konfigurationen | Standard-användarrollen ([finns i dokumentationen till](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Säkerhet – autentiseringsmetoder

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera autentiseringsmetoder | Global administratör | 
Läsa alla konfigurationen | Global administratör | 

## <a name="security---conditional-access"></a>Säkerhet – villkorlig åtkomst

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Konfigurera MFA betrodda IP-adresser | Administratör för villkorsstyrd åtkomst | 
Skapa anpassade kontroller | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa namngivna platser | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa principer | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa användningsvillkor | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Skapa certifikat för VPN-anslutning | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Ta bort den klassiska principen | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Ta bort användningsvillkor | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Ta bort certifikat för VPN-anslutning | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Inaktivera den klassiska principen | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Hantera anpassade kontroller | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Hantera namngivna platser | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Hantera användningsvillkor | Administratör för villkorsstyrd åtkomst | Säkerhetsadministratör
Läsa alla konfigurationen | Säkerhetsläsare | Säkerhetsadministratör
Läs namngivna platser | Säkerhetsläsare | Villkorlig åtkomst administratör, säkerhetsadministratör

## <a name="security---identity-security-score"></a>Säkerhet – identitet säkerhetspoäng

Aktivitet | Minst Privilegierade roller | Ytterligare roller | 
---- | --------------------- | ----------------
Läsa alla konfigurationen | Säkerhetsläsare | Säkerhetsadministratör
Läs säkerhetspoäng | Säkerhetsläsare | Säkerhetsadministratör
Uppdateringsstatus för händelse | Säkerhetsadministratör | 

## <a name="security---risky-sign-ins"></a>Säkerhet – riskfyllda inloggningar

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Läsa alla konfigurationen | Säkerhetsläsare | 
Läs riskfyllda inloggningar | Säkerhetsläsare | 

## <a name="security---users-flagged-for-risk"></a>Säkerhet – användare som har flaggats för risk

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Ignorera alla händelser | Säkerhetsadministratör | 
Läsa alla konfigurationen | Säkerhetsläsare | 
Läsa användare som har flaggats för risk | Säkerhetsläsare | 

## <a name="users"></a>Användare

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Lägga till användare till katalogroll | Privilegierad rolladministratör | 
Lägga till användare i grupp | Användaradministratör | 
Tilldela licens | Licensadministratör | Användaradministratör
Skapa gästanvändare | Gäst bjuder in | Användaradministratör
Skapa användare | Användaradministratör | 
Ta bort användare | Användaradministratör | 
Ogiltigförklara uppdateringstoken för begränsade administratörer (se dokumentationen) | Användaradministratör | 
Ogiltigförklara uppdateringstoken för icke-administratörer (se dokumentationen) | Lösenordsadministratör | Användaradministratör
Ogiltigförklara uppdateringstoken för privilegierade administratörer (se dokumentationen) | Global administratör | 
Läs grundläggande konfiguration | Standard-användarrollen ([finns i dokumentationen](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Återställa lösenord för begränsade administratörer (se dokumentationen) | Användaradministratör | 
Återställa lösenord för icke-administratörer (se dokumentationen) | Lösenordsadministratör | Användaradministratör
Återställa lösenord för privilegierade administratörer | Global administratör | 
Återkalla licens | Licensadministratör | Användaradministratör
Uppdatera alla egenskaper utom User Principal Name | Användaradministratör | 
Uppdatera användarens huvudnamn för begränsade administratörer (se dokumentationen) | Användaradministratör | 
Uppdatera User Principal Name-egenskapen på Privilegierade administratörer (se dokumentationen) | Global administratör | 
Uppdatera användarinställningarna | Global administratör | 


## <a name="support"></a>Support

Aktivitet | Minst Privilegierade roller | Ytterligare roller
---- | --------------------- | ----------------
Skicka supportbegäran | Tjänstadministratör | Programadministratör, Azure Information Protection-administratör, fakturering, Molnprogramadministratör, efterlevnad administratör, Dynamics 365-administratör, skrivbord Analytics administratör, Exchange-administratören administratörslösenord Administratör, Intune-administratör, Skype för företagsadministratören, Power BI-administratör, Privilegierade autentisering administratör, SharePoint-administratör, Teams kommunikation administratör, Teams-administratör kan användare med rollen, Workplace Analytics-administratör

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort azure AD-administratörsroller](directory-manage-roles-portal.md)
* [Referera till Azure AD-administratörsroller](directory-assign-admin-roles.md)
