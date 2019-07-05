---
title: Konfigurera Azure IoT Central instrumentpanel för program | Microsoft Docs
description: Lär dig hur du konfigurerar standard-instrumentpanel för Azure IoT Central program som en builder.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e6947a4f15797028274d49069d9e2787b143860d
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503223"
---
# <a name="configure-the-application-dashboard"></a>Konfigurera instrumentpanel för program

Den **instrumentpanelen** är den sida som läses in när användare som har åtkomst till programmet går du till programmets URL. Om du har valt antingen den **exempel Contoso** eller **exempel Devkits** programmall för att skapa ditt program, ditt program har en fördefinierad instrumentpanel. Om du väljer den **anpassade program** programmall, instrumentpanelen är tomt.

> [!NOTE]
> Användarna kan också [skapa sina egna personliga instrumentpaneler](howto-personalize-dashboard.md) använder i stället standardinstrumentpanelen för programmet.

## <a name="add-tiles"></a>Lägg till paneler

I följande skärmbild visas på instrumentpanelen i ett program som skapats från den **exempel Contoso** mall. Om du vill anpassa standardinstrumentpanelen för ditt program, Välj **redigera** längst upp höger på sidan.

![Instrumentpanel för program baserat på mallen ”Contoso exemplet”](media/howto-configure-homepage/image1a.png)

Att välja **redigera**, öppnas panelen instrumentpanelen bibliotek. Biblioteket innehåller paneler och instrumentpanelen primitiver som du kan använda för att anpassa instrumentpanelen.

![Instrumentpanelen bibliotek](media/howto-configure-homepage/image2a.png)

Du kan till exempel lägga till en **inställningar och egenskaper** rutan för att visa ett urval av de aktuella värdena för inställningar och egenskaper för en enhet. Om du vill göra det väljer du först en **enheten mallen** Välj sedan en **enhetsinstansen**. Efter som tillhandahåller panelen en rubrik och väljer en **inställningen** eller en **egenskapen** ska visas. I följande skärmbild visas inställningar och egenskaper som valts för att lägga till i panelen. Välj **klar** att spara ändringarna på instrumentpanelen.

![”Konfigurera enhetsinformation” formuläret med information om inställningar och egenskaper](media/howto-configure-homepage/image3a.png)

Nu när en operatör visar instrumentpanelen för standard-program, visas den nya panelen med den **ange temperatur** för enheten:

![Fliken ”instrumentpanel” med visas inställningar och egenskaper för panelen](media/howto-configure-homepage/image4a.png)

Du kan utforska andra typer av paneler i biblioteket för att se hur att ytterligare anpassa standardinstrumentpanelen för programmet.

Mer information om hur du använder paneler i Azure IoT Central finns [använda instrumentpaneler](howto-use-tiles.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar Azure IoT Central programmet standardinstrumentpanelen kan du:

> [!div class="nextstepaction"]
> [Lär dig hur du förbereder och ladda upp bilder](howto-prepare-images.md)
