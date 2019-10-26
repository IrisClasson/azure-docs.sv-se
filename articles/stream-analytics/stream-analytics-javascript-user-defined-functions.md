---
title: Användardefinierade funktioner i Azure Stream Analytics JavaScript
description: I den här självstudien får utföra avancerade frågor med användardefinierade JavaScript-funktioner
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: 8a26e369783da8b59837e669dcd45a338ce82722
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935004"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Självstudier: Användardefinierade funktioner i Azure Stream Analytics JavaScript
 
Azure Stream Analytics stödjer användardefinierade JavaScript-funktioner. Med den omfattande uppsättningen av **String**-, **RegExp**-, **Math**-, **Array**- och **Date**-metoder som JavaScript erbjuder blir det enklare att skapa komplexa datatransformationer med Stream Analytics-jobb.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Definiera en användardefinierad JavaScript-funktion
> * Lägga till funktionen i portalen
> * Definiera en fråga som kör funktionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="javascript-user-defined-functions"></a>Användardefinierade JavaScript-funktioner
Användardefinierade JavaScript-funktioner stödjer tillståndslösa, skalära funktioner för beräkning som inte kräver extern anslutning. Returvärdet för en funktion kan endast vara ett skalärvärde (enkelt). När du lägger till en användardefinierad JavaScript-funktion i ett jobb kan använda funktionen var som helst i frågan, som en inbyggd skalärfunktion.

Här följer några scenarier där användardefinierade JavaScript-funktioner kan vara användbara:
* Tolka och manipulera strängar som har funktioner med reguljära uttryck, till exempel **Regexp_Replace()** och **Regexp_Extract()**
* Avkoda och koda data, till exempel konvertering av binärt format till hexadecimalt
* Utföra matematiska beräkningar med JavaScripts **Math**-funktioner
* Utför åtgärder i matrisen som sortera, koppling, söka och fylla

Här följer några saker som du inte kan göra med användardefinierade JavaScript-funktioner i Stream Analytics:
* Anrop till externa REST-slutpunkter, till exempel omvänd IP-sökning eller hämta referensdata från externa källor
* Utför anpassade serialisering eller avserialisering av händelseformat på indata/utdata
* Skapa anpassade samlingar

Även om funktioner som **Date.GetDate()** eller **Math.random()** inte blockeras i definitionen av funktioner du bör undvika att använda dem. Dessa funktioner returnerar **inte** samma resultat varje gång du anropar dem, och Azure Stream Analytics-tjänsten sparar inte funktionsanrop och returnerade resultat. Om en funktion returnerar olika resultat för samma händelser, kan inte repeterbarhet garanteras när du eller Stream Analytics-tjänsten startar ett jobb.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Lägga till en användardefinierad JavaScript-funktion i Azure Portal
Följ dessa steg om du vill skapa en enkel användardefinierad JavaScript-funktion under ett befintligt Stream Analytics jobb:

> [!NOTE]
> De här stegen fungerar på Stream Analytics jobb som kon figurer ATS för att köras i molnet. Om Stream Analytics jobbet är konfigurerat för att köras på Azure IoT Edge ska du i stället använda Visual Studio och [skriva den användardefinierade funktionen med C#hjälp av ](stream-analytics-edge-csharp-udf.md).

1.  Öppna ditt Stream Analytics-jobb på Azure Portal.

2. Under rubriken **jobb Topology** väljer du **Functions**. En tom lista över funktioner visas.

3.  Om du vill skapa en ny användardefinierad funktion väljer du **+ Lägg till**.

4.  På bladet **Ny funktion** för **funktionstypen** väljer du **JavaScript**. En standardmall för funktionen visas i redigeraren.

5.  För **UDF-alias** anger du **hex2Int** och ändrar funktionsimplementeringen på följande sätt:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Välj **Spara**. Din funktion visas i listan över funktioner.
7.  Välj den nya **hex2Int**-funktionen och kontrollera definitionen. Alla funktioner har ett **UDF**-prefix som läggs till funktionens alias. Du behöver *ta med prefixet* när du anropar funktionen i Stream Analytics-frågan. I det här fallet måste du anropa **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Anropa en användardefinierad JavaScript-funktion i en fråga

1. I Frågeredigeraren under rubriken **jobb Topology** väljer du **fråga**.
2.  Redigera frågan och anropa sedan den användardefinierade funktionen så här:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Högerklicka på jobbindata för att ladda upp exempeldatafilen.
4.  Om du vill testa frågan väljer du **Test**.


## <a name="supported-javascript-objects"></a>JavaScript-objekt som stöds
Användardefinierade JavaScript-funktioner i Azure Stream Analytics stödjer inbyggda standardobjekt i JavaScript. En lista över de här objekten finns i [Globala objekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics och JavaScript typkonvertering

Det finns skillnader i vilka typer som Stream Analytics frågespråk och JavaScript stödjer. Den här tabellen visar konverteringsmappningar mellan dessa:

Stream Analytics | JavaScript
--- | ---
bigint | Siffra (JavaScript kan bara representera heltal upp till exakt 2^53)
DateTime | Datum (JavaScript stöder endast millisekunder)
double | Tal
nvarchar(MAX) | Sträng
Spela in | Objekt
Matris | Matris
NULL | Null


Här är konverteringarna från JavaScript till Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Tal | Bigint (om talet är avrundat och mellan long.MinValue och long.MaxValue, i annat fall stöds det inte)
Datum | DateTime
Sträng | nvarchar(MAX)
Objekt | Spela in
Matris | Matris
Null, odefinierad | NULL
Annan typ (till exempel en funktion eller fel) | Stöds inte (resulterar i körningsfel)

JavaScript-språket är Skift läges känsligt och Skift läge för objekt fälten i JavaScript-koden måste matcha versaler och gemener i fälten i inkommande data. Observera att jobb med kompatibilitetsnivån 1,0 kommer att konvertera fält från SQL SELECT-instruktionen till gemener. Under kompatibilitetsnivå 1,1 och högre har fält från SELECT-instruktionen samma Skift läge som anges i SQL-frågan.

## <a name="troubleshooting"></a>Felsöka
JavaScript-körningsfel betraktas som allvarliga och exponeras via aktivitetsloggen. För att hämta loggen i Azure Portal går du till jobbet och väljer **aktivitetsloggen**.


## <a name="other-javascript-user-defined-function-patterns"></a>Andra mönster för användardefinierade JavaScript-funktioner

### <a name="write-nested-json-to-output"></a>Skriva kapslad JSON till utdata
Om du har ett bearbetningssteg för uppföljning som använder Stream Analytics-jobbets utdata som indata, och det kräver ett JSON-format, kan du skriva en JSON-sträng till utdata. I nästa exempel anropar den funktionen **JSON.stringify()** för att packa alla namn-/värdepar för indata och sedan skriva dem som ett enda strängvärde i utdata.

**Definiera en användardefinierad JavaScript-funktion:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Exempelfråga:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade.  
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett Stream Analytics-jobb som kör en enkel användardefinierad funktion i JavaScript. Om du vill veta mer om Stream Analytics kan fortsätta att läsa artiklarna om realtidsscenarierna:

> [!div class="nextstepaction"]
> [Attitydanalys för Twitter i realtid i Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
