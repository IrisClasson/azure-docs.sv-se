---
title: Azure ytterdörren Service - belastningsutjämning med Azure application delivery suite | Microsoft Docs
description: Den här artikeln hjälper dig att lära dig om hur Azure rekommenderar belastningsutjämning med dess application delivery suite
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 3d5c0ac068a6644f3499da6c3b642a4a04408370
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790332"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Belastningsutjämning med Azures paket för programleverans

## <a name="introduction"></a>Introduktion
Microsoft Azure tillhandahåller flera global och regionala tjänster för att hantera hur nätverkstrafiken distribueras och belastningsutjämnade: Traffic Manager, ytterdörren Service, Application Gateway och belastningsutjämnare.  Tillsammans med många Azure-regioner och zonindelade hjälper arkitektur, tillsammans med dessa tjänster dig att skapa robusta, skalbara program med höga prestanda.

![Application Delivery Suite ][1]
 
De här tjänsterna är uppdelat i två kategorier:
1. **Globala tjänster för belastningsutjämning** som Traffic Manager och ytterdörren distribuerar trafik från dina slutanvändare över serverdelen regionala mellan moln eller till och med dina lokala tjänster. Global nätverksbelastning dirigerar trafiken till närmaste service-serverdelen och reagerar på förändringar i tjänstpålitligheten eller prestanda till att alltid, maximal prestanda för dina användare. 
2. **Regionala tjänster för belastningsutjämning** som Standard Load Balancer eller Application Gateway ger möjlighet att distribuera trafik i virtuella nätverk (Vnet) mellan dina virtuella datorer (VM) eller zonindelad tjänstslutpunkter inom en region.

Kombinera global och regionala tjänster i ditt program ger en slutpunkt till slutpunkt tillförlitligt, effektivt och säkert sätt att dirigera trafik till och från dina användare till IaaS, PaaS, eller lokala tjänster. I nästa avsnitt beskrivs var och en av dessa tjänster.

## <a name="global-load-balancing"></a>Global belastningsbalansering
**Traffic Manager** tillhandahåller globala DNS belastningsutjämning. Den tittar på DNS-förfrågningar och svarar med en felfri backend, i enlighet med routningsprincip som kunden har valts. Alternativ för routningsmetoder är:
- Prestanda routning för att skicka begäranden till närmaste serverdelen när det gäller svarstider.
- Prioritet routning för att dirigera all trafik till en serverdel med andra serverdelar som tillbaka upp.
- Viktad resursallokering routning, som distribuerar trafik baserat på det värde som tilldelas varje serverdel.
- Geografisk routning för att säkerställa att beställare som finns i vissa geografiska områden riktas till serverdelar mappas till de regionerna (till exempel alla förfrågningar från Spanien ska dirigeras till den centrala Frankrike-Azure-regionen)
- Undernät routning som gör det möjligt att mappa IP-adress intervall servrar så att begäranden som kommer från de som ska skickas till angivna serverdelen (till exempel alla användare som ansluter från ditt företags HQ IP-adressintervall bör få olika webbinnehåll än allmänna användare)

Klienten ansluter direkt till den serverdelen. Med Azure Traffic Manager identifierar när en serverdel är i feltillstånd och sedan omdirigeras klienterna till en annan felfri instans. Referera till [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentation för mer information om tjänsten.

**Azure ytterdörren Service** ger acceleration av dynamisk webbplats (DSA) inklusive global HTTP-belastningsutjämning.  Den tittar på inkommande HTTP-begäranden vägar till närmaste service-serverdelen / region för angivet värdnamn, URL-sökvägen och konfigurerade reglerna.  
Ytterdörren avslutar HTTP-begäranden i utkanten av Microsofts nätverk och avsökningar aktivt för att identifiera programmet eller infrastrukturen hälsotillstånd eller latens ändringar.  Ytterdörren sedan dirigerar alltid trafik till serverdelen snabbaste och tillgängliga (felfri). Referera till Front dörren [routning arkitektur](front-door-routing-architecture.md) information och [trafikroutningsmetoder](front-door-routing-methods.md) mer information om tjänsten.

## <a name="regional-load-balancing"></a>Regionala belastningsutjämning
Application Gateway erbjuder application Deliver controller (ADC) som en tjänst som erbjuder olika Layer 7 belastningsutjämningsfunktioner för ditt program. Den låter kunder optimera webbservergruppens produktivitet genom att avlasta CPU-intensiva SSL-avslutning till application gateway. Andra Layer 7-routningsfunktioner omfattar resursallokeringsdistribution av inkommande trafik, Cookiebaserad sessionstillhörighet, URL-sökvägsbaserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda application gateway. Application Gateway kan konfigureras som en internetuppkopplad gateway, en endast intern gateway eller en kombination av båda. Application Gateway är helt Azure hanterad, skalbar och högtillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
Load Balancer är en del av Azure SDN-stacken tjänstgör höga prestanda och låg latens Layer 4-belastningsutjämning för alla UDP och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till backend-poolen mål med hjälp av TCP- och HTTP-avsökning av hälsotillstånd alternativ för att hantera tjänstens tillgänglighet.


## <a name="choosing-a-global-load-balancer"></a>Välja en global belastningsutjämnare
När du väljer en global belastningsutjämnare mellan Traffic Manager och Azure ytterdörren för global routning, bör du överväga vad är liknande och vad är skillnaden mellan de två tjänsterna.   Båda tjänsterna tillhandahåller
- **Flera geo-redundans:** Om en region slutar att fungera dirigerar trafik sömlöst till den närmaste regionen utan någon åtgärd från programmets ägare.
- **Närmaste regionen routning:** Trafiken dirigeras automatiskt till den närmaste regionen

</br>I följande tabell beskrivs skillnaderna mellan Traffic Manager och Azure ytterdörren Service:</br>

| Traffic Manager | Azure Front Door Service |
| --------------- | ------------------------ |
|**Alla protokoll:** Eftersom Traffic Manager fungerar på DNS-nivå, kan du dirigera alla typer av nätverkstrafik; HTTP, TCP, UDP, osv. | **HTTP-acceleration:** Med ytterdörren är trafik via proxy på den Edge av Microsofts nätverk.  Därför se begäranden för HTTP (S) svarstid och dataflöde förbättringar svarstiden för SSL-förhandling och använder frekvent anslutningar från AFD till ditt program.|
|**Lokal routning:** Trafiken går alltid från punkt till punkt med routning på en DNS-nivå.  Routning från filialen till din lokalt datacenter kan ta en direkt sökväg. med Traffic Manager även på ditt eget nätverk. | **Oberoende skalbarhet:** Eftersom ytterdörren arbetar med HTTP-begäran, begäranden till olika URL-sökvägar kan vara dirigera till andra backend / regionala pooler (mikrotjänster) baserat på regler och hälsotillståndet för varje program mikrotjänst-tjänsten.|
|**Fakturering format:** DNS-baserade fakturering kan skalas upp med dina användare och tjänster med fler användare, högplaåter för att minska kostnaden vid högre användning. |**Infogad säkerhet:** Åtkomsten kan regler som t.ex hastighetsbegränsning, och IP-åtkomstkontrollposter så att du kan skydda serverdelen innan trafiken når ditt program. 

</br>På grund av prestanda, funktionalitet och säkerhetsfördelarna till HTTP-arbetsbelastningar med ytterdörren rekommenderar vi att kunder använder åtkomsten för sina HTTP-arbetsbelastningar.    Traffic Manager och ytterdörren kan användas parallellt för att hantera all trafik för ditt program. 

## <a name="building-with-azures-application-delivery-suite"></a>Att bygga med Azure application delivery suite 
Vi rekommenderar att alla webbplatser, API: er,-tjänster som är geografiskt redundant och leverera trafik till sina användare från den närmaste (lägsta svarstiden) plats till dem när det är möjligt.  Kombinera tjänster från Traffic Manager, ytterdörren Service, Application Gateway och belastningsutjämnare kan du skapa zonally och geografiskt redundant maximerar tillförlitlighet, skalbarhet och prestanda.

I diagrammet nedan beskriver vi en exempel-tjänst som använder en kombination av dessa tjänster för att skapa en global webbtjänst.   I det här fallet architect har använt Traffic Manager för att dirigera till globala serverdelar för fil- och leverans, när åtkomsten för att dirigera globalt URL-sökvägar som matchar mönstret/store / * till tjänsten som de har migrerats till App Service när routning alla andra begäranden till regionala Application Gateways.

I området för sina IaaS-tjänst programutvecklaren beslutat som de webbadresser som matchar mönstret/bilder / * hanteras från en dedikerad pool med virtuella datorer som skiljer sig från resten av gruppen.

Standard-VM-pool som betjänar dynamiskt innehåll måste dessutom prata med en backend-databas som finns på ett kluster med hög tillgänglighet. Hela distributionen har ställts in via Azure Resource Manager.

Följande diagram illustrerar arkitekturen i det här scenariot:

![Application Delivery Suite, detaljerad arkitektur][2] 

> [!NOTE]
> Det här exemplet är endast ett av många möjliga konfigurationerna belastningsutjämning tjänster som Azure erbjuder. Traffic Manager, ytterdörren, Application Gateway och Load Balancer kan blandas och matchas som bäst passar dina behov för belastningsutjämning. Om SSL-avlastning eller Layer 7-bearbetning behövs inte, till exempel användas belastningsutjämnare i stället för Application Gateway.


## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
