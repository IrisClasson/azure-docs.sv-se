---
title: Simulerade Raspberry Pi till molnet (Node.js) – Anslut Raspberry Pi web-simulatorn på Azure IoT Hub | Microsoft Docs
description: Anslut Raspberry Pi web-simulatorn på Azure IoT Hub för Raspberry Pi att skicka data till Azure-molnet.
author: wesmc7777
manager: philmea
keywords: Raspberry pi-simulator, azure iot raspberry pi, raspberry pi iot-hubb, raspberry pi skicka data till molnet, raspberry pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 9af2f0860c415ddb701e24ed8a698fae36d42e1f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838708"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Anslut Raspberry Pi onlinesimulator till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi onlinesimulator. Du lär dig sedan att sömlöst ansluta Pi-simulatorn till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md).

Om du har några fysiska enheter, [Anslut Raspberry Pi till Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) att komma igång.

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</div>

## <a name="what-you-do"></a>Vad du gör

* Lär dig grunderna i Raspberry Pi onlinesimulator.

* Skapa en IoT-hubb.

* Registrera en enhet för Pi i din IoT-hubb.

* Kör ett exempelprogram på Pi skicka simulerad sensordata till din IoT-hubb.

Anslut simulerade Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram med simulator att generera sensordata. Slutligen kan skicka du sensordata till din IoT hub.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure-IoT-hubb och hämta nya enhetens anslutningssträng. Om du inte har ett Azure-konto, [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) på bara några minuter.

* Hur du arbetar med Raspberry Pi onlinesimulator.

* Så här skickar sensordata till din IoT hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Översikt över Raspberry Pi web simulator

Klicka på knappen för att starta Raspberry Pi onlinesimulator.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Start Raspberry Pi Simulator</a>

Det finns tre områden i web-simulatorn.

1. Sammansättningen område - standard-kretsen är att en Pi ansluter med en BME280 sensor och en Indikator. Området är låst i förhandsversionen så för tillfället inte kan du göra anpassning.

2. Koda området - Kodredigerare online du kan koda med Raspberry Pi. Standard-exempelprogrammet hjälper dig att samla in sensordata från BME280 sensor och skickar till Azure IoT Hub. Programmet är helt kompatibel med verkliga Pi-enheter. 

3. Integrerad konsolfönstret - visar utdata från din kod. Det finns tre knappar längst ned i fönstret.

   * **Kör** -köra programmet i området för kodning.

   * **Återställ** -återställa området kodning till standard-exempelprogram.

   * **Vikning/Expandera** -på höger sida finns en knapp att vikning/Expandera konsolfönstret.

> [!NOTE]
> Raspberry Pi web simulatorn är nu tillgängligt i förhandsversionen. Vi vill gärna höra din röst i den [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Källkoden är offentlig på [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Översikt över Pi onlinesimulator](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Kör ett exempelprogram på Pi web simulator

1. Kontrollera att du arbetar med exempelprogrammet som standard i kodning området. Ersätt platshållaren i rad 15 med Azure IoT hub-anslutningssträngen.
1. 
   ![Ersätt enhetens anslutningssträng](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Välj **kör** eller typ `npm start` att köra programmet.

Du bör se följande utdata som visar sensordata och meddelanden som skickas till din IoT hub ![utdata - sensordata som skickas från Raspberry Pi till din IoT-hubb](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelandena som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av IoT-hubben från den simulerade enheten är att använda Azure IoT Tools för Visual Studio Code. Mer information finns i [Använd Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt för fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
