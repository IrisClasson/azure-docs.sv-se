---
title: Inaktuell TLS 1,0 och 1,1 i IoT Hub | Microsoft Docs
description: Rikt linjer angående utfasning av TLS 1,0 och 1,1 och stöd för chiffrering i IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849526"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Utfasning av TLS 1,0 och 1,1 i IoT Hub

IoT Hub flyttar till Transport Layer Security (TLS) 1,2 som krypterings metod för IoT-enheter och-tjänster för att tillhandahålla den bästa krypteringen. 

## <a name="timeline"></a>Tidslinje

IoT Hub fortsätter att stödja TLS 1.0/1.1 innan ytterligare meddelande visas. Vi rekommenderar dock att alla kunder migrerar till TLS 1,2 så snart som möjligt.

## <a name="deprecating-tls-11-ciphers"></a>Inaktuella TLS 1,1-chiffer

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Inaktuella TLS 1,0-chiffer

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>TLS 1,2-chiffer

Se [IoT Hub TLS 1,2-rekommenderade chiffer](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Kundfeedback

Även om TLS 1,2-tvång är ett branschledande krypterings alternativ och kommer att aktive ras som planerat, vill vi fortfarande höra från kunder om sina olika distributioner och problem som inför TLS 1,2. För det här ändamålet kan du skicka dina kommentarer till [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
