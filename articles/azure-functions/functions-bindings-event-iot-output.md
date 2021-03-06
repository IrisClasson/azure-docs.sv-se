---
title: Azure IoT Hub utgående bindning för Azure Functions
description: Lär dig att skriva meddelanden till Azure IoT Hub-strömmar med Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79277432"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure IoT Hub utgående bindning för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Functions utgående bindningar för IoT Hub. IoT Hub-stödet baseras på [Azure Event Hubs-bindningen](functions-bindings-event-hubs.md).

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-event-iot.md).

> [!IMPORTANT]
> I följande kod exempel används Event Hub API, men den aktuella syntaxen gäller för IoT Hub functions.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Nästa steg

- [Svara på händelser som skickats till händelse strömmen i Event Hub (utlösare)](./functions-bindings-event-iot-trigger.md)
