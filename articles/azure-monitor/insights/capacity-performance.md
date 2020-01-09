---
title: Kapacitet och prestanda lösning i Azure Monitor | Microsoft Docs
description: Använd Kapacitet och prestanda lösning i Övervakaren för att hjälpa dig att förstå kapaciteten hos dina Hyper-V-servrar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: c444991166d9c7d3f1d8ed39e19e333d3c27f982
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405746"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Planera kapaciteten för virtuella Hyper-V-datorer med Kapacitet och prestanda-lösningen (inaktuell)

![Symbol för kapacitet och prestanda](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Lösningen kapacitet och prestanda är inaktuell.  Kunder som redan har installerat lösningen kan fortsätta att använda den, men kapacitet och prestanda kan inte läggas till nya arbetsytor.

Du kan använda Kapacitet och prestanda lösning i Övervakaren för att hjälpa dig att förstå kapaciteten hos dina Hyper-V-servrar. Lösningen ger insikter om Hyper-V-miljön genom att visa det övergripande utnyttjandet (processor, minne och disk) på värdarna och de virtuella datorerna som körs på dessa Hyper-V-värdar. Mått har samlats in för CPU, minne och diskar mellan alla värdar och de virtuella datorerna körs på dem.

Lösning:

-   Visar värdar med högsta och lägsta användning av processor och minne
-   Visar virtuella datorer med högsta och lägsta användning av processor och minne
-   Visar virtuella datorer med högsta och lägsta IOPS och dataflöden användning
-   Visar vilka virtuella datorer som körs på vilka värdar
-   Visar de översta diskarna med stora dataflöden, IOPS och svarstider i klusterdelade volymer
- Gör att du kan anpassa och filtrera baserat på grupper

> [!NOTE]
> Den tidigare versionen av kapacitet och prestanda lösningen kallas Kapacitetshanteringsområde krävs både System Center Operations Manager och System Center Virtual Machine Manager. Den här uppdaterade lösningen har inte dessa beroenden.


## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Support | Beskrivning |
|---|---|---|
| [Windows-agenter](../../azure-monitor/platform/agent-windows.md) | Ja | Lösningen samlar in information om kapacitet och prestanda från Windows-agenter. |
| [Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) | Inga    | Lösningen samlar inte in information om kapacitet och prestanda från direkt Linux-agenter.|
| [SCOM-hanteringsgrupp](../../azure-monitor/platform/om-agents.md) | Ja |Lösningen samlar in data för kapacitet och prestanda från agenter i en ansluten SCOM-hanteringsgrupp. En direktanslutning från SCOM-agenten till Log Analytics krävs inte.|
| [Azure Storage-konto](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Inga | Azure storage inkluderar inte kapacitet och prestanda.|

## <a name="prerequisites"></a>Krav

- Windows- eller Operations Manager-agenter måste installeras på Windows Server 2012 eller högre Hyper-V-värdar, inte virtuella datorer.


## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till lösning för kapacitet och prestanda för din arbetsyta.

- Lägga till lösning för kapacitet och prestanda för Log Analytics-arbetsytan med processen som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).

## <a name="management-packs"></a>Hanteringspaket

Om din SCOM-hanteringsgrupp är ansluten till Log Analytics-arbetsytan kan sedan installeras följande hanteringspaket i SCOM när du lägger till den här lösningen. Det krävs ingen konfigurering eller underhåll av dessa hanteringspaket.

- Microsoft.IntelligencePacks.CapacityPerformance

Händelsen 1201 liknar:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

När lösningen kapacitet och prestanda uppdateras ändras versionsnumret.

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till lösningen kapacitet och prestanda i din arbetsyta, har kapacitet och prestanda lagts till instrumentpanelen. Den här panelen visar antal aktiva Hyper-V-värdar och antalet aktiva virtuella datorer som har övervakas för tidsperioden väljas.

![Panel för kapacitet och prestanda](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Granska användning

Klicka på panelen kapacitet och prestanda för att öppna instrumentpanelen kapacitet och prestanda. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar upp till tio objekt som matchar denna kolumns kriterier för specificerat omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

- **Värdar**
    - **Vara värd för processoranvändning** visar en grafisk trend över CPU-utnyttjande på värddatorer och en lista över värdar, baserat på den valda tidsperioden. Hovra över linjediagrammet att visa detaljer för en specifik tidpunkt. Klicka på diagrammet om du vill visa mer information i loggsökning. Klicka på alla värdnamn för att öppna loggsökning och visa information om prestandaräknare för processor för värdbaserade virtuella datorer.
    - **Vara värd för minnesanvändning** visar en grafisk trend över värddatorer minnesanvändning och en lista över värdar, baserat på den valda tidsperioden. Hovra över linjediagrammet att visa detaljer för en specifik tidpunkt. Klicka på diagrammet om du vill visa mer information i loggsökning. Klicka på alla värdnamn för att öppna loggsökning och visa information om prestandaräknare minne för värdbaserade virtuella datorer.
- **Virtuella datorer**
    - **Processoranvändning för virtuell dator** visar en grafisk trend över CPU-utnyttjande på virtuella datorer och en lista över virtuella datorer, baserat på den valda tidsperioden. Hovra över linjediagrammet att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökning. Klicka på alla VM-namn för att öppna loggsökning och visa sammanställs information om prestandaräknare för processor för den virtuella datorn.
    - **Minnesanvändning för virtuell dator** visar en grafisk trend över minnesanvändning för virtuella datorer och en lista över virtuella datorer, baserat på den valda tidsperioden. Hovra över linjediagrammet att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökning. Klicka på alla VM-namn för att öppna loggsökning och se information om prestandaräknare aggregerade minne för den virtuella datorn.
    - **VM totalt Disk-IOPS** visar en grafisk trend över den totala disken IOPS för virtuella datorer och en lista över virtuella datorer med IOPS för varje baserat på den valda tidsperioden. Hovra över linjediagrammet att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökning. Klicka på alla VM-namn för att öppna loggsökning och visa aggregerade IOPs per disk prestandaräknaren information för den virtuella datorn.
    - **VM totalt Diskgenomflöde** visar en grafisk trend över det totala diskgenomflödet för virtuella datorer och en lista över virtuella datorer med det totala diskgenomflödet för varje baserat på den valda tidsperioden. Hovra över linjediagrammet att visa detaljer för en specifik tidpunkt för de översta 3 virtuella datorerna. Klicka på diagrammet om du vill visa mer information i loggsökning. Klicka på alla VM-namn för att öppna loggsökning och visa sammanställda totala dataflödet räknaren diskinformation för den virtuella datorn.
- **Klusterdelade volymer**
    - **Totalt dataflöde** visar summan av både läsningar och skrivningar på klusterdelade volymer.
    - **Totalt antal IOPS** visar summan av indata/utdataåtgärder per sekund på klusterdelade volymer.
    - **Total svarstid** visar den totala svarstiden på klusterdelade volymer.
- **Vara värd för densitet** övre panelen visar det totala antalet värdar och virtuella datorer som är tillgängliga i lösningen. Klicka på övre panelen för att visa ytterligare information i loggsökning. Visas även alla värdar och antalet virtuella datorer som finns. Klicka på en värd att öka detaljnivån i VM-resultaten i en loggsökning.


![instrumentpanelen värdar bladet](./media/capacity-performance/dashboard-hosts.png)

![bladet för instrumentpanelen för virtuella datorer](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Utvärdera prestanda

Produktion datormiljöer skiljer sig avsevärt från en organisation till en annan. Dessutom kapacitet och prestanda arbetsbelastningar kan bero på hur dina virtuella datorer körs, och vad du vill normal. Specifika procedurer som hjälper dig att mått prestanda förmodligen inte gäller för din miljö. Så mer generaliserad normativ vägledning passar bättre för att. Microsoft publicerar en mängd olika vägledningsartiklar för att du mäta prestanda.

Sammanfattningsvis lösningen samlar in kapacitet och prestandadata från olika källor, inklusive prestandaräknare. Dessa data för kapacitet och prestanda som presenteras i olika ytor i lösningen, och jämför resultaten till webbplatser på den [mäta prestanda på Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx) artikeln. Även om artikeln har publicerats tid sedan är den mått, överväganden och riktlinjer fortfarande giltiga. Artikeln innehåller länkar till andra användbara resurser.


## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på loggsökningar för kapacitet och prestanda Mina data som samlas in och beräknas genom att den här lösningen.


| Söka i data | Beskrivning |
|:--- |:--- |
| Alla värdminnen | Perf &#124; där ObjectName == ”kapacitet och prestanda” och CounterName == ”värd tilldelat minne MB” &#124; sammanfatta MB = avg(CounterValue) av instansnamn |
| Alla konfigurationer av minnen för virtuell dator | Perf &#124; där ObjectName == ”kapacitet och prestanda” och CounterName == ”VM tilldelat minne MB” &#124; sammanfatta MB = avg(CounterValue) av instansnamn |
| Analys av totalt Disk-IOPS i alla virtuella datorer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”VHD läsningar/s” eller CounterName == ”VHD skrivningar/s”) &#124; sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 1 timme), CounterName, instansnamn |
| Analys av totalt Diskgenomflöde i alla virtuella datorer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”VHD Läs MB/s” eller CounterName == ”VHD skriva MB/s”) &#124; sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 1 timme), CounterName, instansnamn |
| Analys av totalt IOPS i alla klusterdelade volymer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”CSV läsningar/s” eller CounterName == ”CSV skrivningar/s”) &#124; sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 1 timme), CounterName, instansnamn |
| Analys av totalt genomflöde i alla klusterdelade volymer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”CSV läsningar/s” eller CounterName == ”CSV skrivningar/s”) &#124; sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 1 timme), CounterName, instansnamn |
| Analys av Totalfördröjning i alla klusterdelade volymer | Perf &#124; där ObjectName == ”kapacitet och prestanda” och (CounterName == ”CSV Läs latens” eller CounterName == ”CSV skriva svarstid”) &#124; sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 1 timme), CounterName, instansnamn |


## <a name="next-steps"></a>Nästa steg
* Använd [Loggsökningar i Log Analytics](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerad information om kapacitet och prestanda.
