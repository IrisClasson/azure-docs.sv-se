---
title: Förstå prissättningen för Azure IoT Hub | Microsoft Docs
description: Utvecklarguide – information om hur Avläsning av programvara och priser fungerar med IoT-hubben som fungerade exempel.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 9b6db1b7171652ea5ace4db370b72dc22b6bdc90
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396553"
---
# <a name="azure-iot-hub-pricing-information"></a>Azure IoT Hub information om priser

[Azure IoT Hub-prissättning](https://azure.microsoft.com/pricing/details/iot-hub) tillhandahåller allmän information om olika SKU: er och priser för IoT Hub. Den här artikeln innehåller ytterligare information om hur de olika funktionerna för IoT Hub mäts som meddelanden som IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Avgifter per åtgärd

| Åtgärd | Faktureringsinformation | 
| --------- | ------------------- |
| Identitetsregisteråtgärder <br/> (skapa, hämta, lista, uppdatera och ta bort) | Inte debiteras. |
| Meddelanden från enheten till molnet | Skickade meddelanden debiteras i segment om 4 KB på inkommande till IoT Hub. Ett meddelande på 6 KB debiteras till exempel 2 meddelanden. |
| Meddelanden från moln till enhet | Skickade meddelanden debiteras i segment om 4 KB, till exempel ett meddelande på 6 KB debiteras 2 meddelanden. |
| Filöverföringar | Filöverföring till Azure Storage mäts inte av IoT Hub. File transfer initiation och slutförande meddelanden debiteras som postmeddelandet förbrukade i steg om 4 KB. Att överföra till exempel en 10 MB-fil debiteras som två meddelanden utöver Azure Storage kostnaden. |
| Direkta metoder | Lyckade metod begär Anden debiteras i segment med fyra KB och svaren debiteras i segment på 4 KB som ytterligare meddelanden. Begäranden till frånkopplade enheter debiteras som meddelanden i segment om 4 KB. Till exempel, en metod med en text i 4 KB som resulterar i ett svar utan bröd text från enheten debiteras som två meddelanden. En metod med en 6-KB text som resulterar i ett 1 KB-svar från enheten debiteras som två meddelanden för begäran plus ett annat meddelande för svaret. |
| Enheten och modulen twin läser | Twin läser från enheten eller modulen och från lösningens serverdel slut debiteras som meddelanden i 512 byte-segment. Till exempel debiteras läsning av en enhetstvilling på 6 KB som 12 meddelanden. |
| Enheten och modulen uppdateringar för enhetstvilling (taggar och egenskaper) | Uppdateringar för enhetstvilling från enheten eller modulen och lösningens backend-server debiteras som meddelanden i 512 byte-segment. Till exempel debiteras läsning av en enhetstvilling på 6 KB som 12 meddelanden. |
| Enheten och modulen twin frågor | Frågor debiteras som meddelanden beroende på resultatstorlek i 512 byte-segment. |
| Jobbåtgärder <br/> (skapa, uppdatera, visa, ta bort) | Inte debiteras. |
| Jobbåtgärder per enhet | Jobbåtgärder (t.ex uppdateringar för enhetstvilling och metoder) debiteras som vanligt. Ett jobb som lett till 1000 metodanrop med 1 KB begäranden och svar med tom brödtext debiteras till exempel 1000 meddelanden. |
| Keep-alive-meddelanden | När du använder AMQP- eller MQTT-protokoll, debiteras inte meddelanden som utbyts för att upprätta anslutningen och meddelanden som utbyts i förhandlingen. |

> [!NOTE]
> Alla storlekar beräknas överväger Nyttolaststorlek i byte (protokollet synkroniseringstecken ignoreras). För meddelanden som har egenskaper och brödtext, beräknas storleken på ett protokoll-oberoende sätt. Mer information finns i [IoT Hub meddelande format](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exempel #1

En enhet skickar ett 1 KB enhet-till-moln-meddelande per minut till IoT Hub, som sedan läses av Azure Stream Analytics. Lösningens Server del anropar en metod (med en 512 byte-nytto Last) på enheten var 10: e minut för att utlösa en speciell åtgärd. Enheten svarar på metoden med ett resultat av 200 byte.

Enheten använder:

* Ett meddelande om att * 60 minuter * 24 timmar = 1440 meddelanden per dag för enhet-till-moln-meddelanden.
* Två begär plus svar * 6 gånger per timme * 24 timmar = 288 meddelanden för metoder.

Den här beräkningen ger totalt 1728 meddelanden per dag.

## <a name="example-2"></a>Exempel #2

En enhet skickar ett meddelande om enheten till molnet att 100 KB varje timme. Uppdaterar den även dess enhetstvilling med 1 KB nyttolaster var fjärde timme. Lösningens serverdel sluta, en gång per dag, läser 14 KB enhetstvillingen och uppdaterar med 512 byte-nyttolaster ändra konfigurationer.

Enheten använder:

* 25 (100 KB/4 KB) meddelanden * 24 timmar för meddelanden från enheten till molnet.
* Två meddelanden (1 KB/0,5 KB) * sex gånger per dag för uppdateringar för enhetstvilling.

Den här beräkningen ger totalt 612 meddelanden per dag.

Lösningens serverdel förbrukar 28 meddelanden (14 KB/0,5 KB) för att läsa enhetstvillingen, plus ett meddelande om att uppdatera det, 29 meddelanden totalt.

Totalt, enheten och lösningens backend-server kan du använda 641 meddelanden per dag.
