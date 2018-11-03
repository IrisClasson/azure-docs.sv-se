---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: 1807dc67d09b521e66314fb98535fb2c1225d34f
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964628"
---
I följande tabell visas begränsningar för olika tjänstnivåer (S1, S2, S3, F1). Information om kostnaden för varje *enhet* i respektive nivå finns [Prisinformation för IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resurs | S1 Standard | S2 Standard | S3 Standard | F1 Kostnadsfri |
| --- | --- | --- | --- | --- |
| Meddelanden per dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximalt antal enheter |200 |200 |10 |1 |

> [!NOTE]
> Kontakta Microsoft-supporten om du räknar med att använda fler än 200 enheter med en hubb på nivå S1 eller S2 eller 10 enheter med en hubb på nivå S3.
> 
> 

I följande tabell visas de begränsningar som gäller för IoT Hub-resurser:

| Resurs | Gräns |
| --- | --- |
| Maximalt antal betalda IoT-hubbar per Azure-prenumeration |50 |
| Maximalt antal kostnadsfria IoT-hubbar per Azure-prenumeration |1 |
| Högsta antalet tillåtna tecken i ett enhets-Id | 128 |
| Maximalt antal enhetsidentiteter<br/> som returneras i ett enskilt anrop |1000 |
| Maximal kvarhållning av IoT Hub-meddelanden för enhet-till-moln-meddelanden |7 dagar |
| Maximal storlek för enhet-till-moln-meddelande |256 kB |
| Maximal storlek för enhet-till-moln-batch |AMQP och http-: 256 KB för hela batchen <br/>MQTT: 256 KB för varje meddelande |
| Maximalt antal meddelanden för enhet-till-moln-batch |500 |
| Maximal storlek för moln-till-enhet-meddelande |64 kB |
| Maximalt TTL-värde för moln-till-enhet-meddelanden |2 dagar |
| Maximalt antal leveranser för moln-till-enhet- <br/> meddelanden |100 |
| Maximalt antal leveranser för feedbackmeddelanden <br/> som svar på ett moln-till-enhet-meddelande |100 |
| Maximalt TTL-värde för meddelanden som <br/> svar på ett moln-till-enhet-meddelande |2 dagar |
| Maximal storlek för enhetstvilling <br/> (taggar, rapporterade egenskaper och önskade egenskaper) | 8 kB |
| Maximal storlek för strängvärde för enhetstvilling | 4 KB |
| Maximalt djup för objekt i enhetstvilling | 5 |
| Maximal storlek på nyttolast för direkt metod | 128 KB |
| Maximal kvarhållning för jobbhistorik | 30 dagar |
| Maximalt antal samtidiga jobb | 10 (för S3), 5 (för S2), 1 (för S1) |
| Maximalt antal ytterligare slutpunkter | 10 (för S1, S2 och S3) |
| Maximalt antal regler för meddelandedirigering | 100 (för S1, S2 och S3) |


> [!NOTE]
> Om du behöver mer än 50 betalda IoT-hubbar i en Azure-prenumeration kan du kontakta Microsoft support.


> [!NOTE]
> Det maximala antalet enheter som du kan ansluta till en enda IoT-hubb är för närvarande 500 000. Om du vill utöka gränsen kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

Tjänsten IoT Hub begränsar begärandena om följande kvoter överskrids:

| Begränsning | Värde per hubb |
| --- | --- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, visa, uppdatera, ta bort), <br/> enskilda eller massimport/massexport |83.33/sec/Unit (5000 per minut per enhet) (för S3) <br/> 1.67/sec/Unit (100 per minut per enhet) (för S1 och S2). |
| Enhetsanslutningar |6 000 per sekund och enhet (för S3), 120 per sekund och enhet (för S2), 12 per sekund och enhet (för S1). <br/>Minst 100 per sekund. |
| Sändningar enhet-till-moln |6 000 per sekund och enhet (för S3), 120 per sekund och enhet (för S2), 12 per sekund och enhet (för S1). <br/>Minst 100 per sekund. |
| Sändningar moln-till-enhet | 83.33/sec/Unit (5000/minut/enhet) (för S3), 1.67/sec/unit (100 per minut per enhet) (för S1 och S2). |
| Mottagningar moln-till-enhet |833.33/sec/Unit (50000 per minut/enhet) (för S3), 16.67/sec/unit (1000 per minut per enhet) (för S1 och S2). |
| Filöverföringsåtgärder |83.33 filen ladda upp meddelanden per sekund och enhet (5000 per minut per enhet) (för S3), 1,67 filen ladda upp meddelanden/sekund och enhet (100 per minut per enhet) (för S1 och S2). <br/> 10 000 SAS URI:er kan vara ute för ett Azure Storage-konto i taget.<br/> 10 SAS URI:er/enheten kan vara ute vid ett och samma tillfälle. |
| Direkta metoder | 24MB per sekund och enhet (för S3), 480KB per sekund och enhet (för S2), 160KB per sekund och enhet (för S1)<br/> Baserat på 8KB begränsning mätaren storlek. |
| Läsoperationer för enhetstvilling | 50 per sekund och enhet (för S3), maximalt 10 per sekund eller 1 per sekund och enhet (för S2), 10 per sekund (för S1) |
| Uppdateringar för enhetstvilling | 50 per sekund och enhet (för S3), maximalt 10 per sekund eller 1 per sekund och enhet (för S2), 10 per sekund (för S1) |
| Jobbåtgärder <br/> (skapa, uppdatera, visa, ta bort) | 83.33/sec/Unit (5000/minut/enhet) (för S3), 1.67/sec/unit (100 per minut per enhet) (för S2), 1.67/sec/unit (100 per minut per enhet) (för S1) |
| Jobb per enhetsåtgärd, dataflöde | 50 per sekund och enhet (för S3), maximalt 10 per sekund eller 1 per sekund och enhet (för S2), 10 per sekund (för S1) |
