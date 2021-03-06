---
title: Konfigurera MFA-registrerings principen – Azure Active Directory Identity Protection
description: Lär dig hur du konfigurerar registrerings principen för Azure AD Identity Protection Multi-Factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c6b57eaa2a68906053faade48dd0e63fbf0db7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84464342"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Gör så här: Konfigurera registrerings principen för Azure-Multi-Factor Authentication

Azure AD Identity Protection hjälper dig att hantera distributionen av Azure Multi-Factor Authentication-registrering (MFA) genom att konfigurera en princip för villkorlig åtkomst för att kräva MFA-registrering oavsett vilken modern app för autentisering som du loggar in på.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Vad är registrerings principen för Azure-Multi-Factor Authentication?

Med Azure Multi-Factor Authentication kan du kontrol lera vem du använder mer än bara ett användar namn och lösen ord. Det innehåller ett andra säkerhets lager för användar inloggningar. För att användarna ska kunna svara på MFA-prompter måste de först registrera sig för Azure Multi-Factor Authentication.

Vi rekommenderar att du kräver Azure Multi-Factor Authentication för användar inloggningar eftersom det:

- Ger stark autentisering via en mängd verifierings alternativ.
- Spelar upp en viktig roll när du förbereder din organisation för att själv åtgärda problemet med att identifiera risker i identitets skyddet.

Mer information om Azure Multi-Factor Authentication finns i [Vad är azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Principkonfiguration

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **identitets skydd**  >  **MFA registrerings princip**.
   1. Under **tilldelningar**
      1. **Användare** – Välj **alla användare** eller **Välj personer och grupper** om du vill begränsa distributionen.
         1. Du kan också välja att exkludera användare från principen.
   1. Under **kontroller**
      1. Se till att kryss rutan **Kräv Azure MFA-registrering** är markerad och välj **Välj**.
   1. **Tillämpa princip**  -  **På**
   1. **Spara**

## <a name="user-experience"></a>Användarupplevelse

Azure Active Directory Identity Protection uppmanas användarna att registrera sig nästa gången de loggar in interaktivt och de har 14 dagar på sig att slutföra registreringen. Under den här 14-dagars perioden kan de kringgå registreringen, men i slutet av perioden måste de registreras innan inloggnings processen kan slutföras.

En översikt över den relaterade användar upplevelsen finns i:

- [Inloggnings upplevelser med Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Nästa steg

- [Aktivera inloggnings-och användar risk principer](howto-identity-protection-configure-risk-policies.md)

- [Aktivera återställning av lösen ord för självbetjäning i Azure AD](../authentication/howto-sspr-deployment.md)

- [Aktivera Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
