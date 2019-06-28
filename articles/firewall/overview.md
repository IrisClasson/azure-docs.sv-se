---
title: Vad är Azure Firewall?
description: Läs mer om Azure Firewall-funktioner.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 6/21/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 2567c47e41306a7940b6d065feb49ae80bb16198
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312690"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt administrerad brandvägg som en tjänst med inbyggd hög tillgänglighet och skalbarhet i obegränsad molnet.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

## <a name="features"></a>Funktioner

Azure Firewall erbjuder följande funktioner:

### <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet

Hög tillgänglighet är inbyggd i, så att det krävs några ytterligare belastningsutjämnare och du behöver konfigurera något.

### <a name="availability-zones-public-preview"></a>Tillgänglighetszoner (förhandsversion)

Azure-brandväggen kan konfigureras vid distribution till sträcker sig över flera Tillgänglighetszoner för ökad tillgänglighet. Med Availability Zones ökar tillgängligheten till din till 99,99% garanterad drifttid. Mer information finns i Azure-brandväggen [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% garanterad drifttid serviceavtal (SLA) erbjuds när två eller fler Tillgänglighetszoner har markerats.

Du kan även associera Azure-brandväggen till en viss zon för närhet orsaker, med hjälp av det tjänsten standard enligt serviceavtalet 99,95%.

Det finns ingen extra kostnad för en brandvägg som distribueras i en Tillgänglighetszon. Det finns dock ytterligare kostnader för överföring av inkommande och utgående data som är associerade med Tillgänglighetszoner. Mer information finns i [prisinformation för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Tillgänglighetszoner i Azure-brandväggen är tillgängliga i regioner som har stöd för Tillgänglighetszoner. Mer information finns i [vad är Tillgänglighetszoner i Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Availability Zones kan endast konfigureras under distributionen. Du kan inte konfigurera en befintlig Brandvägg för att inkludera Tillgänglighetszoner.

Läs mer om Tillgänglighetszoner [vad är Tillgänglighetszoner i Azure?](../availability-zones/az-overview.md)

### <a name="unrestricted-cloud-scalability"></a>Obegränsad molnskalbarhet

Azure Firewall kan skala upp så mycket du behöver för att hantera föränderliga nätverkstrafikflöden, så du behöver inte budgetera för hög trafikbelastning.

### <a name="application-fqdn-filtering-rules"></a>Programmets FQDN-filtreringsregler

Du kan begränsa utgående HTTP/S-trafik till en angiven lista med fullständigt kvalificerade domännamn (FQDN), inklusive jokertecken. Den här funktionen kräver inte SSL-avslutning.

### <a name="network-traffic-filtering-rules"></a>Regler för filtrering av nätverkstrafik

Du kan centralt skapa nätverksfiltreringsreglerna *tillåt* eller *neka* efter källans och målets IP-adress, port och protokoll. Azure Firewall är helt tillståndskänslig så att den kan identifiera legitima paket för olika typer av anslutningar. Regler tillämpas och loggas i flera prenumerationer och virtuella nätverk.

### <a name="fqdn-tags"></a>FQDN-taggar

FQDN-taggar gör det enkelt att tillåta välkänd Azure-tjänstnätverkstrafik via brandväggen. Anta exempelvis att du vill tillåta Windows Update-nätverkstrafik via brandväggen. Du skapar en programregel och inkluderar Windows Update-taggen. Nätverkstrafik från Windows Update kan nu flöda genom brandväggen.

### <a name="service-tags"></a>Tjänsttaggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar eller ange vilka IP-adresser som ingår i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

### <a name="threat-intelligence"></a>Hotinformation

Hotinformationsbaserad filtrering kan aktiveras för brandväggen för att avisera och avvisa trafik från/till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-feeden.

### <a name="outbound-snat-support"></a>Stöd för utgående SNAT

Alla IP-adresser för utgående trafik över virtuellt nätverk översätts till den offentliga Azure Firewall-IP-adressen (Source Network Address Translation). Du kan identifiera och tillåta trafik som kommer från ditt virtuella nätverk till fjärranslutna Internetmål. Azure-brandväggen inte SNAT när mål-IP är en privat IP-adressintervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Om din organisation använder en offentlig IP-adressintervall för privata nätverk, kommer Azure brandvägg SNAT trafik till en brandvägg privata IP-adresser i AzureFirewallSubnet.

### <a name="inbound-dnat-support"></a>Stöd för inkommande DNAT

Inkommande nätverkstrafik till din brandväggs offentliga IP-adress översätts (Destination Network Address Translation) och filtreras till de privata IP-adresserna på dina virtuella nätverk.

### <a name="multiple-public-ips-public-preview"></a>Flera offentliga IP-adresser (offentlig förhandsversion)

Du kan associera flera offentliga IP-adresser (upp till 600) med brandväggen.

På så sätt kan följande scenarier:

- **DNAT** – du kan översätta flera instanser av standard-port till backend-servrar. Du kan till exempel översätta TCP-port 3389 (RDP) för båda IP-adresser om du har två offentliga IP-adresser.
- **SNAT** -ytterligare portar som är tillgängliga för utgående SNAT-anslutningar, minskar risken för SNAT portöverbelastning. För tillfället väljer Azure brandvägg slumpmässigt offentliga IP-källadressen för en anslutning. Om du har någon underordnad filtrering i nätverket, måste du tillåta alla offentliga IP-adresser som är associerade med din brandvägg.

> [!NOTE]
> Den offentliga förhandsversionen, om du lägger till eller ta bort en offentlig IP-adress till en aktiv brandvägg kanske befintliga inkommande anslutningar med hjälp av DNAT regler inte fungerar i 40-120 sekunder. Du kan inte ta bort den första offentliga IP-adress som har tilldelats brandväggen, såvida inte brandväggen har frigjorts eller tagits bort.

### <a name="azure-monitor-logging"></a>Azure Monitor-loggning

Alla händelser är integrerade med Azure Monitor, vilket gör att du kan arkivera loggar till ett lagringskonto, strömma händelser till din händelsehubb eller skicka dem till Azure Monitor-loggar.

## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Konflikt med Azure Security Center (ASC) Just-in-Time-funktionen (JIT)|Om åtkomst till en virtuell dator sker via JIT, och den är i ett undernät med en användardefinierad väg som pekar på Azure Firewall som en standard-gateway, fungerar inte ASC JIT. Det här är ett resultat av asymmetrisk routning – ett paket kommer in via den virtuella datorn offentliga IP-Adressen (JIT öppnas åtkomst), men den returnera sökvägen är via brandväggen som släpper paketet eftersom det finns inga upprättad session i brandväggen.|Du kan kringgå problemet genom att placera de virtuella JIT-datorerna på ett separat undernät som inte har en användardefinierad väg till brandväggen.|
Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Vi utforskar alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverksregler.|Det är fortfarande möjligt att använda ICMP som ett protokoll via portalen och REST-API. Vi arbetar för att lägga till ICMP i PowerShell och CLI snart.|
|FQDN-taggar kräver att protokoll: port anges|Regler för program med FQDN taggar kräver port: protokoll-definition.|Du kan använda **https** som port: protokoll-värde. Vi arbetar för att göra det här fältet valfritt när FQDN taggar används.|
|Flyttning av en brandvägg till en annan resursgrupp eller prenumeration stöds inte|Flytta en brandvägg till en annan resursgrupp eller prenumeration stöds inte.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Portintervall i nätverk och regler|Portar som är begränsade till 64 000 eftersom höga portar är reserverade för hantering och diagnostiska sökningar. |Vi arbetar för att minska den här begränsningen.|
|Threat intelligence aviseringar kan hämta maskeras|Nätverksregler med mål 80/443 för utgående filtrering masker hot intelligence aviseringar när konfigurerad för att aviseringen läge.|Skapa utgående filtrering för 80/443 med hjälp av regler för program. Eller ändra threat intelligence läge till **Avisera och neka**.|
|Azure-brandväggen använder Azure DNS för att matcha|Azure-brandväggen löser FQDN: er endast med Azure DNS. En anpassad DNS-server stöds inte. Det finns ingen inverkan på DNS-matchning i andra undernät.|Vi arbetar för att minska den här begränsningen.|
|Azure brandväggen SNAT DNAT fungerar inte för privata IP-mål|Support för Azure brandväggen SNAT/DNAT är begränsad till Internet utgående/inkommande. SNAT/DNAT fungerar inte för närvarande för privata IP-mål. Till exempel eker att ekrar.|Det här är en aktuell begränsning.|
|Det går inte att ta bort första offentlig IP-adress|Du kan inte ta bort den första offentliga IP-adress som har tilldelats brandväggen, såvida inte brandväggen har frigjorts eller tagits bort.|Det här är avsiktligt.|
|Om du lägger till eller ta bort en offentlig IP-adress, fungerar inte DNAT reglerna tillfälligt.| Om du lägger till eller ta bort en offentlig IP-adress till en aktiv brandvägg fungerar inte befintliga inkommande anslutningar med hjälp av DNAT regler i 40-120 sekunder.|Det här är en begränsning i den offentliga förhandsversionen för den här funktionen.|
|Med Availability zones kan endast konfigureras under distributionen.|Med Availability zones kan endast konfigureras under distributionen. Du kan inte konfigurera Tillgänglighetszoner när en brandvägg som har distribuerats.|Det här är avsiktligt.|

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)
- [Distribuera Azure Firewall med hjälp av en mall](deploy-template.md)
- [Skapa en testmiljö för Azure Firewall](scripts/sample-create-firewall-test.md)
