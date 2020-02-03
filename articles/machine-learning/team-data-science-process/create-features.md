---
title: Funktionstekniker i datavetenskap - Team Data Science Process
description: Förklarar syftet funktionsframställning och innehåller exempel på dess roll i processen för data förbättring av machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721140"
---
# <a name="feature-engineering-in-data-science"></a>Funktionstekniker i datavetenskap
Den här artikeln förklarar syftet funktionsframställning och innehåller exempel på dess roll i processen för data förbättring av machine learning. I exemplen som används för att illustrera denna process hämtas från Azure Machine Learning Studio. 

Den här uppgiften är ett steg i [TDSP (Team data science process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Funktionen engineering försöker ökar förutsägande learning-algoritmer genom att skapa funktioner från rådata som underlättar learning processen. Teknikerna och valet av funktioner är en del av TDSP som beskrivs i [livs cykeln för team data science process?](overview.md) Funktions teknik och urval är delar av steget **utveckla funktioner** i TDSP. 

* **funktions teknik**: den här processen försöker skapa ytterligare relevanta funktioner från befintliga obearbetade funktioner i datan och öka den förutsägande kraften hos inlärnings algoritmen.
* **Val av funktion**: den här processen väljer en nyckel del av de ursprungliga data funktionerna i ett försök att minska inlärnings problemets dimensionalitet.

Normalt **används funktionerna först** för att generera ytterligare funktioner och sedan utförs **funktions urvalet** för att eliminera irrelevanta, redundanta eller mycket korrelerade funktioner.

Träningsdata som används vid maskininlärning kan ofta förbättras extrahering av funktioner från rådata som samlas in. Ett exempel på en bakåtkompilerade funktion i samband med att lära dig hur du klassificerar bilder av handskriven tecken är skapandet av lite densitet kartan konstrueras från rådata bitars distributionsdata. Den här kartan kan hitta kanter tecknen mer effektivt än att bara använda raw distributionen direkt.

För att skapa funktioner för data i specifika miljöer finns i följande artiklar:

* [Skapa funktioner för data i SQL Server](create-features-sql-server.md)
* [Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Skapa funktioner från dina data – funktionstekniker
Träningsdata består av en matris bestående av exempel (poster eller observationer som lagras i rader) som har en uppsättning funktioner (variabler eller fält som lagrats i kolumner). Funktioner som anges i den experimentella designen förväntas beskriva mönster i data. Även om många av fälten rådata kan inkluderas direkt i den valda funktionsuppsättning som används för att träna en modell, men det är ofta fallet att extrafunktioner (bakåtkompilerade) måste konstrueras från funktionerna i rådata för att generera en förbättrad utbildning-datauppsättning.

Vilken typ av funktioner ska skapas för att förbättra datauppsättningen när träna en modell? Bakåtkompilerade funktioner som förbättrar utbildningen ger information som särskiljer bättre mönster i data. De nya funktionerna förväntas innehåller ytterligare information som inte är tydligt avbildade eller enkelt tydligt i den ursprungliga eller en befintlig funktionsuppsättningen. Men den här processen är ungefär en bild. Ljud-och produktiva kräver ofta vissa expertis.

När du börjar med Azure Machine Learning är det enklast att förstå själva processen concretely med hjälp av exemplen i Studio. Två exempel visas här:

* Ett Regressions exempel som [förutsäger antalet cykel hyror](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) i ett övervakat experiment där mål värden är kända
* Ett exempel på en text utvinnings klassificering som använder [funktionen hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exempel 1: Lägg till den temporala funktioner för en regressionsmodell
Vi använder experimentet "prognoser för efter frågan av cyklar" i Azure Machine Learning Studio (klassisk) för att demonstrera funktioner för en Regressions uppgift. Målet med det här experimentet är att förutse behovet av cyklar, det vill säga antalet uthyrda cyklar inom en viss månad/dag/timme. Datauppsättningen ”cykel hyra cykeluthyrningsdata från datauppsättningen” används som indata rådata. Den här datauppsättningen är baserad på verkliga data från kapital Bikeshare företaget som underhåller ett cykel hyra nätverk i Washington DC i USA. Datauppsättningen representerar antalet uthyrda cyklar inom en viss timme per dag under år 2011 och år 2012 och innehåller 17379 rader och 17 kolumner. Rå funktionsuppsättningen innehåller väderförhållanden (temperatur/fuktighet/vindhastighet) och vilken typ av dagen (helgdag/veckodag). Fältet att Förutsäg är antalet CNT, som representerar cykel hyrarna inom en angiven timme och vilka sträcker sig från 1 till 977.

Med målet att konstruera effektiva funktioner i träningsdata fyra regression modeller skapas med samma algoritm men med datauppsättningar för fyra olika utbildning. De fyra datauppsättningarna representerar samma inkommande rådata, men med ett större antal funktioner. Dessa funktioner är grupperade i fyra kategorier:

1. A = väder + helgdag + veckodag + helgen funktioner för den förväntade dagen
2. B = antal cyklar som har hyrs i var och en av de senaste 12 timmarna
3. C = antal cyklar som har hyrs i var och en av de senaste 12 dagarna i en och samma timme
4. D = antal cyklar som har hyrs i var och en av de föregående 12 veckorna på en och samma timme och samma dag

Förutom funktionen set A, som redan finns i den ursprungliga rådata, skapas de andra tre uppsättningarna med funktioner via funktionen tekniska processen. Funktions uppsättning B fångar upp senaste efter frågan för cyklarna. Funktionen Ange C insamlingar behovet av cyklar på en viss timme. D insamlingar behovet av cyklar funktionsuppsättning på viss timme och viss dag i veckan. De fyra datauppsättningar för utbildning varje innehåller funktionen uppsättning A, A + B, A + B och C och A + B + C + D.

I Azure Machine Learning-experiment bildas dessa fyra datauppsättningar för utbildning via fyra grenar från förbearbetade datauppsättningen för indata. Förutom grenen längst till vänster innehåller var och en av dessa grenar en modul för att [köra R-skript](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) där de härledda funktionerna (funktions uppsättning B, C och D) skapas och läggs till i den importerade data uppsättningen. Följande bild visar R-skript som används för att skapa funktionsuppsättningen B i andra vänstra grenen.

![Skapa funktioner](./media/create-features/addFeature-Rscripts.png)

En jämförelse av de fyra modellerna prestandaresultat sammanfattas i tabellen nedan: 

![jämförelse av resultat](./media/create-features/result1.png)

Bästa resultat visas för funktionerna A + B + C. Fel frekvensen minskar när ytterligare funktions uppsättning ingår i tränings data. Verifierar antagandet att funktionsuppsättningen B, C ger ytterligare relevant information för aktiviteten regression. Men att lägga till funktionen D verkar inte ger någon ytterligare sänkning Felfrekvensen.

## <a name="example2"></a>Exempel 2: skapa funktioner i text utvinning
Funktionsframställning används ofta i uppgifter som rör text-utvinning, till exempel dokument klassificering och sentiment analys. Till exempel när du vill klassificera dokument i flera kategorier, är en typisk antagandet att word/fraser som ingår i en kategori för doc mindre sannolikt kan förekomma i en annan doc-kategori. Frekvensen för ord/fraser distribution kan med andra ord att beskriva olika kategorierna. I text-utvinning program, eftersom enskilda delar av text-innehållet är vanligtvis indata, behövs funktionen tekniska processen för att skapa funktioner som involverar ord eller en viss fras frekvenser.

För att uppnå den här uppgiften används en teknik som kallas **funktion-hash** för att effektivt aktivera godtycklig text funktioner i index. I stället för att koppla varje text-funktionen (ord/fraser) till en viss index, den här metoden fungerar med hjälp av en hash-funktionen till funktioner och direkt med sina hash-värden som index i förväg.

I Azure Machine Learning finns en modul för [hashing av funktioner](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) som skapar ord/fras-funktioner bekvämt. Följande bild visar ett exempel på hur du använder den här modulen. Den inkommande datauppsättningen innehåller två kolumner: boken omdömet mellan 1 och 5, och det faktiska granska innehållet. Målet med den här modulens [hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) -modul är att hämta en massa nya funktioner som visar förekomst frekvensen för motsvarande Word-/phrase i en viss bok granskning. Utför följande steg för att använda den här modulen:

* Börja med att välja den kolumn som innehåller indatatexten (”Col2” i det här exemplet).
* Andra värdet ”Hashing bitsize” 8, vilket innebär att 2 ^ 8 = 256-funktioner kommer att skapas. Word/fas i all text ska kodas till 256 index. Parametern ”Hashing bitsize” mellan 1 och 31. Ord / fraser används är mindre troligt att kodas till samma index om att den ska vara ett större antal.
* Det tredje värdet parametern ”N-gram” 2. Det här värdet får förekomsten frekvensen för unigrams (en funktion för varje enstaka ord) och bigrams (en funktion för varje par av intilliggande ord) från den inmatade texten. Parametern ”N-gram” mellan 0 och 10, som anger det maximala antalet sekventiella ord som ska ingå i en funktion.  

![”Funktion Hashing” modul](./media/create-features/feature-Hashing1.png)

Följande bild visar vad de här nya funktionen ser ut.

![”Funktion Hashing” exempel](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Sammanfattning
Bakåtkompilerade och valda funktioner öka effektiviteten för utbildning-processen, som försöker att extrahera den viktiga informationen i data. De kan även förbättra kraften hos dessa modeller att klassificera indata korrekt och att förutsäga resultat av intresse mer kraftigare. Funktioner och egenskapsval kan också kombinera för att göra utbildningsresurser mer beräkningsmässigt tractable. Detta sker genom bättre och minskar antalet funktioner som behövs för att kalibrera eller tränar en modell. De funktioner som valts för att träna modellen är matematiskt sett en minimal uppsättning oberoende variabler som förklarar mönster i data och förutsäga resultat har.

Det är inte alltid nödvändigtvis för val av teknik eller funktion av funktioner. Om det behövs eller inte beror på informationen för hand eller som samlas in, den algoritm som valts och syftet med experimentet.

