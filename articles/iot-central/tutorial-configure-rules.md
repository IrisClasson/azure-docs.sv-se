---
title: Konfigurera regler och åtgärder i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar hur du som byggare konfigurerar telemetribaserade regler och åtgärder i Azure IoT Central-programmet.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 56ced4f5e2fd0fbf829f72cff2413998398a7a09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066003"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Självstudie: Konfigurera regler och åtgärder för enheten i Azure IoT Central

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

I den här självstudien skapar du en regel som skickar ett e-postmeddelande när temperaturen i en ansluten luftkonditioneringsenhet överskrider 90&deg; F.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du slutföra självstudien om att [definiera en ny enhetstyp i programmet](tutorial-define-device-type.md) för att skapa enhetsmallen **Ansluten luftkonditioneringsenhet** som du kommer att arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny regel för telemetribaserad i ditt program, i den vänstra navigeringsmenyn och välj **enheten mallar**:

    ![Sidan Enhetsmallar](media/tutorial-configure-rules/templatespage1.png)

    Du ser enhetsmallen **Connected Air Conditioner (1.0.0)** som du skapade i föregående självstudie.

2. För att anpassa mallen för enheten, Välj den **anslutna luftkonditionering** mallen som du skapade i föregående självstudie.

3. Att lägga till en telemetribaserad regel i den **regler** väljer **regler**väljer **+ ny regel**, och välj sedan **telemetri**:

    ![Vyn Regler](media/tutorial-configure-rules/newrule.png)

5. Använd informationen i följande tabell för att definiera regeln:

    | Inställning                                      | Värde                             |
    | -------------------------------------------- | ------------------------------    |
    | Namn                                         | Temperaturmeddelande för luftkonditionering |
    | Aktivera regeln för alla enheter med den här mallen | På                                |
    | Tillstånd                                    | Temperaturen är högre än 90    |
    | Sammansättning                                  | Ingen                              |

    ![Regelvillkor för temperatur](media/tutorial-configure-rules/temperaturerule.png)

    Välj sedan **Spara**.

## <a name="add-an-action"></a>Lägga till en åtgärd

När du definierar en regel kan du även definiera en åtgärd som ska köras när regelvillkoren uppfylls. I den här självstudien skapar du en regel med en åtgärd som skickar en e-postavisering.

1. För att lägga till en **åtgärd** **sparar** du regeln och rullar sedan ned på panelen **Konfigurera telemetriregel**. Välj **+** bredvid **Åtgärder** och välj sedan **E-post**:

    ![Regelåtgärd för temperatur](media/tutorial-configure-rules/addaction.png)

2. Använd informationen i följande tabell för att definiera åtgärden:

    | Inställning   | Värde                          |
    | --------- | ------------------------------ |
    | Till        | Din e-postadress             |
    | Anteckningar     | Temperaturen i luftkonditioneringsenheten har överskridit tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    ![Temperaturåtgärd](media/tutorial-configure-rules/temperatureaction.png)

3. Välj **Spara**. Regeln finns på sidan **Regler**.

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett meddelande till den e-postadress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Skapa en telemetribaserad regel
> * Lägga till en åtgärd

Nu när du har definierat en tröskelvärdesbaserad regel är nästa steg att [anpassa operatörens vyer](tutorial-customize-operator.md).

Mer information om olika typer av regler i Azure IoT Central och hur du parameteriserar regeldefinitionen finns här:
* [Skapa en telemetriregel och konfigurera meddelanden](howto-create-telemetry-rules.md).
* [Skapa en händelseregel och konfigurera meddelanden](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->