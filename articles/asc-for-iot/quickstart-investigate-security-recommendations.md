---
title: 'Snabb start: Undersök säkerhets rekommendationer'
description: Undersök säkerhets rekommendationer med Azure Security Center för IoT Security Service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88144235"
---
# <a name="quickstart-investigate-security-recommendations"></a>Snabb start: Undersök säkerhets rekommendationer


Analys av analyser och förbättringar av rekommendationer av Azure Security Center för IoT är det bästa sättet att förbättra säkerheten position och minska angrepps ytan i IoT-lösningen.

I den här snabb starten ska vi utforska den information som är tillgänglig i varje IoT-säkerhetsrekommendation och förklara hur du kan öka detalj nivån och använda informationen för varje rekommendation och relaterade enheter för att minska risken.

Nu börjar vi.

## <a name="investigate-new-recommendations"></a>Undersök nya rekommendationer

I listan med IoT Hub rekommendationer visas alla sammanställda säkerhets rekommendationer för din IoT Hub.

1.  I Azure Portal öppnar du IoT Hub som **IoT Hub**   du vill undersöka för nya rekommendationer.

1.  Från menyn **säkerhet**   väljer du **rekommendationer**. Alla säkerhets rekommendationer för IoT Hub visas och rekommendationerna med en **ny**   flagga Markera rekommendationerna från de senaste 24 timmarna. 

    [![Undersök säkerhets rekommendationer med ASC för IoT](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  Välj och öppna alla rekommendationer i listan för att öppna rekommendations informationen och gå nedåt till de olika uppgifterna.

## <a name="security-recommendation-details"></a>Information om säkerhets rekommendationer

Öppna varje sammanställd rekommendation för att visa detaljerad rekommendations beskrivning, reparations steg, enhets-ID för varje enhet som utlöste en rekommendation. Den visar också rekommendations allvarlighets grad och direkt gransknings åtkomst med hjälp av Log Analytics.

1.  Välj och öppna säkerhets rekommendationer från listan **IoT Hub**   \>  **säkerhets**   \>  **rekommendationer**   .

1.  Läs rekommendations **beskrivningen**, **allvarlighets graden**, **enhets informationen**   för alla enheter som utfärdade den här rekommendationen under samlings perioden. 

1.  När du har granskat rekommendations information kan du använda **stegen för manuella åtgärder**   för att åtgärda problemet och lösa problemet som orsakade rekommendationen. 

    [![Åtgärda säkerhets rekommendationer med ASC för IoT](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  Utforska rekommendations informationen för en speciell enhet genom att välja önskad enhet på sidan öka detalj nivån.

    [![Undersök vissa säkerhets rekommendationer för en enhet med ASC för IoT](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  Om ytterligare undersökning krävs bör **du undersöka rekommendationen i Log Analytics**   med hjälp av länken. 


## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar anpassade aviseringar...

> [!div class="nextstepaction"]
> [Skapa anpassade aviseringar](quickstart-create-custom-alerts.md)
