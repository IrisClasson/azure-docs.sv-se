---
title: Översikt över Azures säkerhets grupper
titlesuffix: Azure Virtual Network
description: Lär dig om säkerhetsgrupper för nätverk och program. Med säkerhetsgrupper kan du filtrera nätverkstrafik mellan Azure-resurser.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: e73c4f0a658fb0f9c9abd854a8f91108a617a3d7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975412"
---
# <a name="security-groups"></a>Säkerhetsgrupper
<a name="network-security-groups"></a>

Du kan filtrera nätverkstrafik till och från Azure-resurser i ett [virtuellt nätverk](virtual-networks-overview.md) i Azure med en nätverkssäkerhetsgrupp. En nätverkssäkerhetsgrupp innehåller [säkerhetsregler](#security-rules) som tillåter eller nekar inkommande nätverkstrafik till, eller utgående nätverkstrafik från, flera typer av Azure-resurser. Information om vilka Azure-resurser du kan distribuera till ett virtuellt nätverk och associera med nätverkssäkerhetsgrupper finns i [Virtual network integration for Azure services](virtual-network-for-azure-services.md) (Integrering av virtuella nätverk för Azure-tjänster). För varje regel kan du ange källa och mål, port och protokoll.

Den här artikeln beskriver vad nätverkssäkerhetsgrupper är, så att du kan använda dem så effektivt som möjligt. Om du aldrig har skapat en nätverkssäkerhetsgrupp kan du gå en snabb [självstudie](tutorial-filter-network-traffic.md) för att få lite erfarenhet. Om du redan är bekant med nätverkssäkerhetsgrupper och vill lära dig hur du hanterar dem läser du [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md). Om du har kommunikationsproblem och behöver felsöka nätverkssäkerhetsgrupper läser du [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnostisera problem med filtreringen av nätverkstrafik för virtuella nätverk). Du kan aktivera [flödesloggar för nätverkssäkerhetsgrupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) om du vill [analysera nätverkstrafik](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) till och från resurser som har en associerad nätverkssäkerhetsgrupp.

## <a name="security-rules"></a>Säkerhetsregler

En nätverkssäkerhetsgrupp kan innehålla noll regler, eller så många regler du vill, inom Azure-prenumerationens [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Varje regel anger följande egenskaper:

|Egenskap  |Förklaring  |
|---------|---------|
|Namn|Ett unikt namn inom nätverkssäkerhetsgruppen.|
|Prioritet | Ett tal mellan 100 och 4096. Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.|
|Källa eller mål| Valfria, eller enskilda IP-adresser, CIDR-block (Classless Inter-Domain Routing) (t.ex. 10.0.0.0/24), [tjänsttaggar](service-tags-overview.md) eller [programsäkerhetsgrupper](#application-security-groups). Om du anger en adress för en Azure-resurs anger du den privata IP-adressen som tilldelats till resursen. Nätverkssäkerhetsgrupper bearbetas efter att Azure omvandlar en offentlig IP-adress till en privat IP-adress för inkommande trafik, och innan Azure omvandlar en privat IP-adress till en offentlig IP-adress för utgående trafik. Läs mer om [IP-adresser](virtual-network-ip-addresses-overview-arm.md) i Azure. Du kan begränsa antalet säkerhetsregler du skapar genom att ange ett intervall, en tjänsttagg eller en programsäkerhetsgrupp. Möjligheten att ange flera enskilda IP-adresser och intervall (du kan inte ange flera tjänsttaggar eller programgrupper) i en regel kallas [förhöjda säkerhetsregler](#augmented-security-rules). Förhöjda säkerhetsregler kan bara skapas i nätverkssäkerhetsgrupper som skapats genom Resource Manager-distributionsmodellen. Du kan inte ange flera IP-adresser och IP-adressintervall i nätverkssäkerhetsgrupper som skapats via den klassiska distributionsmodellen. Läs mer om [distributionsmodeller i Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protokoll     | TCP, UDP, ICMP eller valfritt.|
|Riktning| Om regeln gäller för inkommande eller utgående trafik.|
|Portintervall     |Du kan ange en enskild port eller ett portintervall. Du kan till exempel ange 80 eller 10000–10005. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. Förhöjda säkerhetsregler kan bara skapas i nätverkssäkerhetsgrupper som skapats genom Resource Manager-distributionsmodellen. Du kan inte ange flera portar eller portintervall i samma säkerhetsregel i nätverkssäkerhetsgrupper som skapats med den klassiska distributionsmodellen.   |
|Åtgärd     | Tillåt eller neka        |

Säkerhetsregler för nätverkssäkerhetsgrupper utvärderas baserat på prioritet med hjälp av 5-tuppelinformationen (källa, källport, mål, målport och protokoll) för att tillåta eller neka trafik. En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus. Flödesposten gör att en nätverkssäkerhetsgrupp kan vara tillståndskänslig. Om du till exempel anger en utgående säkerhetsregel till en adress via port 80, behöver du inte ange en inkommande säkerhetsregel för svar på utgående trafik. Du behöver bara ange en inkommande säkerhetsregel om kommunikationen initieras externt. Även det motsatta gäller. Om inkommande trafik tillåts via en port, behöver du inte ange en utgående säkerhetsregel för svar på trafik via porten.
Befintliga anslutningar kanske inte avbryts när du tar bort en säkerhetsregel som aktiverade flödet. Trafikflöden avbryts när anslutningar har stoppats och ingen trafik passerar i endera riktning under minst ett par minuter.

Det finns gränser för hur många säkerhetsregler du kan skapa i en nätverkssäkerhetsgrupp. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Förhöjda säkerhetsregler

Förhöjda säkerhetsregler förenklar säkerhetsdefinitionen för virtuella nätverk så att du kan definiera större och mer komplexa nätverkssäkerhetsprinciper med färre regler. Du kan kombinera flera portar och flera explicita IP-adresser och IP-intervall i en enda, lättbegriplig säkerhetsregel. Använd förhöjda regler i fälten för källa, mål och port för en regel. För att göra det enklare att underhålla definitionen av dina säkerhetsregler kan du kombinera förhöjda säkerhetsregler med [tjänsttaggar](service-tags-overview.md) eller [programsäkerhetsgrupper](#application-security-groups). Det finns begränsningar för antalet adresser, intervall och portar som du kan ange i en regel. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Tjänsttaggar

En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Det bidrar till att minimera komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler.

Mer information finns i avsnittet om [Azure Service-Taggar](service-tags-overview.md). 

## <a name="default-security-rules"></a>Standardsäkerhetsregler

Azure skapar följande standardregler i varje nätverkssäkerhetsgrupp som du skapar:

### <a name="inbound"></a>Inkommande

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Åtkomst|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Alla|Tillåt|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Åtkomst|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Alla|Tillåt|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioritet|Källa|Källportar|Mål|Målportar|Protokoll|Åtkomst|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Alla|Neka|

### <a name="outbound"></a>Utgående

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Alla | Tillåt |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Alla | Tillåt |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Alla | Neka |

I kolumnerna **Källa** och **Mål** är *VirtualNetwork*, *AzureLoadBalancer*, och *Internet* så kallade [tjänsttaggar](service-tags-overview.md), inte IP-adresser. I kolumnen protokoll finns **alla** kompasser TCP, UDP och ICMP. När du skapar en regel kan du ange TCP, UDP, ICMP eller valfri. *0.0.0.0/0* i kolumnerna **Källa** och **Mål** representerar alla adresser. Klienter som Azure Portal, Azure CLI eller PowerShell kan använda * eller något av detta uttryck.
 
Du kan inte ta bort standardreglerna, men du kan åsidosätta dem genom att skapa regler med högre prioritet.

## <a name="application-security-groups"></a>Programsäkerhetsgrupper

Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper. Du kan återanvända din säkerhetsprincip i stor skala utan manuellt underhåll av explicita IP-adresser. Plattformen hanterar komplexiteten med explicita IP-adresser och flera regeluppsättningar så att du kan fokusera på affärslogik. Följande exempel hjälper dig att bättre förstå programsäkerhetsgrupper:

![Programsäkerhetsgrupper](./media/security-groups/application-security-groups.png)

I föregående bild är *NIC1* och *NIC2* medlemmar i programsäkerhetsgruppen *AsgWeb*. *NIC3* är medlem i programsäkerhetsgruppen *AsgLogic*. *NIC4* är medlem i programsäkerhetsgruppen *AsgDb*. Även om varje nätverksgränssnitt i det här exemplet endast är medlem i en säkerhetsgrupp kan ett nätverksgränssnitt vara medlem i flera programsäkerhetsgrupper, upp till [gränserna i Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Inget av nätverksgränssnitten har en associerad nätverkssäkerhetsgrupp. *NSG1* är associerat med båda undernäten och innehåller följande regler:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Den här regeln krävs för att tillåta trafik från Internet till webbservrarna. Eftersom inkommande trafik från Internet nekas av standardsäkerhetsregeln [DenyAllInbound](#denyallinbound), krävs ingen ytterligare regel för programsäkerhetsgruppen *AsgLogic* eller *AsgDb*.

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Tillåt |

### <a name="deny-database-all"></a>Deny-Database-All

Eftersom standardsäkerhetsregeln [AllowVNetInBound](#allowvnetinbound) tillåter all kommunikation mellan resurser i samma virtuella nätverk, krävs den här regeln för att neka trafik från alla resurser.

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Alla | Neka |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Den här regeln tillåter trafik från programsäkerhetsgruppen *AsgLogic* till programsäkerhetsgruppen *AsgDb*. Den här regeln har högre prioritet än regeln *Deny-Database-All*. Det innebär att den här regeln bearbetas före regeln *Deny-Database-All*, så att trafik från programsäkerhetsgruppen *AsgLogic* tillåts, medan all annan trafik blockeras.

|Prioritet|Källa|Källportar| Mål | Målportar | Protokoll | Åtkomst |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Tillåt |

Reglerna som definierar en programsäkerhetsgrupp som källan eller målet tillämpas bara på nätverksgränssnitt som är medlemmar i programsäkerhetsgruppen. Om nätverksgränssnittet inte är medlem i en programsäkerhetsgrupp tillämpas inte regeln på nätverksgränssnittet, även om nätverkssäkerhetsgruppen är associerad med undernätet.

Programmet säkerhetsgrupper har följande begränsningar:

-   Det finns gränser för hur många programsäkerhetsgrupper du kan ha i en prenumeration, liksom andra begränsningar som gäller för programsäkerhetsgrupper. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Du kan ange en programsäkerhetsgrupp som källa och mål i en säkerhetsregel. Du kan inte ange flera programsäkerhetsgrupper i källan eller målet.
- Alla nätverksgränssnitt som har tilldelats till en programsäkerhetsgrupp måste finnas i samma virtuella nätverk som det första nätverksgränssnittet som programsäkerhetsgruppen finns i. Om exempelvis det första nätverksgränssnittet som tilldelats till en programsäkerhetsgrupp som heter *AsgWeb* finns i det virtuella nätverket med namnet *VNet1*, måste alla efterföljande nätverksgränssnitt som tilldelats *AsgWeb* finnas i *VNet1*. Du kan inte lägga till nätverksgränssnitt från olika virtuella nätverk i säkerhetsgruppen för samma program.
- Om du anger en programsäkerhetsgrupp som källa och mål i en säkerhetsregel, måste nätverksgränssnitten i bägge programsäkerhetsgrupperna finnas i samma virtuella nätverk. Om till exempel *AsgLogic* innehåller nätverksgränssnitt från *VNet1*, och *AsgDb* innehåller nätverksgränssnitt från *VNet2*, kan du inte tilldela *AsgLogic* som källan och *AsgDb* som målet i en regel. Alla nätverksgränssnitt för både käll- och målprogramsäkerhetsgrupperna måste finnas i samma virtuella nätverk.

> [!TIP]
> För att minimera antalet säkerhetsregler, och behovet av att behöva ändra reglerna, bör du tänka igenom vilka programsäkerhetsgrupper du behöver och sedan skapa regler med hjälp av tjänsttaggar eller programsäkerhetsgrupper, i stället för att använda enskilda IP-adresser eller IP-adressintervall.

## <a name="how-traffic-is-evaluated"></a>Så här utvärderas trafik

Du kan distribuera resurser från flera Azure-tjänster till ett virtuellt Azure-nätverk. En fullständig lista finns i [Tjänster som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Du kan associera en, eller ingen, nätverkssäkerhetsgrupp med varje [undernät](virtual-network-manage-subnet.md#change-subnet-settings) och [nätverksgränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) i ett virtuellt nätverk på en virtuell dator. Du kan associera samma nätverkssäkerhetsgrupp med så många undernät och nätverksgränssnitt du vill.

Scenarierna i följande bild visar hur du kan distribuera nätverkssäkerhetsgrupper för att tillåta nätverkstrafik till och från Internet via TCP-port 80:

![NSG-bearbetning](./media/security-groups/nsg-interaction.png)

Bilden ovan, och texten nedan, beskriver hur Azure bearbetar inkommande och utgående regler för nätverkssäkerhetsgrupper:

### <a name="inbound-traffic"></a>Inkommande trafik

För inkommande trafik bearbetar Azure först reglerna i en nätverkssäkerhetsgrupp som är associerad med ett undernät, om ett sådant finns. Därefter bearbetas reglerna i en nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet, om ett sådant finns.

- **VM1**: Säkerhetsreglerna i *NSG1* bearbetas eftersom nätverkssäkerhetsgruppen är associerad med *Subnet1* och *VM1* finns i *Subnet1*. Såvida du inte har skapat en regel som tillåter inkommande trafik på port 80, nekas trafiken av standardsäkerhetsregeln [DenyAllInbound](#denyallinbound) och utvärderas aldrig av *NSG2* eftersom *NSG2* är associerad med nätverksgränssnittet. Om *NSG1* har en säkerhetsregel som tillåter trafik på port 80, bearbetas trafiken av *NSG2*. För att trafik på port 80 ska tillåtas på den virtuella datorn måste både *NSG1* och *NSG2* ha en regel som tillåter trafik på port 80 från Internet.
- **VM2**: Reglerna i *NSG1* bearbetas eftersom *VM2* också finns i *Subnet1*. Eftersom ingen nätverkssäkerhetsgrupp är associerad med nätverksgränssnittet för *VM2*, tar den emot all trafik som tillåts via *NSG1* eller nekas all trafik som nekas av *NSG1*. Trafik antingen tillåts eller nekas till alla resurser i samma undernät när en nätverkssäkerhetsgrupp är associerad med ett undernät.
- **VM3**: Eftersom ingen nätverkssäkerhetsgrupp är associerad med *Subnet2*, tillåts trafik till undernätet och bearbetas av *NSG2* eftersom *NSG2* är associerad med nätverksgränssnittet som är kopplat till *VM3*.
- **VM4**: Trafik tillåts till *VM4* eftersom ingen nätverkssäkerhetsgrupp är associerad med *Subnet3* eller med nätverksgränssnittet på den virtuella datorn. All nätverkstrafik tillåts genom ett undernät eller nätverksgränssnitt om ingen nätverkssäkerhetsgrupp är associerad med undernätet eller nätverksgränssnittet.

### <a name="outbound-traffic"></a>Utgående trafik

För utgående trafik bearbetar Azure först reglerna i en nätverkssäkerhetsgrupp som är associerad med ett nätverksgränssnitt, om ett sådant finns. Därefter bearbetas reglerna i en nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet, om ett sådant finns.

- **VM1**: Säkerhetsreglerna i *NSG2* bearbetas. Såvida du inte skapar en säkerhetsregel som nekar utgående trafik till Internet via port 80, tillåts trafiken av standardsäkerhetsregeln [AllowInternetOutbound](#allowinternetoutbound) i både *NSG1* och *NSG2*. Om *NSG2* har en säkerhetsregel som nekar port 80, nekas trafiken och utvärderas aldrig av *NSG1*. För att trafik på port 80 ska nekas från den virtuella datorn måste den ena eller båda nätverkssäkerhetsgrupperna ha en regel som nekar trafik på port 80 till Internet.
- **VM2**: All trafik skickas via nätverksgränssnittet till undernätet eftersom nätverksgränssnittet som är kopplat till *VM2* inte har någon associerad nätverkssäkerhetsgrupp. Reglerna i *NSG1* bearbetas.
- **VM3**: Om *NSG2* har en säkerhetsregel som nekar trafik på port 80, nekas trafiken. Om *NSG2* har en säkerhetsregel som tillåter trafik på port 80, tillåts utgående trafik till Internet via port 80 eftersom ingen nätverkssäkerhetsgrupp är associerad med *Subnet2*.
- **VM4**: All nätverkstrafik tillåts från *VM4* eftersom ingen nätverkssäkerhetsgrupp är associerad med nätverksgränssnittet som är kopplat till den virtuella datorn eller till *Subnet3*.


### <a name="intra-subnet-traffic"></a>Trafik inom under nätet

Det är viktigt att Observera att säkerhets regler i en NSG som är kopplade till ett undernät kan påverka anslutningen mellan den virtuella datorn i det. Om en regel till exempel läggs till i *NSG1* som nekar all inkommande och utgående trafik, kommer *VM1* och *VM2* inte längre att kunna kommunicera med varandra. En annan regel skulle behöva läggas till specifikt för att tillåta detta. 



Du kan enkelt granska vilka regler som tillämpas för ett nätverksgränssnitt genom att visa [gällande säkerhetsregler](virtual-network-network-interface.md#view-effective-security-rules) för ett nätverksgränssnitt. Du kan också använda funktionen [Kontrollera IP-flöde](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure Network Watcher för att ta reda på om kommunikation tillåts till eller från ett nätverksgränssnitt. Funktionen Kontrollera IP-flöde anger om kommunikation tillåts eller nekas och vilken nätverkssäkerhetsregel som tillåter eller nekar trafik.

> [!NOTE]
> Nätverks säkerhets grupper är kopplade till undernät eller virtuella datorer och moln tjänster som distribueras i den klassiska distributions modellen och till undernät eller nätverks gränssnitt i distributions modellen för Resource Manager. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Såvida du inte har en särskild anledning att inte göra det, rekommenderar vi att du associerar en nätverkssäkerhetsgrupp med ett undernät, eller med ett nätverksgränssnitt, men inte med båda. Eftersom regler i en nätverkssäkerhetsgrupp som är associerad med ett undernät kan stå i konflikt med regler i en nätverkssäkerhetsgrupp som är associerad med ett nätverksgränssnitt, kan det uppstå oväntade kommunikationsproblem som kräver felsökning.

## <a name="azure-platform-considerations"></a>Azure-plattformsöverväganden

- Den **virtuella IP-adressen för noden värd**: grundläggande infrastruktur tjänster som DHCP, DNS, IMDS och hälso övervakning tillhandahålls via de virtualiserade värd-IP-adresserna 168.63.129.16 och 169.254.169.254. De här IP-adresserna tillhör Microsoft och är de enda virtualiserade IP-adresserna som används i alla regioner för det här ändamålet.
- **Licensiering (nyckelhanteringstjänsten):** Windows-avbildningar som kör på de virtuella datorerna ska vara licensierade. Licensieringen kontrolleras genom att en begäran skickas till nyckelhanteringstjänstens värdservrar som hanterar sådana frågor. Begäran är en utgående begäran via port 1688. För distributioner som använder konfigurationer med [standardflödet 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), inaktiveras denna plattformsregel.
- **Virtuella datorer i lastbalanserade pooler**: Källporten och adressintervallet som används kommer från den ursprungliga datorn, inte lastbalanseraren. Målporten och måladressutrymmet kommer från måldatorn, inte lastbalanseraren.
- **Azure-tjänstinstanser**: Instanser av flera Azure-tjänster, till exempel HDInsight, tillämpningstjänstmiljöer och VM-skalningsuppsättningar distribueras i undernät för virtuella nätverk. En fullständig lista över tjänster som du kan distribuera till virtuella nätverk finns i [Virtuellt nätverk för Azure-tjänster](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ta reda på portkraven för varje tjänst innan du tillämpar en nätverkssäkerhetsgrupp för det undernät som resursen är distribuerad i. Om du nekar åtkomst till portar som krävs för tjänsten kommer tjänsten inte att fungera korrekt.
- **Skicka utgående e-post**: Microsoft rekommenderar att du använder autentiserade SMTP-relätjänster (ansluts vanligtvis, men inte alltid, via TCP-port 587) för att skicka e-post från Azure Virtual Machines. SMTP-relätjänsterna är specialiserade på avsändaromdöme för att minska möjligheten att externa e-postleverantörer avvisar meddelanden. SMTP-relätjänsterna omfattar, men är inte begränsade till, Exchange Online Protection och SendGrid. Användningen av SMTP-relätjänster är inte begränsad i Azure, oavsett vilken typ av prenumeration du har. 

  Om du har skapat din Azure-prenumeration före 15 november 2017 kan du, förutom att använda SMTP-relätjänster, även skicka e-post direkt via TCP-port 25. Om du har skapat din prenumeration efter 15 november 2017 kan du inte skicka e-post direkt via port 25. Beteendet för utgående kommunikation via port 25 beror på vilken typ av prenumeration du har:

     - **Enterprise-avtal**: Utgående kommunikation via port 25 tillåts. Du kan skicka utgående e-post direkt från virtuella datorer till externa e-postleverantörer utan begränsningar från Azure-plattformen. 
     - **Betala per användning:** Utgående kommunikation via port 25 blockeras från alla resurser. Om du behöver skicka e-post från en virtuell dator direkt till externa e-postleverantörer (inte använda ett autentiserat SMTP-relä) kan du skicka en begäran om att ta bort begränsningen. Förfrågningarna granskas och godkänns enligt Microsofts gottfinnande och beviljas endast efter att bedrägerikontroller utförts. Om du vill skicka en förfrågan öppnar du ett supportärende med ärendetypen *Teknisk*, *Virtuell nätverksanslutning*, *Det går inte att skicka e-post (SMTP/Port 25)* . I ditt supportärende anger du information om varför du (din prenumeration) behöver skicka e-post direkt till e-postleverantörer i stället för att gå via ett autentiserat SMTP-relä. Om din prenumeration undantas kan endast virtuella datorer som skapats efter undantagsdatumet använda utgående kommunikation via port 25.
     - **MSDN, Azure-pass, Azure i Open, Education, BizSpark och kostnadsfri utvärderingsversion**: Utgående kommunikation via port 25 blockeras från alla resurser. Det går inte att skicka förfrågningar om att ta bort begränsningen eftersom dessa inte beviljas. Om du vill skicka e-post från din virtuella dator måste du använda en SMTP-relätjänst.
     - **Molntjänstleverantör**: Kunder som förbrukar Azure-resurser via en molntjänstleverantör kan skapa ett supportärende hos molntjänstleverantören och begära ett avblockeringsärende om det inte går att använda ett säkert SMTP-relä.

  Även om Azure tillåter att du skickar e-post via port 25 kan Microsoft inte garantera att e-postleverantörerna godkänner inkommande e-post från din virtuella dator. Om en viss leverantör avvisar e-post från din virtuella dator kontaktar du leverantören för att lösa eventuella problem med meddelandeleverans eller skräppostfiltrering. Alternativt använder du en autentiserad SMTP-relätjänst.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md).
