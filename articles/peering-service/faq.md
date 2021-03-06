---
title: Vanliga frågor och svar om Azure-peering
description: Läs om vanliga frågor om Microsoft Azure peering-tjänsten
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872052"
---
# <a name="peering-service-faq"></a>Vanliga frågor och svar om peering-tjänsten

I den här artikeln förklaras de vanligaste frågorna om anslutningar till Azure peering-tjänsten.


**F. vilka är mål kunderna?**

A. Mål kunder är företag som ansluter till Microsoft Cloud via Internet som transport.

**F. kan kunder registrera sig för peering-tjänsten med flera leverantörer?** 

A. Ja, kunder kan registrera sig för peering-tjänsten med flera leverantörer i samma region eller olika regioner, men inte för samma prefix.

**F. kan kunder välja en unik Internet leverantör för sina webbplatser per geografisk region?**

A. Ja, kunderna kan göra det. Välj partner leverantören per region som passar dina verksamhets-och drift behov.

**F. Vad är en Microsoft Edge-PoP?**

A. Det är en fysisk plats där Microsoft samverkar med andra nätverk. På Microsoft Edge-PoP-platsen är tjänster som Azures front-dörr och Azure CDN värdbaserade. Mer information finns i [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features).

## <a name="peering-service-unique-characteristics"></a>Peering-tjänst: unika egenskaper

**F. Hur skiljer sig peering-tjänsten från normal Internet åtkomst?**

A. Partner som har registrerat sig för Microsoft-peering-tjänsten arbetar med Microsoft för att erbjuda optimerad Routning och tillförlitlig anslutning till Microsoft-tjänster.  

**F. Hur skiljer sig peering-tjänsten från ExpressRoute?**

A. Azure ExpressRoute är en privat, dedikerad anslutning från en eller flera kund platser. Medan peering-tjänsten erbjuder optimerad offentlig anslutning och inte stöder någon privat anslutning, erbjuder den även optimerad anslutning för lokala Internet-seminarier.

## <a name="next-steps"></a>Nästa steg

- Mer information om peering-tjänsten finns i [Översikt över peering-tjänsten](about.md).
- Information om hur du hittar en tjänst leverantör finns i [partners och platser för peering-tjänsten](location-partners.md).
- Information om hur du publicerar en peering service-anslutning finns i [onboarding peering service](onboarding-model.md).
- Information om hur du registrerar en peering service-anslutning finns i [Registrera en peering-tjänst anslutning-Azure Portal](azure-portal.md).
- För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).