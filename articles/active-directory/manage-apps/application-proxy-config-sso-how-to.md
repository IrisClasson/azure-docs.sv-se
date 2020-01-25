---
title: Så här konfigurerar du enkel inloggning till en Application Proxy-app
description: Hur du kan konfigurera enkel inloggning till din application proxy-program snabbt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712028"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Så här konfigurerar du enkel inloggning till ett Application Proxy-program

Enkel inloggning (SSO) kan användarna komma åt ett program utan autentiseras flera gånger. Det tillåter enkel autentisering kan förekomma i molnet mot Azure Active Directory, och gör att tjänsten eller anslutningen att personifiera användare för att slutföra alla ytterligare autentiseringsfrågor från programmet.

## <a name="how-to-configure-single-sign-on"></a>Hur du konfigurerar enkel inloggning på
Om du vill konfigurera enkel inloggning, se till att ditt program är konfigurerad för förautentisering via Azure Active Directory. Om du vill göra den här konfigurationen, gå till **Azure Active Directory**  - &gt; **företagsprogram**  - &gt; **alla program**   - &gt; Programmets  **- &gt; Application Proxy**. På den här sidan finns i fältet ”förautentisering” och kontrollerar som är inställt på ”Azure Active Directory. 

Mer information om de före autentiseringsmetoderna finns i steg 4 i den [publishing appdokument](application-proxy-add-on-premises-application.md).

   ![Förautentiseringsmetod i Azure-portalen](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera enkel inloggning lägen för Application Proxy-program
Konfigurera vilken typ av enkel inloggning. Metoderna som inloggning klassificeras baserat på vilken typ av autentisering backend-programmet använder. App Proxy-program stöder tre typer av inloggning:

-   **Lösenordsbaserad inloggning**: lösenordsbaserad inloggning kan användas för alla program som använder fälten användarnamn och lösenord för att logga in. Konfigurationsstegen finns i [konfigurera lösenord för enkel inloggning för ett Azure AD-galleriprogram](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrerad Windows-autentisering**: för program som använder integrerad Windows autentisering (IWA), enkel inloggning är aktiverat via Kerberos-begränsad delegering (KCD). Den här metoden ger Programproxyns-kopplingar behörighet i Active Directory att personifiera användare, och för att skicka och ta emot token för deras räkning. Information om hur du konfigurerar KCD finns i den [enkel inloggning med KCD-dokumentation](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Rubrikbaserad inloggning**: rubrikbaserad inloggning är aktiverat via ett partnerskap och kräver ytterligare konfigurering. Mer information om partnerskap och stegvisa instruktioner för att konfigurera enkel inloggning till ett program som använder rubriker för autentisering finns i den [PingAccess för Azure AD-dokumentationen](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML enkel inloggning**: med enkel inloggning med SAML, autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar information inloggning till programmet via en anslutningsprotokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk. Information om hur du konfigurerar SAML enkel inloggning finns i [SAML för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Var och en av dessa alternativ kan hittas genom att gå till ditt program i ”program” och öppna den **enkel inloggning** sida på den vänstra menyn. Observera att om ditt program skapades på den gamla portalen kanske du inte ser alla dessa alternativ.

På den här sidan kan du också ser något alternativ för ytterligare inloggning: länkad inloggning. Det här alternativet stöds också av programproxy. Det här alternativet läggs dock inte enkel inloggning till programmet. Men programmet kan redan ha enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services. 

Det här alternativet kan en administratör för att skapa en länk till ett program att första mark användare på vid åtkomst till programmet. Om det finns ett program som är konfigurerad för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör exempelvis använda alternativet ”länkad inloggning” för att skapa en länk till den på åtkomstpanelen.

## <a name="next-steps"></a>Nästa steg
- [Lösen ords valv för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos-begränsad delegering för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Huvud-baserad autentisering för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
