---
title: Containersäkerhet
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skyddar din behållare
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fd2a6cdad01302501e30ec60a4d3ccf6efd9c266
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876848"
---
## <a name="azure-cognitive-services-container-security"></a>Säkerhet för Azure Cognitive Services-behållare

Säkerheten bör vara primärt fokuserad när du utvecklar program. Vikten av säkerhet är ett mått för framgång. När du skapar en program varu lösning som innehåller Cognitive Services behållare är det viktigt att du förstår de begränsningar och möjligheter som är tillgängliga för dig. Mer information om nätverks säkerhet finns i [Konfigurera Azure Cognitive Services virtuella nätverk][az-security].

> [!IMPORTANT]
> Som standard finns det *Ingen säkerhet* för API: et för Cognitive Services container. Orsaken till detta är att behållaren ofta körs som en del av en pod som skyddas från utsidan av en nätverks brygga. Det är dock möjligt att aktivera autentisering som fungerar identiskt med autentiseringen som används vid åtkomst till den [molnbaserade Cognitive Services][request-authentication].

Diagrammet nedan illustrerar **standard och osäker** Metod:

![Containersäkerhet](../media/container-security.svg)

Som ett alternativ och *säkert* tillvägagångs sätt kan förbrukare av Cognitive Services behållare utöka en behållare med en klient som är klient del, så att behållar slut punkten är privat. Vi ska ta en titt på ett scenario där vi använder [Istio][istio] som en ingress-Gateway. Istio stöder HTTPS/TLS och autentisering av klient certifikat. I det här scenariot exponeras behållar åtkomsten i Istio-frontend-filen, som presenterar klient certifikatet som vit listas i förväg med Istio.

[Nginx][nginx] är ett annat populärt val i samma kategori. Både Istio och Nginx fungerar som ett tjänst nät och erbjuder ytterligare funktioner, inklusive t. ex. belastnings utjämning, Routning och Rate-kontroll.

### <a name="container-networking"></a>Nätverk för containrar

Cognitive Services behållare krävs för att skicka in avläsnings information för fakturerings syfte. Det enda undantaget är *offline-behållare* när de följer en annan fakturerings metod. Det gick inte att tillåta en lista över olika nätverks kanaler som Cognitive Services behållare förlitar sig på förhindrar att behållaren fungerar.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Lista över tillåtna Cognitive Services domäner och portar

Värden ska tillåta List **port 443** och följande domäner:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Inaktivera djup paket granskning

> [Djup paket inspektion](https://en.wikipedia.org/wiki/Deep_packet_inspection) (dpi) är en typ av data bearbetning som identifierar data som skickas över ett dator nätverk, och som vanligt vis vidtar åtgärder genom att blockera, dirigera om eller logga i enlighet med detta.

Inaktivera DPI på de säkra kanaler som Cognitive Services behållare skapar till Microsoft-servrar. Om du inte gör det kommer behållaren inte att fungera korrekt.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
