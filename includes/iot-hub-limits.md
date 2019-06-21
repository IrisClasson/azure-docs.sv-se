---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 104849557a8580e16fa1860b7919d1c0252debe9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187738"
---
I följande tabell visas de begränsningar som är associerade med de olika nivåerna S1, S2, S3 och F1. Information om kostnaden för var och en *enhet* i respektive nivå finns [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Kostnadsfri |
| --- | --- | --- | --- | --- |
| Meddelanden per dag |400,000 |6,000,000 |300,000,000 |8,000 |
| Maximalt antal enheter |200 |200 |10 |1 |

> [!NOTE]
> Om du räknar med att använda fler än 200 enheter med en hubb på nivå S1 eller S2 eller 10 enheter med en hubb på nivå S3, kontaktar du Microsoft Support.
> 
> 

I följande tabell visas de begränsningar som gäller för IoT Hub-resurser.

| Resource | Gräns |
| --- | --- |
| Maximalt antal betalda IoT-hubbar per Azure-prenumeration |50 |
| Maximalt antal kostnadsfria IoT-hubbar per Azure-prenumeration |1 |
| Högsta antalet tillåtna tecken i ett enhets-ID | 128 |
| Maximalt antal enhetsidentiteter<br/> som returneras i ett enskilt anrop |1,000 |
| Maximal kvarhållning av IoT Hub-meddelanden för enhet-till-moln-meddelanden |7 dagar |
| Maximal storlek för enhet-till-moln-meddelande |256 kB |
| Maximal storlek för enhet-till-moln-batch |AMQP och HTTP: 256 KB för hela batchen <br/>MQTT: 256 KB för varje meddelande |
| Maximalt antal meddelanden för enhet-till-moln-batch |500 |
| Maximal storlek för moln-till-enhet-meddelande |64 kB |
| Maximalt TTL-värde för moln-till-enhet-meddelanden |2 dagar |
| Maximalt antal leveranser för moln-till-enhet- <br/> meddelanden |100 |
| Maximalt antal leveranser för feedbackmeddelanden <br/> som svar på ett moln-till-enhet-meddelande |100 |
| Maximalt TTL-värde för meddelanden som <br/> svar på ett moln-till-enhet-meddelande |2 dagar |
| [Maximal storlek för enhetstvilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (taggar, rapporterade egenskaper och önskade egenskaper) | 8 kB |
| Maximal storlek för strängvärde för enhetstvilling | 4 KB |
| [Maximalt djup för objekt i enhetstvilling](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Maximal storlek på nyttolast för direkt metod | 128 KB |
| Maximal kvarhållning för jobbhistorik | 30 dagar |
| Maximalt antal samtidiga jobb | 10 (för S3), 5 (för S2), 1 (för S1) |
| Maximalt antal ytterligare slutpunkter | 10 (för S1, S2 och S3) |
| Maximalt antal regler för meddelandedirigering | 100 (för S1, S2 och S3) |
| Maximalt antal samtidigt ansluten enhet strömmar | 50 (för S1, S2, S3 och endast F1) |
| Maximal enhet stream-dataöverföring | 300 MB per dag (för S1, S2, S3 och endast F1) |

> [!NOTE]
> Om du behöver mer än 50 betalda IoT-hubbar i en Azure-prenumeration kan du kontakta Microsoft Support.

> [!NOTE]
> Det maximala antalet enheter som du kan ansluta till en enda IoT-hubb är för närvarande 1 000 000. Om du vill utöka gränsen kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub begränsar begärandena om följande kvoter överskrids.

| Begränsning | Värde per hubb |
| --- | --- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, lista, uppdatera och ta bort), <br/> enskilda eller massimport/massexport |83.33/sec/Unit (5 000 per minut per enhet) (för S3). <br/> 1.67/sec/Unit (100 per minut per enhet) (för S1 och S2). |
| Enhetsanslutningar |6 000 per sekund och enhet (för S3), 120 per sekund/enhet (för S2), 12 per sekund och enhet (för S1). <br/>Minst 100 per sekund. |
| Sändningar enhet-till-moln |6 000 per sekund och enhet (för S3), 120 per sekund/enhet (för S2), 12 per sekund och enhet (för S1). <br/>Minst 100 per sekund. |
| Sändningar moln-till-enhet | 83.33/sec/Unit (5 000 per minut/enhet) (för S3), 1.67/sec/unit (100 per minut per enhet) (för S1 och S2). |
| Mottagningar moln-till-enhet |833.33/sec/Unit (50 000 per minut/enhet) (för S3), 16.67/sec/unit (1 000 per minut per enhet) (för S1 och S2). |
| Filöverföringsåtgärder |83.33 filen ladda upp meddelanden per sekund och enhet (5 000 per minut per enhet) (för S3), 1,67 filen ladda upp meddelanden/sekund och enhet (100 per minut per enhet) (för S1 och S2). <br/> 10 000 SAS URI: er kan vara ute för ett Azure Storage-konto i taget.<br/> 10 SAS URI:er/enheten kan vara ute vid ett och samma tillfälle. |
| Direkta metoder | 24 MB/sek/enhet (för S3), 480 KB/sek/enhet (för S2), 160 KB/sek/enhet (för S1).<br/> Baserat på 8 KB begränsning mätaren storlek. |
| Läsoperationer för enhetstvilling | 500 per sekund och enhet (för S3), högst 100 per sekund eller 10 per sekund och enhet (för S2), 100 per sekund (för S1) |
| Uppdateringar för enhetstvilling | 250 per sekund och enhet (för S3), högst 50 per sekund eller 5 per sekund och enhet (för S2), 50/sek (för S1) |
| Jobbåtgärder <br/> (skapa, uppdatera, lista, och ta bort) | 83.33/sec/Unit (5 000 per minut/enhet) (för S3), 1.67/sec/unit (100 per minut per enhet) (för S2), 1.67/sec/unit (100 per minut per enhet) (för S1). |
| Jobb per enhetsåtgärd, dataflöde | 50 per sekund och enhet (för S3), maximalt 10 per sekund eller 1 per sekund och enhet (för S2), 10 per sekund (för S1). |
| Enheten initiation dataströmshastighet | 5 nya dataströmmar/sek (för S1, S2, S3 och endast F1). |
