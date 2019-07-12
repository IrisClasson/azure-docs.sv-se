---
title: Konfigurera Azure Security Center för IoT-lösning förhandsversion | Microsoft Docs
description: Lär dig hur du konfigurerar din IoT-lösning för slutpunkt till slutpunkt som använder Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 7f90dba899651b677740e9ceb88bdd579ebb073c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616643"
---
# <a name="quickstart-configure-your-iot-solution"></a>Snabbstart: Konfigurera din IoT-lösning

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en förklaring av hur du utför inledande konfiguration av din IoT-säkerhetslösning med ASC för IoT. 

## <a name="azure-security-center-asc-for-iot"></a>Azure Security Center (ASC) för IoT

ASC för IoT ger omfattande slutpunkt till slutpunkt för Azure-baserade IoT-lösningar.

Med ASC för IoT, kan du övervaka hela IoT-lösningen i en instrumentpanel, visa alla dina IoT-enheter, IoT-plattformar och backend-resurser i Azure.

När du har aktiverat på din IoT-hubb, identifierar ASC för IoT automatiskt andra Azure-tjänster också ansluten till din IoT-hubb och relaterade till din IoT-lösning.

Förutom automatisk relationsidentifiering, kan du också välja vilka andra Azure-resurser till taggen som en del av din IoT-lösning.
Dina val kan du lägga till hela prenumerationer, resursgrupper eller enkla resurser.

När du har definierat alla relationer resource utnyttjar ASC för IoT Azure Security Center för att ge säkerhetsrekommendationer och säkerhetsaviseringar för dessa resurser.

## <a name="add-azure-resources-to-your-iot-solution"></a>Lägga till Azure-resurser i din IoT-lösning

Lägg till ny resurs i din IoT-lösning genom att göra följande: 

1. Öppna din **IoT Hub** i Azure-portalen. 
2. Välj och öppna **resurser** under **Security** menyn till vänster. 
3. Välj **Lägg till resurser**.
4. Välj resurser som hör till din IoT-lösning.
5. Klicka på **Lägg till**. 

Grattis! Du har lagt till en ny resurs till din IoT-lösning.

ASC för IoT nu Övervakare som du har nyligen lagt till resurser, och hämtar relevanta säkerhetsrekommendationer och säkerhetsaviseringar som en del av din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar säkerhetsmoduler...

> [!div class="nextstepaction"]
> [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)