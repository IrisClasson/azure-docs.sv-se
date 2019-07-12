---
title: Importera Azure Log Analytics-data till Power BI | Microsoft Docs
description: Powerbi är en molnbaserad företagsanalystjänst från Microsoft som innehåller kraftfulla visualiseringar och rapporter för analys av olika uppsättningar av data.  Den här artikeln beskriver hur du konfigurerar och importera Log Analytics-data till Power BI och konfigurera den för att automatiskt uppdatera.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2019
ms.author: bwren
ms.openlocfilehash: 0b1627306f1a8e9d9285c72118bfebdcb53d369b
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626123"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importera Azure Monitor log-data till Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) är en molnbaserad företagsanalystjänst från Microsoft som innehåller kraftfulla visualiseringar och rapporter för analys av olika uppsättningar av data.  Du kan importera resultatet av en Azure Monitor log-fråga till en Power BI-datauppsättning så att du kan dra nytta av dess funktioner som kombinerar data från olika källor och dela rapporter på webben och mobila enheter.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Översikt
Importera data från en [Log Analytics-arbetsyta](manage-access.md) i Azure Monitor till Power BI kan du skapa en datauppsättning i Power BI baserat på en [loggfråga](../log-query/log-query-overview.md) i Azure Monitor.  Frågan körs varje gång datauppsättningen uppdateras.  Du kan sedan skapa Power BI-rapporter som använder data från datauppsättningen.  För att skapa datauppsättningen i Power BI, du kan exportera din fråga från Log Analytics för att [Power Query (M) språk](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification).  Du kan sedan använda detta för att skapa en fråga i Power BI Desktop och publicera den till Power BI som en datauppsättning.  Information om den här processen beskrivs nedan.

![Log Analytics till Powerbi](media/powerbi/overview.png)

## <a name="export-query"></a>Exportera fråga
Börja med att skapa en [loggfråga](../log-query/log-query-overview.md) som returnerar de data som du vill fylla i Power BI-datauppsättningen.  Sedan kan du exportera den frågan till [Power Query (M) språk](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) som kan användas av Power BI Desktop.

1. [Skapa loggfråga i Log Analytics](../log-query/get-started-portal.md) att extrahera data för din datauppsättning.
2. Välj **exportera** > **Power BI-fråga (M)** .  Det här exporterar frågan till en textfil med namnet **PowerBIQuery.txt**. 

    ![Exportera loggsökning](media/powerbi/export-analytics.png)

3. Öppna filen och kopiera innehållet.

## <a name="import-query-into-power-bi-desktop"></a>Importera fråga till Power BI Desktop
Power BI Desktop är ett program som gör att du kan skapa datauppsättningar och rapporter som kan publiceras till Power BI.  Du kan också använda den för att skapa en fråga med Power Query-språk som exporteras från Azure Monitor. 

1. Installera [Power BI Desktop](https://powerbi.microsoft.com/desktop/) om du inte redan har det och öppna sedan programmet.
2. Välj **hämta Data** > **tom fråga** att öppna en ny fråga.  Välj sedan **Avancerad redigerare** och klistra in innehållet i den exporterade filen i frågan. Klicka på **Klar**.

    ![Power BI Desktop-fråga](media/powerbi/desktop-new-query.png)

5. Frågan körs, och resultaten visas.  Du kan uppmanas om autentiseringsuppgifter för att ansluta till Azure.  
6. Ange ett beskrivande namn för frågan.  Standardvärdet är **Query1**. Klicka på **Stäng och tillämpa** att lägga till datauppsättningen i rapporten.

    ![Namn på Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicera till Powerbi
När du publicerar till Power BI, skapas en datauppsättning och en rapport.  Om du skapar en rapport i Power BI Desktop, kommer sedan detta att publiceras med dina data.  Annars kan du sedan en tom rapport skapas.  Du kan ändra rapporten i Power BI eller skapa en ny baseras på datauppsättningen.

1. Skapa en rapport som baseras på dina data.  Använd [Power BI Desktop-dokumentationen](https://docs.microsoft.com/power-bi/desktop-report-view) om du inte är bekant med den.  
1. När du är redo att skicka den till Power BI, klickar du på **publicera**.  
1. När du uppmanas, Välj ett mål i Power BI-kontot.  Om du inte har ett specifikt mål i åtanke, använda **Min arbetsyta**.

    ![Publicera Power BI Desktop](media/powerbi/desktop-publish.png)

1. När publiceringen är klar klickar du på **öppna i Power BI** att öppna Power BI med din nya datauppsättning.


### <a name="configure-scheduled-refresh"></a>Konfigurera schemalagd uppdatering
Den datauppsättning som skapats i Power BI har samma data som du såg tidigare i Power BI Desktop.  Du måste uppdatera datauppsättningen regelbundet för att köra frågan igen och fyller den med den senaste informationen från Azure Monitor.  

1. Klicka på arbetsytan där du laddade upp din rapport och välj den **datauppsättningar** menyn. 
1. Välj snabbmenyn bredvid din nya datauppsättningen och välj **inställningar**. 
1. Under **datakällans autentiseringsuppgifter** bör du ha ett meddelande att autentiseringsuppgifterna är ogiltiga.  Det beror på att du inte har angett autentiseringsuppgifterna ännu för datamängden som ska användas när den uppdaterar dess data.  
1. Klicka på **redigera autentiseringsuppgifter** och ange autentiseringsuppgifter för åtkomst till Log Analytics-arbetsyta i Azure Monitor. Om du kräver tvåfaktorsautentisering, Välj **OAuth2** för den **autentiseringsmetod** uppmanas att logga in med dina autentiseringsuppgifter.

    ![Power BI-schema](media/powerbi/powerbi-schedule.png)

5. Under **schemalagd uppdatering** aktivera alternativet att **hålla data uppdaterade**.  Du kan också ändra den **uppdateringsfrekvens** och en eller flera specifika gånger för att köra uppdateringen.

    ![Power BI-uppdatering](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggsökningar](../log-query/log-query-overview.md) att skapa frågor som kan exporteras till Power BI.
* Läs mer om [Power BI](https://powerbi.microsoft.com) att skapa visualiseringar baserade på Azure Monitor log-export.