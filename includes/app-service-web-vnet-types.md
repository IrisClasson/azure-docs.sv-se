---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81312850"
---
* De flera klient system som har stöd för alla pris avtal förutom isolerade.
* App Service-miljön, som distribueras till ditt VNet och stöder isolerade program för prissättnings planer.

Funktionen VNet-integrering används i appar för flera klient organisationer. Om din app är i [App Service-miljön][ASEintro]är den redan i ett VNet och kräver inte att funktionen VNet-integrering används för att komma åt resurser i samma VNet. Mer information om alla nätverksfunktioner finns i [App Service nätverksfunktioner][Networkingfeatures].

VNet-integrering ger din app åtkomst till resurser i ditt VNet, men den beviljar inte inkommande privat åtkomst till din app från det virtuella nätverket. Åtkomst till privata webbplatser syftar bara på att göra en app tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering används bara för att göra utgående samtal från din app till ditt VNet. Funktionen för VNet-integrering fungerar annorlunda när den används med VNet i samma region och med VNet i andra regioner. Funktionen för VNet-integrering har två varianter:

* **Regional VNet-integrering**: när du ansluter till Azure Resource Manager virtuella nätverk i samma region måste du ha ett dedikerat undernät i det virtuella nätverk som du integrerar med.
* **Gateway-nödvändig VNet-integrering**: när du ansluter till VNet i andra regioner eller till ett klassiskt virtuellt nätverk i samma region behöver du en Azure Virtual Network Gateway etablerad i det virtuella mål nätverket.

Funktionerna för VNet-integrering:

* Kräv en pris plan för standard, Premium, PremiumV2 eller elastisk Premium.
* Stöd för TCP och UDP.
* Arbeta med Azure App Service appar och Function-appar.

Det finns vissa saker som VNet-integrering inte stöder, t. ex.:

* Montera en enhet.
* Active Directory-integrering.
* NetBIOS.

Gateway-nödvändig VNet-integrering ger endast åtkomst till resurser i målets virtuella nätverk eller i nätverk som är anslutna till målets virtuella nätverk med peering eller VPN. Gateway-nödvändig VNet-integrering ger inte åtkomst till resurser som är tillgängliga i Azure ExpressRoute-anslutningar eller fungerar med tjänst slut punkter.

Oavsett vilken version som används ger VNet-integration appen åtkomst till resurser i ditt VNet, men den beviljar inte inkommande privat åtkomst till din app från det virtuella nätverket. Åtkomst till privata webbplatser syftar bara på att göra appen tillgänglig från ett privat nätverk, t. ex. inifrån ett Azure VNet. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
