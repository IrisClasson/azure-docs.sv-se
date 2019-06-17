---
title: Förstå Azure Security Center för IoT-lösningsarkitektur förhandsversion | Microsoft Docs
description: Läs mer om flödet av information i Azure Security Center för IoT-tjänsten.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 3c775c8cb99b056c7f468c028a80dd0b27ae6438
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200683"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center för IoT-arkitekturen

Den här artikeln beskriver funktionella systemarkitekturen av Azure Security Center (ASC) för IoT-lösning. 

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC för IoT-komponenter

ASC för IoT består av följande komponenter:
- Enhet-agenter
- Skicka säkerhetsmeddelande SDK
- IoT Hub-integrering
- Analyspipeline
 
### <a name="asc-for-iot-workflow"></a>ASC för IoT-arbetsflöde

ASC för agenter för IoT-enhet kan du enkelt kan samla in råa säkerhetshändelser från dina enheter. Rå säkerhetshändelser kan inkludera IP-anslutningar, processgenerering, användarinloggningar och annan säkerhetsrelevanta information. ASC för IoT-enhet agenter hanterar även händelse aggregering för att undvika hög nätverkets genomflöde. Agenterna är mycket anpassningsbara, vilket gör att du kan använda dem för specifika uppgifter som att skicka endast viktig information på det snabbaste serviceavtalet, eller för att sammanställa omfattande säkerhetsinformation och kontext i större segment undvika högre Tjänstekostnader för.
 
Enheten agenter och andra program använder den **Azure ASC skicka säkerhetsmeddelande SDK** att skicka säkerhetsinformation om i Azure IoT Hub. IoT Hub tar upp den här informationen och vidarebefordrar den till ASC för IoT-tjänsten.

När ASC för IoT-tjänsten är aktiverad, förutom vidarebefordrade data, IoT Hub skickar också ut alla interna data för analys av ASC för IoT. Dessa data innehåller enheten molnet åtgärdsloggar, enhetsidentiteter och Hub konfiguration. All denna information hjälper dig för att skapa ASC för IoT-analyspipeline.
 
ASC för IoT-analyspipeline tar också emot ytterligare threat intelligence dataströmmar från olika källor på Microsoft och Microsofts partner. ASC för hela IoT-analyspipeline fungerar med varje kund-konfigurationen som utförts på tjänsten (t.ex anpassade varningar och användning av skicka säkerhetsmeddelande SDK).
 
Med analytics-pipelinen kombinerar ASC för IoT alla strömmar av information för att skapa användbara rekommendationer och aviseringar. Pipelinen innehåller både anpassade regler som skapats av säkerhetsanalytiker och experter samt maskininlärningsmodeller som söker efter avvikelser från standard-enhet beteende och risk analys.
 
ASC för IoT-rekommendationer och aviseringar (analytics pipeline utdata) skrivs till Log Analytics-arbetsyta för varje kund. Inklusive rådatahändelser i arbetsytan som aviseringar och rekommendationer kan djupdykning undersökningar och frågor med information om misstänkta aktiviteter som identifieras.  

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om grundläggande arkitektur och arbetsflöde för ASC för IoT-lösning. Mer information om förutsättningar, hur du kommer igång och aktivera din säkerhetslösning i IoT Hub finns i följande artiklar:

- [Krav för tjänst](service-prerequisites.md)
- [Komma igång](getting-started.md)
- [Konfigurera din lösning](quickstart-configure-your-solution.md)
- [Aktivera säkerhet i IoT Hub](quickstart-onboard-iot-hub.md)
- [ASC för IoT vanliga frågor och svar](resources-frequently-asked-questions.md)
- [ASC för IoT-säkerhetsvarningar](concept-security-alerts.md)

