---
title: Hantera Azure IoT Hub cloud enhet-meddelanden med Azure IoT-verktyg för Visual Studio Code | Microsoft Docs
description: Lär dig hur du använder Azure IoT-verktyg för Visual Studio Code för att övervaka enhet till moln-meddelanden och skicka cloud enhet-meddelanden i Azure IoT Hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 1289e9c8f8cfc9360c9b2325507b43bab3a69028
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838498"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Använd Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub

![Slutpunkt till slutpunkt-diagram](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) är ett användbart Visual Studio Code-tillägg som gör IoT Hub hantering och utveckling av IoT enklare. Den här artikeln handlar om hur du använder Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT-hubben.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Vad du får lära dig

Du lär dig hur du använder Azure IoT-verktyg för Visual Studio Code att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten. Meddelanden från enheten till molnet kan vara sensordata som din enhet samlar in och skickar sedan till din IoT-hubb. Meddelanden från moln till enhet kan vara kommandon som din IoT-hubb som skickas till din enhet blinkar en Indikator som är ansluten till din enhet.

## <a name="what-you-will-do"></a>Vad du ska göra

* Använd Azure IoT-verktyg för Visual Studio Code för att övervaka meddelanden från enheten till molnet.

* Använda Azure IoT-verktyg för Visual Studio Code för att skicka meddelanden från molnet till enheten.

## <a name="what-you-need"></a>Vad du behöver

* En aktiv Azure-prenumeration.

* Azure IoT hub i din prenumeration.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT-verktyg för VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller [öppna den här länken i Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

1. I **Explorer** av VS Code, expanderar **Azure IoT Hub-enheter** avsnitt i det nedre vänstra hörnet.

2. Klicka på **Välj IoT Hub** i snabbmenyn.

3. Ett popup-fönster visas i det nedre högra hörnet så att du kan logga in på Azure för första gången.

4. När du har loggat in din Azure-prenumeration lista visas och välj sedan Azure-prenumeration och IoT Hub.

5. Listan över enheter visas i **Azure IoT Hub-enheter** fliken på några sekunder.

   > [!Note]
   > Du kan också slutföra konfigurationen genom att välja **Set IoT Hub Connection String** (Ange IoT Hub-anslutningssträng). Ange den **iothubowner** princip anslutningssträngen för IoT-hubb som din IoT-enhet som ansluter till i popup-fönstret.

## <a name="monitor-device-to-cloud-messages"></a>Övervaka meddelanden från enheten till molnet

Följ dessa steg för att övervaka meddelanden som skickas från enheten till IoT hub:

1. Högerklicka på enheten och välj **starta inbyggda händelse slutpunkt för övervakning av**.

2. Övervakade meddelandena visas i **utdata** > **Azure IoT Hub Toolkit** vy.

3. Stoppa övervakning genom att högerklicka på den **utdata** visa och välja **stoppa inbyggda händelse slutpunkt för övervakning av**.

## <a name="send-cloud-to-device-messages"></a>Skicka meddelanden från moln till enhet

Om du vill skicka ett meddelande från IoT hub till enheten, Följ dessa steg:

1. Högerklicka på enheten och välj **C2D skicka meddelande till enhet**.

2. Skriv meddelandet i textrutan.

3. Resultaten visas i **utdata** > **Azure IoT Hub Toolkit** vy.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att övervaka meddelanden från enheten till molnet och skicka meddelanden från molnet till enheten mellan dina IoT-enhet och Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]