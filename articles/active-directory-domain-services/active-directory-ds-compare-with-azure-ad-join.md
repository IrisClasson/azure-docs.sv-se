---
title: Jämför Azure AD-anslutning och Azure Active Directory Domain Services | Microsoft Docs
description: Välja mellan Azure AD-anslutning och Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: eaa8cb54a46b1ff3c2c0f7c40c824f6ddcca16b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234966"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Välj mellan Azure Active Directory-koppling och Azure Active Directory Domain Services
Den här artikeln beskrivs skillnaderna mellan Azure Active Directory (AD)-anslutning och Azure AD Domain Services och hjälper till att du väljer, baserat på användningsfall.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Azure AD-registrerade och Azure AD-anslutna enheter
Azure AD kan du hantera identiteten för enheter som används av din organisation och kontrollera åtkomsten till företagets resurser från dessa enheter. Användare kan registrera sina personliga enheter (bring-your-own) med Azure AD, som innehåller enheten med en identitet. Därefter kan Azure AD autentisera enheten när en användare loggar in på Azure AD och använder enheten för att komma åt skyddade resurser. Dessutom kan du hantera enheten med Mobile Device Management (MDM) programvara som Microsoft Intune. Den här funktionen kan du begränsa åtkomsten till känsliga resurser från hanterade och policy-kompatibla enheter.

Du kan också ansluta till organisationen företagsägda enheter till Azure AD. Den här mekanismen har samma fördelar med att registrera en personlig enhet med Azure AD. Dessutom kan användare logga in på enheten med sina företagsuppgifter. Azure AD-anslutna enheter ger följande fördelar:
* Single-sign-on (SSO) vilket program som skyddas av Azure AD
* Princip-kompatibla företagsnätverksväxling av användarinställningar mellan enheter.
* Åtkomst till Windows Store för företag med hjälp av företagets autentiseringsuppgifter.
* Windows Hello för företag
* Begränsad åtkomst till appar och resurser från enheter som är kompatibel med företagets princip.

| **Typ av enhet** | **Enhetsplattformar** | **Mekanism** |
|:---| --- | --- |
| Personliga enheter | Windows 10, iOS, Android, Mac OS | Azure AD-registrerad |
| Organisation för företagsägda enheter som inte tillhör en lokal AD | Windows 10 | Azure AD-ansluten |
| Organisation för företagsägda enheter som är anslutna till en lokal AD | Windows 10 | Hybrid Azure AD-ansluten |

På en Azure AD ansluten eller registrerad enhet, användarautentisering händer med moderna OAuth/OpenID Connect baserat protokoll. Dessa protokoll är utformade att fungera via internet och är perfekt för mobila scenarier där användarna kommer åt företagsresurser från valfri plats.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Anslut till domän till Azure AD Domain Services hanterade domäner
Azure AD Domain Services är en hanterad AD-domän i Azure-nätverk. Du kan ansluta datorer till den här hanterade domänen med traditionell domänanslutning mekanismer. Windows-klienten (Windows 7, Windows 10) och Windows Server-datorer kan kopplas till den hanterade domänen. Dessutom kan du även ansluta Linux och Mac OS-datorer till den hanterade domänen. När du ansluter en dator till en AD-domän, kan användare logga in på den virtuella datorn med sina företagsuppgifter. Dessa datorer kan hanteras med Grupprincip, därför tillämpa kompatibilitet med organisationens säkerhetsprinciper.

På en domänansluten dator sker autentisering av användare med hjälp av NTLM eller Kerberos-autentiseringsprotokoll. Den domänanslutna datorn behöver åtkomst till domänkontrollanterna i den hanterade domänen för användarautentisering ska fungera. Därför domänanslutna datorer måste vara i samma virtuella nätverk som den hanterade domänen. Alternativt kan domänanslutna datorer måste vara ansluten till den hanterade domänen via en peer-kopplade virtuella nätverket eller via en plats-till-plats VPN/ExpressRoute-anslutning. Den här mekanismen är därför inte passade bra för enheter som mobila eller ansluta till resurser från utanför företagsnätverket.

> [!NOTE]
> Tekniskt sett är det möjligt att ansluta till en lokal klient-arbetsstation till den hanterade domänen via en plats-till-plats VPN eller ExpressRoute-anslutning. Men för slutanvändarenheter som vi rekommenderar starkt du använder antingen registrera enheten med Azure AD (personliga enheter) eller ansluta enheten till Azure AD (enheter). Den här mekanismen fungerar bättre via internet och slutanvändarna kan arbeta från valfri plats. Azure AD Domain Services är perfekt för Windows eller Linux-Server virtuella datorer som distribueras i din Azure-nätverk, där dina program distribueras.


## <a name="summary---key-differences"></a>Sammanfattning – viktiga skillnader
| **Aspekt** | **Azure AD Join** | **Azure AD Domain Services** |
|:---| --- | --- |
| Enheten kontrolleras av | Azure AD | Azure AD Domain Services-hanterad domän |
| Återgivning i katalogen | Enhetsobjekt i Azure AD-katalog. | Datorobjekt i den hanterade AAD-DS-domänen. |
| Autentisering | OAuth/OpenID Connect-baserade protokoll | Kerberos, NTLM-protokoll |
| Hantering | Mobile Device Management (MDM) programvara som Intune | En Grupprincip |
| Nätverk | Fungerar via internet | Kräver att datorerna ska vara i samma virtuella nätverk som den hanterade domänen.|
| Bra för... | Slutanvändarens mobila eller stationära enheter | Server-datorer som distribueras i Azure |


## <a name="next-steps"></a>Nästa steg
### <a name="learn-more-about-azure-ad-domain-services"></a>Läs mer om Azure AD Domain Services
* [Översikt över Azure AD Domain Services](overview.md)
* [Funktioner](active-directory-ds-features.md)
* [Distributionsscenarier](scenarios.md)
* [Ta reda på om Azure AD Domain Services passar dina användningsfall](comparison.md)
* [Förstå hur Azure AD Domain Services synkroniseras med din Azure AD-katalog](synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Läs mer om Azure AD Join
* [Introduktion till enhetshantering i Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Kom igång med Azure AD Domain Services
* [Aktivera Azure AD Domain Services med Azure portal](create-instance.md)
