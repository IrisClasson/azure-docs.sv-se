---
title: Integrera Microsoft Azure med Oracle Cloud Infrastructure | Microsoft Docs
description: Lär dig mer om lösningar som integrerar Oracle-appar som körs på Microsoft Azure med databaser i Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: rgardler
manager: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: rogardle
ms.custom: ''
ms.openlocfilehash: d93446f4db914c736235daeb4e08e82b9ff00e62
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224510"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Oracle-programlösningar som integrerar Microsoft Azure och Oracle Cloud Infrastructure

Microsoft och Oracle har samarbetat för att tillhandahålla låg latens, hög genom strömning mellan moln anslutningar, så att du kan dra nytta av det bästa av båda molnen. 

Med den här anslutningen över molnet kan du partitionera ett program på flera nivåer för att köra databas nivån på en Oracle Cloud Infrastructure (OCI) och programmet och andra nivåer på Microsoft Azure. Upplevelsen liknar att köra hela lösnings stacken i ett enda moln. 

Om du är intresse rad av att köra mellanprogram, inklusive WebLogic-Server, på Azure-infrastruktur, men har Oracle-databasen som körs i OCI, se [WebLogic Server Azure-program](oracle-weblogic.md).

Om du är intresse rad av att distribuera Oracle-lösningar helt och hållet i Azure-infrastrukturen, se [Oracle VM-avbildningar och deras distribution på Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Scenarioöversikt

Anslutning mellan moln är en lösning som gör att du kan köra Oracles branschledande program och dina egna anpassade program på Azure Virtual Machines samtidigt som du får fördelarna med värdbaserade databas tjänster i OCI. 

Från och med maj 2020 certifieras följande program i en konfiguration mellan moln:

* E-Business Suite
* JD Edwards-EnterpriseOne
* PeopleSoft
* Oracle återförsäljarversion-program
* Ekonomisk hantering för Oracle Hyperion

Följande diagram är en övergripande översikt över den anslutna lösningen. För enkelhetens skull visar diagrammet bara en program nivå och en data nivå. Beroende på program arkitekturen kan din lösning innehålla ytterligare nivåer, till exempel ett WebLogic-Server kluster eller en webb nivå i Azure. Mer information finns i följande avsnitt.

![Översikt över Azure OCI-lösning](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Tillgänglighet för regioner 

Anslutning mellan moln är begränsad till följande regioner:
* USA, östra USA (östra USA) & OCI-Ashburn, VA (östra USA)
* Azure Storbritannien, södra (UKSouth) & OCI London (Storbritannien, södra)
* Azure Canada Central (Indiensödra) & OCI Toronto (Kanada, sydöstra)
* Azure Västeuropa (WestEurope) & OCI Amsterdam (Nederländerna Northwest)
* Azure Japan, östra (Japanöstra) & OCI Tokyo (Östra Japan)

## <a name="networking"></a>Nätverk

Företags kunder väljer ofta att diversifiera och distribuera arbets belastningar över flera moln för olika verksamhets-och drifts skäl. För att kunna diversifiera varandra kan kunder sammanställa moln nätverk med Internet, IPSec VPN eller med hjälp av moln leverantörens direkta anslutnings lösning via det lokala nätverket. Att koppla samman moln nätverk kan kräva betydande investeringar i tid, pengar, utformning, tillvaratagande, installation, testning och drift. 

För att lösa dessa kund utmaningar har Oracle och Microsoft aktiverat en integrerad miljö med flera moln. Nätverk över molnet upprättas genom att ansluta en [ExpressRoute](../../../expressroute/expressroute-introduction.md) -krets i Microsoft Azure med en [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) -krets i OCI. Den här anslutningen är möjlig när en peering-plats i Azure ExpressRoute är i närheten av eller på samma peering-plats som OCI-FastConnect. Den här installationen möjliggör säker och snabb anslutning mellan de två molnen utan behov av en mellanliggande tjänst leverantör.

Med hjälp av ExpressRoute och FastConnect kan kunder peer-koppla ett virtuellt nätverk i Azure med ett virtuellt moln nätverk i OCI, förutsatt att det privata IP-adressutrymmet inte överlappar varandra. Genom att peer-koppla de två nätverken kan en resurs i det virtuella nätverket kommunicera med en resurs i det virtuella OCI-nätverket som om båda finns i samma nätverk.

## <a name="network-security"></a>Nätverkssäkerhet

Nätverks säkerhet är en viktig komponent i alla företags program och är central för den här lösningen med flera moln. All trafik som går över ExpressRoute och FastConnect passerar över ett privat nätverk. Den här konfigurationen möjliggör säker kommunikation mellan ett virtuellt Azure-nätverk och ett virtuellt nätverk i Oracle. Du behöver inte ange någon offentlig IP-adress för virtuella datorer i Azure. På samma sätt behöver du inte en Internet-gateway i OCI. All kommunikation sker via datorernas privata IP-adress.

Dessutom kan du konfigurera [säkerhets listor](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) i ditt OCI-nätverk och säkerhets regler för din OCI (anslutna till Azures [nätverks säkerhets grupper](../../../virtual-network/security-overview.md)). Använd de här reglerna för att styra trafiken som flödar mellan datorer i de virtuella nätverken. Nätverks säkerhets regler kan läggas till på en dator nivå på en under näts nivå, samt på nivån för det virtuella nätverket.

[WebLogic-serverns Azure-program](oracle-weblogic.md) skapar en nätverks säkerhets grupp som förkonfigureras för att fungera med WebLogic-serverns port konfiguration.
 
## <a name="identity"></a>Identitet

Identiteten är en av kärn pelaren i partnerskapet mellan Microsoft och Oracle. Betydande arbete har gjorts för att integrera [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) med [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst. Användarna kan logga in och komma åt olika resurser med hjälp av Azure AD. Med Azure AD kan du också hantera dina användare och deras behörigheter.

Med den här integreringen kan du för närvarande hantera på en central plats, som är Azure Active Directory. Azure AD synkroniserar eventuella ändringar i katalogen med motsvarande Oracle-katalog och används för enkel inloggning till globala Oracle-lösningar i flera moln.

## <a name="next-steps"></a>Nästa steg

Kom igång med ett [nätverk över molnet](configure-azure-oci-networking.md) mellan Azure och OCI. 

Mer information och dokumentation om OCI finns i dokumentationen till [Oracle-molnet](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
