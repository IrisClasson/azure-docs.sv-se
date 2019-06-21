---
title: Kom igång med Azure Monitor Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att använda Log Analytics i Azure-portalen för att skriva frågor.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: 17b5c0b459e70909d9f305beb8bf87b83f1cf65c
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296520"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Kom igång med Log Analytics i Azure Monitor

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här självstudiekursen kommer du lära dig hur du använder Log Analytics i Azure-portalen för att skriva Azure Monitor log-frågor. Det får du lära dig hur du:

- Använd Log Analytics för att skriva en enkel fråga
- Förstå schemat för dina data
- Filtrera, sortera och gruppera resultat
- Tillämpa ett tidsintervall
- Skapa diagram
- Spara och läsa in frågor
- Exportera och dela frågor

En självstudiekurs om hur du skriver loggfrågor finns i [Kom igång med loggfrågor i Azure Monitor](get-started-queries.md).<br>
Mer information om loggfrågor finns i [översikt över log frågor i Azure Monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Uppfyll Log Analytics
Log Analytics är ett webbverktyg som används för att skriva och köra Azure Monitor log-frågor. Öppna den genom att välja **loggar** i Azure Monitor-menyn. Det börjar med en ny tom fråga.

![Startsida](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Brandväggskrav
Om du vill använda Log Analytics kräver åtkomst till följande adresser i din webbläsare. Om webbläsaren har åtkomst till Azure-portalen via en brandvägg, måste du aktivera åtkomst till dessa adresser.

| URI: N | IP-adress | Portar |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisk | 80,443 |
| api.loganalytics.io | Dynamisk | 80,443 |
| docs.loganalytics.io | Dynamisk | 80,443 |

## <a name="basic-queries"></a>Grundläggande frågor
Frågor kan användas för att söka villkoren, identifiera trender, analysera mönster och ger många andra insikter utifrån dina data. Börja med en grundläggande fråga:

```Kusto
Event | search "error"
```

Den här frågan söker den _händelse_ tabellen för poster som innehåller termen _fel_ i en egenskap.

Frågor kan börja med antingen ett tabellnamn eller en [search](/kusto/query/searchoperator) kommando. Exemplet ovan börjar med tabellnamnet _händelse_, som hämtar alla poster från tabellen händelse. Vertikalstreck (|) separerar kommandon, så att utdata från den första som fungerar som indata för kommandot. Du kan lägga till valfritt antal kommandon till en enskild fråga.

Ett annat sätt att skriva samma frågan är:

```Kusto
search in (Event) "error"
```

I det här exemplet **search** är begränsad till den _händelse_ tabellen och alla poster i tabellen genomsöks efter termen _fel_.

## <a name="running-a-query"></a>En fråga som körs
Köra en fråga genom att klicka på den **kör** knapp eller trycker på **SKIFT + RETUR**. Överväg följande information som avgör den kod som ska köras och de data som returneras:

- Radbrytningar: En enda break gör det lättare att läsa din fråga. Flera radbrytningar dela upp den till separata frågor.
- Markören: Placera markören någonstans i frågan för att köra den. Den aktuella frågan anses vara koden tills en tom rad hittas.
- Tidsintervall – ett tidsintervall för _senaste 24 timmarna_ är som standard. Om du vill använda ett annat intervall, Använd tidsväljare eller Lägg till en explicit tid filter för datumintervall i frågan.


## <a name="understand-the-schema"></a>Förstå schemat
Schemat är en uppsättning tabeller visuellt grupperade under en logisk kategori. Nu kan många av kategorierna av lösningar. Den _LogManagement_ kategorin innehåller vanliga data, till exempel Windows och Syslog-händelser, prestandadata och agentens pulsslag.

![Schema](media/get-started-portal/schema.png)

I varje tabell ordnas data i kolumner med olika datatyper som anges av ikonerna bredvid kolumnnamnet på. Till exempel den _händelse_ tabellen som visas i skärmbilden innehåller kolumner som _datorn_ som är text, _EventCategory_ som är ett tal och _TimeGenerated_ som är datum/tid.

## <a name="filter-the-results"></a>Filtrera resultaten
Börja med att hämta allt den _händelse_ tabell.

```Kusto
Event
```

Log Analytics scope automatiskt resultaten efter:

- Tidsintervall:  Som standard är frågorna begränsade till den senaste 24 timmarna.
- Antal resultat: Resultatet är begränsade till högst 10 000 poster.

Den här frågan är mycket allmänna och returnerar den för många resultat för att användbar. Du kan filtrera resultaten via tabellelement eller genom att uttryckligen lägga till ett filter i frågan. Filtrerar resultaten via tabellelement gäller befintliga resultatmängden, medan ett filter för att själva frågan returnerar en ny filtrerat resultat ställa in och kan därför få exaktare resultat.

### <a name="add-a-filter-to-the-query"></a>Lägga till ett filter i frågan
Det finns en pil till vänster om varje post. Klicka på pilen för att öppna informationen för en viss post.

Hovra över ett kolumnnamn för de ”+” och ”-” ikoner för att visa. Klicka på tecknet ”+” för att lägga till ett filter som ska returnera bara poster med samma värde. Klicka på ”-” undanta poster med det här värdet och klicka sedan på **kör** att köra frågan igen.

![Lägg till filter i frågan](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrera genom tabellelement
Nu ska vi fokusera på händelser med en allvarlighetsgrad för _fel_. Detta har angetts i en kolumn med namnet _EventLevelName_. Du måste rulla till höger för att se den här kolumnen.

Klicka på filterikonen bredvid rubriken för kolumnen och i popup-fönstret väljer du värden som _börjar med_ texten _fel_:

![Filter](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Sortera och gruppera resultat
Resultaten nu minskar antalet för att inkludera endast felhändelser från SQL Server, som skapats under de senaste 24 timmarna. Resultaten sorteras dock inte på något sätt. Sortera resultaten efter en viss kolumn som _tidsstämpel_ exempelvis klickar du på rubriken för kolumnen. Ett enda klick sorteras i stigande ordning, medan en andra Klicka om du vill sortera i fallande.

![Sorteringskolumn](media/get-started-portal/sort-column.png)

Det är ett annat sätt att ordna resultaten av grupper. Att gruppera resultat efter en viss kolumn helt enkelt dra kolumnrubriken ovanför de andra kolumnerna. Om du vill skapa undergrupper dra andra kolumner i övre fältet samt.

![Grupper](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Välj kolumner att visa
Resultattabellen innehåller ofta många kolumner. Du kanske upptäcker att vissa av kolumnerna returnerade visas inte som standard, eller kan du ta bort några kolumner som visas. Klicka på knappen kolumner för att välja kolumnerna som ska visas:

![Välja kolumner](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Välj ett tidsintervall
Som standard tillämpar Log Analytics de _senaste 24 timmarna_ tidsintervall. Om du vill använda ett annat intervall, Välj ett annat värde via tidsväljare och klicka på **kör**. Utöver de förinställda värdena kan du använda den _anpassat tidsintervall_ möjlighet att välja ett absolut adressintervall för din fråga.

![Tidsväljare](media/get-started-portal/time-picker.png)

När du väljer ett anpassat tidsintervall, är de valda värdena i UTC, som kan skilja sig från din lokala tidszon.

Om frågan uttryckligen innehåller ett filter för _TimeGenerated_tidpunkten väljare rubrik visas _ange i fråga_. Manuellt val kommer att inaktiveras för att förhindra att en konflikt.


## <a name="charts"></a>Diagram
Förutom att returnera resultat i en tabell, kan frågeresultat presenteras i visual format. Använd följande fråga som ett exempel:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Som standard visas resultaten i en tabell. Klicka på _diagram_ så visas resultaten i en grafisk vy:

![Liggande stapeldiagram](media/get-started-portal/bar-chart.png)

Resultaten visas i ett stående stapeldiagram. Klicka på _staplat fraktionsdiagram_ och välj _cirkel_ att visa en annan vy av resultaten:

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

Olika egenskaper för vyn, till exempel x och y-axlarna eller gruppering och dela inställningar, kan ändras manuellt från kontrollfältet.

Du kan också ange önskad vy i frågan, med hjälp av operatorn rendering.

### <a name="smart-diagnostics"></a>Smart diagnostik
Om det finns en plötslig insamling eller steg i dina data på ett tidsdiagram kan du se en markerad punkt på raden. Indikerar det att _Smart Diagnostics_ har identifierat en kombination av egenskaper som filtrerar bort plötsliga ändringen. Klicka på punkten att hämta mer information om filtret och för att se den filtrera versionen. Detta kan hjälpa dig identifiera vad som orsakade ändringen:

![Smart diagnostik](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Fäst vid instrumentpanelen
Klicka på ikonen PIN-kod för att fästa ett diagram eller en tabell till en av dina delade Azure-instrumentpaneler.

![Fäst vid instrumentpanelen](media/get-started-portal/pin-dashboard.png)

Vissa förenklingar tillämpas på ett diagram när du fäster den på en instrumentpanel:

- Tabellens kolumner och rader: För att fästa en tabell till instrumentpanelen, måste den ha fyra eller färre kolumner. Endast de översta sju raderna visas.
- Begränsning: Frågor begränsas automatiskt till de senaste 14 dagarna.
- Bin antal begränsningar: Om du visar ett diagram med diskreta lagerplatser mycket mindre fylls i automatiskt lagerplatser automatiskt är grupperade i en enda _andra_ bin.

## <a name="save-queries"></a>Spara frågor
När du har skapat en användbar fråga, kanske du vill spara den eller dela med andra. Den **spara** ikonen som visas i det översta fältet.

Du kan spara sidan hela eller en enskild fråga som en funktion. Funktioner är frågor som kan också refereras av andra frågor. Du måste ange ett funktionsalias som det namn som används för att anropa den här frågan när de refereras av andra förfrågningar för att spara en fråga som en funktion.

![Spara funktionen](media/get-started-portal/save-function.png)

Log Analytics-frågor finns alltid sparas i en vald arbetsyta och delas med andra användare av arbetsytan.

## <a name="load-queries"></a>Läsa in frågor
Query Explorer-ikonen visas i området längst upp till höger. Här visas alla sparade frågor efter kategori. Du kan också markera specifika frågor som Favoriter för att snabbt hitta dem i framtiden. Dubbelklicka på en sparad fråga om du vill lägga till den i det aktuella fönstret.

![Frågeutforskaren](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportera och dela som länk
Log Analytics har stöd för flera exporterar metoder:

- Excel: Spara resultatet som en CSV-fil.
- Power BI: Exportera resultaten till Power BI. Se [importera Azure Monitor log-data till Power BI](../../azure-monitor/platform/powerbi.md) mer information.
- Dela en länk: Själva frågan kan delas som en länk som sedan kan skickas och körs av andra användare som har åtkomst till samma arbetsyta.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [skriva frågor för Azure Monitor log](get-started-queries.md).
