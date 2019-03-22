---
title: Användarautentisering för Azure Active Directory - Azure Active Directory
description: Hur kan jag som Azure AD-administratör skydda användarautentisering och samtidigt minska påverkan för slutanvändarna?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38bc2f138a8e56fc56b48ccdaf0636a93138f95d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314662"
---
# <a name="what-methods-are-available-for-authentication"></a>Vilka metoder är tillgängliga för autentisering?

Vi hör på nyheterna att lösenord stjäls och identiteter komprometteras. Genom att kräva en andra faktor utöver ett lösenord ökar du direkt säkerheten för din organisation. Microsoft Azure Active Directory (Azure AD) innehåller funktioner såsom Azure Multi-Factor Authentication (Azure MFA) och självåterställning av lösenord (SSPR) med Azure AD för att hjälpa administratörer att skydda sina organisationer och användare med ytterligare autentiseringsmetoder.

Det finns många scenarier som omfattar inloggning till ett program, återställning av lösenord, aktivering av Windows Hello och annat, och dina användare kan bli ombedda att tillhandahålla ytterligare verifiering för att identifiera sig.

Ytterligare verifiering kan ges i form av autentiseringsmetoder som:

* En kod som tillhandahålls i ett e-post- eller textmeddelande
* Ett telefonsamtal
* En avisering eller kod på telefonen
* Svar på säkerhetsfrågor

![Exempel på inloggningssidan login.microsoftonline.com i Chrome](media/overview-authentication/overview-login.png)

Självåterställning av lösenord med Azure MFA och Azure AD ger administratörer kontroll över konfiguration, princip, övervakning och rapportering med hjälp av Azure AD och Azure-portalen för att skydda sina organisationer.

## <a name="self-service-password-reset"></a>Återställning av lösenord för självbetjäning

Självåterställning av lösenord ger användarna möjlighet att återställa sina lösenord utan inblandning av administratören, när och var som helst.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

Självåterställning av lösenord inkluderar:

* **Lösenordsändring:** Jag känner till mitt lösenord men jag vill ändra det till något nytt.
* **Lösenordsåterställning:** Jag kan inte logga in och jag vill återställa lösenordet med hjälp av en eller flera godkända autentiseringsmetoder.
* **Kontoupplåsning:** Jag kan inte logga in eftersom mitt konto är låst, och jag vill låsa upp det med hjälp av en eller flera godkända autentiseringsmetoder.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med autentiseringsmetoder som är godkända av administratör hjälper Azure MFA till att skydda din åtkomst till data och program, samtidigt som behovet av en enkel inloggningsprocess uppfylls.

## <a name="next-steps"></a>Nästa steg

Nästa steg är att sätta igång och konfigurera självåterställning av lösenord och Azure Multi-Factor Authentication.

Kom igång med självåterställning av lösenord genom att läsa [snabbstartsartikeln om att aktivera SSPR](quickstart-sspr.md).

Läs mer om självåterställning av lösenord i artikeln om [ hur självåterställning av lösenord i Azure AD fungerar](concept-sspr-howitworks.md)

Läs mer om Azure Multi-Factor Authentication i artikeln om [ hur Azure Multi-Factor Authentication fungerar](concept-mfa-howitworks.md)
