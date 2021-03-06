---
title: Kompatibilitetslista för Azure AD-federation
description: Den här sidan har identitets leverantörer som inte kommer från Microsoft och som kan användas för att implementera enkel inloggning.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3ca659535c225ca6736fe7bba8aa5b0120c684c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359831"
---
# <a name="azure-ad-federation-compatibility-list"></a>Kompatibilitetslista för Azure AD-federation
Azure Active Directory ger enkel inloggning och förbättrad åtkomst till program åtkomst för Office 365 och andra Microsoft Online Services för Hybrid-och molnbaserade implementeringar utan att kräva någon tredje parts lösning. Office 365, som de flesta av Microsofts online tjänster, är integrerat med Azure Active Directory för katalog tjänster, autentisering och auktorisering. Azure Active Directory tillhandahåller även enkel inloggning till tusentals SaaS-program och lokala webb program. Se Azure Active Directory [program galleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) för SaaS-program som stöds. 

## <a name="idp-validation"></a>IDP-verifiering
Om din organisation använder en Federations lösning från tredje part kan du konfigurera enkel inloggning för dina lokala Active Directory användare med Microsoft Online Services, till exempel Office 365, förutsatt att Federations lösningen från tredje part är kompatibel med Azure Active Directory.  Kontakta din identitets leverantör om du har frågor om kompatibilitet.  Om du vill se en lista över identitets leverantörer som tidigare har testats för kompatibilitet med Azure AD, klickar du [här](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft tillhandahåller inte längre verifierings testning till oberoende identitets leverantörer för kompatibilitet med Azure Active Directory. Om du vill testa din produkt för interoperabilitet, se dessa [rikt linjer](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Nästa steg

- [Integrerar dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
