---
title: 'Azure Active Directory Domain Services: Felsöka säker LDAP-konfiguration | Microsoft Docs'
description: Felsöka säker LDAP för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: dde4a02e5be32d5549ba499742d1ab650fa146c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246591"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - felsökning säker LDAP-konfiguration

Den här artikeln innehåller lösningar för vanliga problem när [konfigurera säkert LDAP](configure-ldaps.md) för Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Konfiguration för säker LDAP-Nätverkssäkerhetsgrupp

**Varningsmeddelande:**

*Säkert LDAP över internet har aktiverats för den hanterade domänen. Åtkomst till port 636 är dock inte låst nedåt med hjälp av en nätverkssäkerhetsgrupp. Detta kan medföra att användarkonton på den hanterade domänen för lösenord brute force-attacker.*

### <a name="secure-ldap-port"></a>Säker LDAP-port

När säker LDAP är aktiverad, rekommenderar vi skapar ytterligare regler som tillåter inkommande LDAPS åtkomst endast från vissa IP-adresser. De här reglerna kan du skydda din domän från brute force-attacker som kan utgöra en säkerhetsrisk. Port 636 ger åtkomst till din hanterade domän. Här är hur du uppdaterar din NSG för att tillåta åtkomst för säkert LDAP:

1. Navigera till den [Nätverkssäkerhetsgrupper fliken](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure portal
2. Välj NSG: N som är associerade med din domän från tabellen.
3. Klicka på **ingående säkerhetsregler**
4. Skapa regel för port 636
   1. Klicka på **Lägg till** i det övre navigeringsfältet.
   2. Välj **IP-adresser** för källan.
   3. Ange källportsintervall för den här regeln.
   4. Indata ”636” för målportsintervall.
   5. Protokollet är **TCP**.
   6. Ge regeln ett namn, beskrivning och en prioritet. Den här regelns prioritet bör vara högre än ”neka alla”-regelns prioritet, om du har en.
   7. Klicka på **OK**.
5. Kontrollera att regeln har skapats.
6. Kontrollera hälsan för domänen i två timmar att kontrollera att du har slutfört stegen korrekt.

> [!TIP]
> Port 636 är den enda regeln som behövs för Azure AD Domain Services att köras. Mer information finns i [riktlinjer för nätverk](network-considerations.md) eller [felsöka NSG-konfiguration](alert-nsg.md) artiklar.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Säker LDAP-certifikatet upphör att gälla

**Varningsmeddelande:**

*Certifikatet för säkert LDAP för den hanterade domänen upphör att gälla [date]].*

**Lösning:**

Skapa ett nytt certifikat för säkert LDAP genom att följa anvisningarna i den [konfigurera säker LDAP](configure-ldaps.md) artikeln.

## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](contact-us.md).
