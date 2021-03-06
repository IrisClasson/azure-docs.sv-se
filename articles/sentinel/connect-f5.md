---
title: Anslut F5 ASM-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder F5 ASM data Connector för att hämta F5 ASM-loggar till Azure Sentinel. Visa F5 ASM-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 63e20467750e9a4b0c85de33180d4fa51831e59b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555417"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Anslut F5 ASM till Azure Sentinel

Den här artikeln förklarar hur du använder F5 ASM data Connector för att enkelt hämta dina F5 ASM-loggar till Azure Sentinel. På så sätt kan du Visa F5 ASM-data i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Med F5 ASM-data i Azure Sentinel får du mer insikter om din organisations webb program säkerhet och förbättrar dina säkerhets funktioner. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Konfigurera F5 ASM för att skicka CEF-meddelanden

1. Följ anvisningarna i [F5 Konfigurera loggning av program säkerhets händelser](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) för att konfigurera fjärrloggning med hjälp av följande rikt linjer:
   - Ange **typen av Fjärrlagring** till **CEF**.
   - Ställ in **protokollet** på **TCP**.
   - Ange **IP-adressen** till syslog-serverns IP-adress.
   - Ange **port numret** till **514**, eller den port som du anger att agenten ska använda.
   - Du kan ange max storleken för **frågesträngen** till den storlek som du har angett i din agent.

1. Om du vill använda det relevanta schemat i Log Analytics för CEF-händelser söker du efter `CommonSecurityLog` .

1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter F5 ASM till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

