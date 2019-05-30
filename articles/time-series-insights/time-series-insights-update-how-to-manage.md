---
title: Etablera och hantera förhandsversion av Azure Time Series | Microsoft Docs
description: För att förstå hur du etablerar och hanterar Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6251df2317ceff9dded92f2d829bfab0503fdf1b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237596"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Etablera och hantera Azure Time Series Insights Preview

Den här artikeln beskriver hur du skapar och hanterar en förhandsversion av Azure Time Series Insights-miljö med hjälp av den [Azure-portalen](https://portal.azure.com/).

## <a name="overview"></a>Översikt

Azure Time Series Insights Preview-miljöer är betala per användning (PAYG)-miljöer.

När du etablerar en förhandsversion av Azure Time Series Insights-miljö kan skapa du två Azure-resurser:

* En förhandsversion av Azure Time Series Insights-miljö  
* Ett Azure Storage general-purpose v1-konto
  
Lär dig [hur du planerar din miljö](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> För förhandsversionen, kontrollera att du använder ett allmänt Azure Storage-v1 (GPv1)-konto.

Alternativt kan du associera varje förhandsversion av Azure Time Series Insights-miljö med en händelsekälla. Mer information finns i [lägga till en händelsekälla hub](./time-series-insights-how-to-add-an-event-source-eventhub.md) och [Lägg till en IoT hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md). Du anger en tidsstämpel-ID-egenskap och en unik konsumentgrupp under det här steget. På så sätt att miljön har åtkomst till lämplig händelserna.

När etableringen har slutförts kan ändra du dina principer för åtkomst och andra miljö-attribut som passar dina affärsbehov.

## <a name="create-the-environment"></a>Skapa miljön

Följande steg beskriver hur du skapar en förhandsversion av Azure Time Series Insights-miljö:

1. Välj den **PAYG** knappen den **SKU** menyn. Ange ett namn på miljö och välj vilken prenumerationsgrupp och vilken resursgrupp som du använder. Välj en plats som stöds för den miljö som ska köras i.

   [![Skapa en Azure Time Series Insights-instans.](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

1. Ange en tidsserie-ID.

    >[!NOTE]
    > * Time Series-ID är skiftlägeskänsliga och kan ändras. (Det kan inte ändras när den har ställts in.)
    > * Time Series-ID: N kan vara upp till tre nycklar.
    > * Mer information om hur du väljer en tid-ID [väljer en tid-ID](./time-series-insights-update-how-to-id.md).

1. Skapa ett Azure storage-konto genom att välja ett lagringskontonamn och utse ett alternativ för replikering. Gör automatiskt skapar ett Azure Storage general-purpose v1-konto. Det kommer att skapas i samma region som förhandsversion av Azure Time Series Insights-miljö som du valde tidigare.

    [![Skapa ett Azure storage-konto för din instans](media/v2-update-manage/manage_five.PNG)](media/v2-update-manage/manage_five.PNG#lightbox)

1. Alternativt kan du lägga till en händelsekälla.

   * Time Series Insights stöder [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) och [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) som alternativ. Du kan lägga till en enda händelsekälla vid tidpunkten för skapandet av miljön, kan du lägga till en annan händelsekälla senare. Det är bäst att skapa en unik konsumentgrupp för att säkerställa att alla händelser som är synliga för förhandsversionen av Azure Time Series Insights-instans. Du kan välja en befintlig konsumentgrupp eller skapa en ny konsumentgrupp när du lägger till händelsekällan.

   * Du bör även välja lämplig tidsstämpel-egenskapen. Som standard använder Azure Time Series Insights Kötid meddelande för varje händelsekälla.

     > [!TIP]
     > Kötid meddelande kanske inte den bästa konfigurerade inställningen för att använda batch händelse eller historiska data överför scenarier. Se till att kontrollera ditt beslut att använda eller inte använda en tidsstämpel-egenskapen i sådana fall.

     [![Händelse-flik för datakälla](media/v2-update-manage/manage_two.PNG)](media/v2-update-manage/manage_two.PNG#lightbox)

1. Bekräfta att din miljö har etablerats med de önskade inställningarna.

    [![Granska + skapa flik](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din förhandsversion av Azure Time Series Insights-miljö med hjälp av Azure portal. Här följer de större skillnaderna i hanteringen av en förhandsversionen av PAYG Azure Time Series Insights-miljö, till skillnad från en S1 eller S2-miljö med hjälp av Azure portal:

* Azure-portalens **översikt** bladet är oförändrad i Azure Time Series Insights, utom på följande sätt:
  * Kapacitet tas bort, eftersom det här konceptet inte är relevant för PAYG-miljöer.
  * Time Series-ID-egenskapen har lagts till. Det avgör hur dina data är partitionerad.
  * Referensdatauppsättningar tas bort.
  * Visas URL: en tar dig till den [förhandsversion av Azure Time Series Insights explorer](./time-series-insights-update-explorer.md).
  * Namnet på ditt Azure storage-konto visas.

* Azure-portalens **konfigurera** bladet har tagits bort i förhandsversionen av Azure Time Series Insights eftersom PAYG-miljöer inte kan konfigureras.

* Azure-portalens **referensdata** bladet har tagits bort i förhandsversionen av Azure Time Series Insights eftersom referensdata inte är en komponent i PAYG-miljöer.

[![Time Series Insights Preview-miljö i Azure portal](media/v2-update-manage/manage_four.PNG)](media/v2-update-manage/manage_four.PNG#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs [planera miljön](./time-series-insights-update-plan.md).

- Lär dig hur du [lägga till en händelsekälla hub](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurera [en IoT hub-källa](./time-series-insights-how-to-add-an-event-source-iothub.md).