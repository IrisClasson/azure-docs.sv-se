---
title: Vanliga frågor om Custom Speech Service på Azure | Microsoft Docs
description: Här är några svar på de mest populära frågorna om Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: 88486ec9d1ca11d25ca31ca0abb4a34509d19a27
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228043"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Vanliga och frågor svar om Custom Speech Service

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)] 

Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga Custom Speech Service community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) och [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Generell

**Fråga**: Hur vet jag när bearbetningen av min datauppsättning eller en modell är klar?

**Svar**: Status för den modellen eller en uppsättning data i tabellen är för närvarande bara vill veta.
När bearbetningen är klar blir statusen ”klar”.
Vi arbetar på förbättrade metoder för kommunikation Bearbetningsstatus, till exempel e-postmeddelande.

**Fråga**: Kan jag skapa mer än en modell i taget?

**Svar**: Det finns ingen gräns för hur många modeller finns i samlingen, men endast en kan skapas i taget på varje sida.
Till exempel starta du inte en språk modell processen om det finns för närvarande en språkmodell i processen steg.
Du kan dock ha en akustisk modell och en språkmodell bearbetning på samma gång. 

**Fråga**: Jag insåg att jag har gjort ett misstag. Hur avbryter mitt dataimporten jag eller modellera skapa pågående? 

**Svar**: För närvarande återställa du inte en akustiska eller språk anpassning process.
Importerade data kan tas bort när importen är klar

**Fråga**: Vad är skillnaden mellan sökningen & diktering modeller och Konversationsanpassade modeller?

**Svar**: Det finns två grundläggande akustiska och Språkmodeller att välja bland i Custom Speech Service.
sökfrågor eller diktering. Microsoft Conversational AM är lämpligt för att känna igen tal som sägs i ett konversationsanpassade format.
Den här typen av tal är vanligtvis riktat mot en annan person som i call Center eller möten.

**Fråga**: Kan jag uppdatera min befintliga modellen (model stapling)?

**Svar**: Vi erbjuder inte kan uppdatera en befintlig modell med nya data.
Om du har en ny datauppsättning och du vill anpassa en befintlig modell anpassa du igen den med nya data och gamla datauppsättningen som du använde.
De gamla och nya uppgifter som måste kombineras i en enda ZIP (om det är akustiska data) eller en txt-fil om den är språk data en gång anpassning görs nya uppdaterade modellen måste vara ta bort distribuerade att hämta en ny slutpunkt

**Fråga**: Vad händer om jag behöver högre samtidighet än standardvärdet. 

**Svar**: Du kan skala upp din modell i steg om 5 samtidiga begäranden som vi kallar Skalningsenheter. Varje skalningsenhet garanterar att din modell kan bearbeta 5 ljudström samtidigt. Du kan köpa till 100 skalningsenheter (eller 500 samtidiga begäranden).

Kontakta oss om du behöver högre än så.

**Fråga**: Kan jag hämta Mina modellen och köra den lokalt?

**Svar**: Vi gör inte modeller för att hämtas och köras lokalt.

**Fråga**: Loggas Mina begäranden

**Svar**: Du kan välja när du skapar en distribution för att stänga av spårning, då ingen ljud eller avskrifter kommer att loggas. Annars är begäranden vanligtvis inloggad i Azure säker lagring. Om du har fler sekretessen som förhindrar med Custom Speech Service Kontakta oss.

## <a name="importing-data"></a>Importera Data

**Fråga**: Vad är gränsen för storleken på datauppsättningen? Varför? 

**Svar**: Den aktuella gränsen för en datauppsättning är 2 GB, ladda upp på grund av begränsning på storleken på en fil för HTTP. 

**Fråga**: Kan jag zip min textfiler för att ladda upp en större textfil? 

**Svar**: Nej, för närvarande bara okomprimerade textfiler tillåts.

**Fråga**: Rapporten data säger det fanns misslyckade yttranden. Är detta ett problem?

**Svar**: Om bara ett par yttranden kunde importeras, men detta är inte ett problem.
Om det stora flertalet yttranden i en språkdata eller språk data (t.ex. > 95%) har importerats, kan datauppsättningen vara användbar. Vi rekommenderar dock att du försöker förstå varför talade misslyckades och åtgärda problem.
De vanligaste problem, till exempel formateringsfel, är enkla att åtgärda. 

## <a name="creating-am"></a>Skapa AM

**Fråga**: Hur mycket akustiska data behöver jag?

**Svar**: Vi rekommenderar att du börjar med 30 minuter att en timmes akustiska data

**Fråga**: Vilken typ av data ska jag samla in?

**Svar**: Du bör samla in data som ligger så nära programmet scenario och användningsfall som möjligt.
Det innebär att insamling av data ska matcha målprogrammet och användare när det gäller enheter eller enheter, miljöer och typer av talare. I allmänhet du ska samla in data från som bred olika talare som möjligt. 

**Fråga**: Hur ska jag samlar in det? 

**Svar**: Du kan skapa ett fristående program för insamling av data eller använda en del av hylla ljudinspelning programmet.
Du kan också skapa en version av programmet som loggar ljuddata och används den. 

**Fråga**: Behöver jag transkribera anpassning data mig? 

**Svar**: Data måste vara transkriberas. Du kan transkribera själv eller använda en professionell avskrift-tjänst. Vissa av dessa journalanteckningarna för användning och andra använder gemensamt skapade. Vi rekommenderar också kan en avskrift tjänst på begäran.

**Fråga**: Hur lång tid tar det för att skapa en anpassad akustisk modell?

**Svar**: Bearbetningstid för att skapa en anpassad akustisk modell är ungefär densamma som längden på akustiska datauppsättningen.
Därför en anpassad akustisk modell som skapats från en fem timme datauppsättningen tar cirka fem timmar att bearbeta. 

## <a name="offline-testing"></a>Offlinetestning

**Fråga**: Kan jag utföra offline testning av min anpassad akustisk modell med hjälp av en anpassad språkmodell?

**Svar**: Ja, du bara välja modellen med anpassat språk för i listrutan när du ställer in offline testet

**Fråga**: Kan jag utföra offline testning av mina anpassade språkmodell med hjälp av en anpassad akustisk modell?

**Svar**: Ja, markera bara anpassad akustisk modell i den nedrullningsbara menyn när du ställer in offline testet.

**Fråga**: Vad är ordet Felfrekvens och hur beräknas den?

**Svar**: Felfrekvens för Word är utvärderingen mått för taligenkänning. Räknas den som det totala antalet fel, vilket innefattar tillägg, borttagningar och ersättningar, dividerat med det totala antalet ord i referens avskrift.

**Fråga**: Jag nu vet testresultaten i Mina anpassade modellen, är det ett bra eller dålig tal?

**Svar**: Resultaten visar en jämförelse mellan baslinje och de som du anpassat.
Du bör syfta till att Microsoft baslinje-modellen för att göra anpassningen lönar

**Fråga**: Hur jag ta reda på WER av de grundläggande modellerna så att jag kan se om det var improvement? 

**Svar**: Resultaten av offline visa riktighet baslinje riktighet anpassade modellen och förbättring över baslinje

## <a name="creating-lm"></a>Skapa LM

**Fråga**: Hur mycket textdata behöver jag att ladda upp?

**Svar**: Detta är en fråga som har svårt att ge ett exakt svar, som det beror på hur olika ordförråd och fraser som används i ditt program är från från språkmodeller. För alla nya ord är det praktiskt att ange så många exempel som möjligt av användningen av orden. För vanliga fraser som används i ditt program, är inklusive de på language-data också användbart eftersom det anger att lyssna efter även dessa villkor.
Det är vanligt att ha minst 100 och vanligtvis flera hundra yttranden i datauppsättningen språk eller mer.
Även om vissa typer * av frågor som förväntas vara vanligare än andra, du kan infoga flera kopior av de vanliga frågorna i datauppsättningen.

**Fråga**: Kan jag bara ladda upp en lista över ord?

**Svar**: Ladda upp en lista över ord hämta orden i i ordförråd men inte lära systemet hur orden normalt används.
Genom att tillhandahålla fullständigt eller partiellt yttranden (meningar eller satser saker som användare kan komma att säga) språkmodellen lära dig nya ord och hur de används. Anpassade språkmodellen är bra inte bara för att hämta nya ord i systemet, men också för att ändra sannolikheten för kända ord för ditt program. Att tillhandahålla fullständig yttranden hjälper systemet lära sig. 

-----

 * [Översikt](cognitive-services-custom-speech-home.md)
 * [Igång](cognitive-services-custom-speech-get-started.md)
 * [Ordlista](cognitive-services-custom-speech-glossary.md)
