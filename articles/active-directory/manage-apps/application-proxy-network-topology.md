---
title: Nätverks sto pol faktorer för Azure AD-programproxy
description: Beskriver topologiöverväganden för nätverk när du använder Azure AD-programproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075b2c92168afe0c366608266c38b14394b73cff
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375761"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Topologiöverväganden för nätverk när du använder Azure Active Directory Application Proxy

Den här artikeln förklarar topologiöverväganden för nätverk när du använder Azure Active Directory (Azure AD) Application Proxy för att publicera och komma åt dina program via fjärranslutning.

## <a name="traffic-flow"></a>Trafikflöde

När ett program publiceras via Azure AD Application Proxy, flödar trafiken från användare till program genom tre anslutningar:

1. Användaren ansluter till Azure AD Application Proxy-tjänsten offentlig slutpunkt på Azure
1. Application Proxy-tjänsten ansluts till anslutningsappen för programproxyn
1. Application Proxy connector ansluter till målprogrammet

![Diagram över trafikflödet från användare till målprogrammet](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Klientplats och Application Proxy-tjänsten

När du registrerar dig för en Azure AD-klient bestäms regionen för din klient organisation av landet/regionen som du anger. När du aktiverar programproxy valt tjänstinstanser Application Proxy för din klient eller skapats i samma region som din Azure AD-klient eller den närmaste regionen till den.

Till exempel, om din Azure AD-klient land eller region är Storbritannien, använda alla Application Proxy-kopplingar tjänstinstanser i datacenter för Europa. När dina användare åtkomst publicerat program, passerar trafiken tjänstinstanser Application Proxy på den här platsen.

## <a name="considerations-for-reducing-latency"></a>Överväganden för att minska svarstiden

Alla proxylösningar introducera svarstid i nätverksanslutningen. Oavsett vilken proxy- eller VPN-lösning som du väljer som din lösning för fjärråtkomst, innehåller den alltid en uppsättning servrar som aktiverar anslutningen till i företagsnätverket.

Organisationer som vanligtvis inkluderar serverslutpunkter i sina perimeternätverk. Med Azure AD Application Proxy dock flödar trafik genom proxytjänst i molnet när anslutningsapparna finns i företagsnätverket. Det krävs inga perimeternätverk.

I nästa avsnitt innehåller ytterligare förslag för att hjälpa dig att minska ytterligare fördröjning. 

### <a name="connector-placement"></a>Connector placering

Programproxy väljer platsen för instanser för dig, baserat på din klientplats. Men får du du bestämma om du installerar anslutningstjänsten, ger dig kraften att definiera egenskaperna för fördröjning av nätverkstrafiken.

När du konfigurerar tjänsten Application Proxy kan du ställa följande frågor:

- Var finns appen?
- Var finns de flesta användare som har åtkomst till appen?
- Var finns Application Proxy-instans?
- Du redan har en dedikerad nätverksanslutning till Azure-Datacenter konfigurera Azure ExpressRoute eller ett liknande VPN?

Anslutningen måste kommunicera med både Azure och dina program (steg 2 och 3 i trafikflödesdiagram), så placeringen av anslutningen påverkar svarstiden för dessa två anslutningar. När du utvärderar placeringen av kopplingen, Tänk på följande saker övervägas:

- Om du vill använda Kerberos-begränsad delegering (KCD) för enkel inloggning behöver åtkomst till ett datacenter med anslutningstjänsten. Dessutom måste connector-server vara domänanslutna.  
- Installera connector närmare till programmet tveksamheter.

### <a name="general-approach-to-minimize-latency"></a>Allmän metod för att minimera fördröjning

Du kan minimera svarstiden för slutpunkt till slutpunkt-trafik genom att optimera varje nätverksanslutning. Varje anslutning kan optimeras genom att:

- Minskar avståndet mellan två slutpunkterna hopp.
- Välja rätt nätverk att passera. Exempel: gå igenom ett privat nätverk i stället för det offentliga Internet kanske snabbare, på grund av dedicerade länkar.

Om du har en dedikerad VPN eller ExpressRoute-länk mellan Azure och företagets nätverk kan du använda den.

## <a name="focus-your-optimization-strategy"></a>Fokusera din strategi för optimering

Det finns lite som du kan göra för att kontrollera anslutningen mellan dina användare och programproxytjänsten. Användare kan komma åt dina appar från ett hem nätverk, ett kafé eller ett annat land/en annan region. I stället kan du optimera anslutningar från Application Proxy-tjänsten till Application Proxy-kopplingar till appar. Överväg att använda följande mönster i din miljö.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Mönstret 1: Placera nära programmet-anslutningen

Placera nära målprogrammet kopplingen i kundens nätverk. Den här konfigurationen minskar steg 3 i diagrammet topografi eftersom anslutningen och program är Stäng.

Om din anslutningsapp behöver åtkomst till domänkontrollanten, är det fördelaktigt med det här mönstret. De flesta av våra kunder att använda det här mönstret eftersom den fungerar bra för de flesta scenarier. Det här mönstret kan också kombineras med mönstret 2 för att optimera trafik mellan tjänsten och anslutningen.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Mönstret 2: Dra nytta av ExpressRoute med Microsoft-peering

Om du har ExpressRoute som konfigurerats med Microsoft-peering kan använda du snabbare ExpressRoute-anslutningen för trafik mellan Application Proxy och anslutningen. Anslutningen är fortfarande i nätverket, nära appen.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Mönster 3: Dra nytta av ExpressRoute med privat peering

Om du har en dedikerad VPN eller ExpressRoute som konfigurerats med privat peering mellan Azure och företagets nätverk, har du ett annat alternativ. I den här konfigurationen anses vanligtvis det virtuella nätverket i Azure som ett tillägg till företagets nätverk. Så kan du installera connector i Azure-datacentret och fortfarande uppfyller kraven för låg fördröjning för anslutning till app-anslutningen.

Svarstid komprometteras inte eftersom trafik som flödar över en dedikerad anslutning. Du kan också få bättre svarstid för Application Proxy-tjänstanslutning eftersom kopplingen är installerad i ett Azure-datacenter nära din plats för Azure AD-klient.

![Diagram över anslutningstjänsten installerad i ett Azure-datacenter](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andra metoder

Fokus i den här artikeln är anslutningen placering, men du kan också ändra placeringen av programmet för att få bättre svarstid egenskaper.

Allt mer flyttar organisationer sina nätverk i värdbaserade miljöer. Detta gör det enkelt att placera sina appar i en miljö som också är en del av företagets nätverk, och fortfarande var i domänen. I det här fallet kan de mönster som beskrivs i föregående avsnitt tillämpas på den nya platsen för programmet. Om du överväger det här alternativet, se [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Tänk också på att organisera dina anslutningar med [anslutnings grupper](application-proxy-connector-groups.md) för att rikta in appar som finns på olika platser och i nätverk.

## <a name="common-use-cases"></a>Vanliga användarsituationer

I det här avsnittet ska gå vi igenom några vanliga scenarier. Anta att den Azure AD-klient (och därför proxy tjänsteslutpunkt) finns i USA (USA). Överväganden som beskrivs i dessa fall gäller även för andra regioner i hela världen användning.

Dessa scenarier kan vi anropa en ”hopp” för varje anslutning och nummer dem enklare beskrivning:

- **Hopp 1**: användare till Application Proxy-tjänsten
- **Hopp 2**: Application Proxy service till Application Proxy Connector
- **Hopp 3**: Application Proxy Connector till mål programmet 

### <a name="use-case-1"></a>Användningsfall 1

**Scenario:** Appen är i en organisations nätverk i USA, med användare i samma region. Inga ExpressRoute eller VPN finns mellan Azure-datacentret och företagets nätverk.

**Rekommendation:** Följ mönstret 1, förklaras i föregående avsnitt. Överväg att använda ExpressRoute för bättre svarstid, om det behövs.

Det här är ett enkelt mönster. Du kan optimera hopp 3 genom att placera anslutningen i appen. Detta är också det naturliga valet eftersom anslutningen installeras vanligtvis med åtkomst till appen och till datacenter för att utföra åtgärder för KCD.

![Diagram som visar användare, proxy, anslutning och app är alla i USA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Användningsfall 2

**Scenario:** Appen är i en organisations nätverk i USA, där användare sprids ut globalt. Inga ExpressRoute eller VPN finns mellan Azure-datacentret och företagets nätverk.

**Rekommendation:** Följ mönstret 1, förklaras i föregående avsnitt.

Igen, det vanliga mönstret är att optimera hopp 3, där du placerar anslutningen i appen. Hopp 3 är inte vanligtvis dyr, om det är allt inom samma region. Hopp 1 kan dock dyrare beroende på var användaren befinner, eftersom användare över hela världen måste komma åt Application Proxy-instans i USA. Det är värt att någon proxy-lösning har liknande egenskaper om användare att sprida ut globalt.

![Användare sprids globalt, men allt annat är i USA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Användningsfall 3

**Scenario:** Appen finns i en organisations nätverk i USA. ExpressRoute med Microsoft-peering finns mellan Azure och företagets nätverk.

**Rekommendation:** Följ mönster 1 och 2, förklaras i föregående avsnitt.

Placera först anslutningen så nära som möjligt till appen. Systemet använder sedan automatiskt ExpressRoute för hopp 2.

Om ExpressRoute-länken använder Microsoft-peering, flödar trafiken mellan proxyservern och anslutningen via länken. Hopp 2 har optimerats svarstid.

![Diagram över ExpressRoute mellan proxy och anslutning](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Användningsfall 4

**Scenario:** Appen finns i en organisations nätverk i USA. ExpressRoute med privat peering finns mellan Azure och företagets nätverk.

**Rekommendation:** Följ mönstret 3, förklaras i föregående avsnitt.

Placera anslutningen i Azure-datacentret som är ansluten till företagsnätverket via ExpressRoute privat peering.

Anslutningen kan placeras i Azure-datacentret. Eftersom anslutningen har fortfarande åtkomst till programmet och datacenter via det privata nätverket, förblir hopp 3 optimerad. Dessutom optimeras hopp 2 ytterligare.

![Koppling i Azure Data Center, ExpressRoute mellan koppling och app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Användningsfall 5

**Scenario:** Appen finns i en organisations nätverk i EU, med Application Proxy-instansen och de flesta användare i USA.

**Rekommendation:** Placera kopplingen nära appen. Eftersom USA användarna kommer åt en Application Proxy-instans som råkar vara i samma region, är hopp 1 inte för dyrt. Hopp 3 optimeras. Överväg att använda ExpressRoute för att optimera hopp 2.

![Diagrammet visar användare och proxy i USA, anslutning och app i EU](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Du kan också använda en annan variant i det här fallet. Om de flesta användare i organisationen finns i USA, så risken som finns som utökar ditt nätverk till USA också. Placera kopplingen i USA och använda dedikerade interna företagsnätverket raden till programmet inom EU. Det här sättet hopp 2 och 3 optimeras.

![Diagrammet visar användare, proxy och anslutning i USA, app i EU](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera programproxy](application-proxy-add-on-premises-application.md)
- [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)
- [Felsök problem med Application Proxy](application-proxy-troubleshoot.md)
