---
title: Lägg till komplettera automatiskt och förslag i en sökruta
titleSuffix: Azure Cognitive Search
description: Aktivera frågor som är av typ typ i Azure Kognitiv sökning genom att skapa förslag och utforma begär Anden som kompletterar en sökruta med färdiga villkor eller fraser. Du kan också returnera föreslagna matchningar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 2de282da56a40c92eacde84ac913be0ceacf9e2b
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87413025"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Lägg till komplettera automatiskt och förslag till klient program

Sökning efter typ är en vanlig teknik för att förbättra produktiviteten för användarinitierade frågor. I Azure Kognitiv sökning stöds den här upplevelsen genom *Autoavsluta*, som avslutar en term eller fras baserat på inaktuella inleveranser (som slutförs med "Micro" med "Microsoft"). Ett annat formulär är *förslag*: en kort lista med matchande dokument (som returnerar bok titlar med ett ID så att du kan länka till en informations sida). Både Autoavsluta och förslag är predikat på en matchning i indexet. Tjänsten erbjuder inte frågor som returnerar noll resultat.

Om du vill implementera dessa upplevelser i Azure Kognitiv sökning behöver du:

+ En *förslags ställare* på Server delen.
+ En *fråga* som anger [Autoavsluta](https://docs.microsoft.com/rest/api/searchservice/autocomplete) -eller [Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) -API: et för begäran.
+ En *gränssnitts kontroll* som hanterar interaktioner från sökning till typ i klient programmet. Vi rekommenderar att du använder ett befintligt JavaScript-bibliotek för detta ändamål.

I Azure Kognitiv sökning hämtas kompletterade frågor och föreslagna resultat från Sök indexet från valda fält som du har registrerat med en förslags ställare. En förslags ställare är en del av indexet och anger vilka fält som ska tillhandahålla innehåll som antingen fyller i en fråga, föreslår ett resultat eller båda. När indexet skapas och läses in skapas en förslags data struktur internt för att lagra prefix som används för att matcha delar av frågor. För förslag bör du välja lämpliga fält som är unika eller som minst inte upprepas. Mer information finns i [skapa en förslags ställare](index-add-suggesters.md).

Resten av den här artikeln fokuserar på frågor och klient kod. Den använder Java Script och C# för att illustrera viktiga punkter. REST API exempel används för att kortfattat presentera varje åtgärd. Länkar till slut punkts kod exempel finns i [Nästa steg](#next-steps).

## <a name="set-up-a-request"></a>Konfigurera en begäran

Element i en begäran inkluderar en av Sök-som-typ-API: erna, en partiell fråga och en förslags ställare. Följande skript visar komponenter i en begäran med hjälp av Autoavsluta-REST API som exempel.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName** ger dig de förslags känsliga fält som används för att slutföra villkor eller förslag. För förslag i synnerhet bör fält listan bestå av de som erbjuder tydliga val mellan matchande resultat. På en plats som säljer dator spel kan fältet vara spelets rubrik.

**Sök** parametern innehåller den partiella frågan där tecken matas till fråge förfrågan via jQuery-kontrollen för automatisk komplettering. I exemplet ovan är "minecraf" en statisk illustration av vad kontrollen kan ha gått till.

API: erna tillämpar inte minimi kraven på längd på den partiella frågan. Det kan vara så litet som ett enda steg. JQuery komplettera automatiskt innehåller dock en minimilängd. Minst två eller tre tecken är typiska.

Matchningar är i början av en term var som helst i Indatasträngen. Med "Quick Jansson Fox" kommer både Autoavsluta och förslag att matchas på vissa versioner av "The", "Quick", "brun" eller "Fox", men inte i delar av infix-termer som "raden" eller "ox". Dessutom ställer varje matchning över omfånget för underordnade expansionar. En partiell fråga om "Quick br" kommer att matchas på "Quick Jansson" eller "Quick bröd", men varken "brun" eller "bröd" kan vara detsamma om inte "Quick" föregår dem.

### <a name="apis-for-search-as-you-type"></a>API: er för sökning efter typ

Följ dessa länkar för referens sidorna REST och .NET SDK:

+ [Förslag REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Autoavsluta-REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Strukturera ett svar

Svar för Autoavsluta och förslag är vad du kan förväntat dig för mönstret: [Autoavsluta](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) returnerar en lista med villkor, [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) returnerar villkor plus ett dokument-ID så att du kan hämta dokumentet (Använd sökverktygets API för att hämta det [aktuella dokumentet för](https://docs.microsoft.com/rest/api/searchservice/lookup-document) en informations sida).

Svar definieras av parametrarna på begäran. För Autoavsluta ställer du in [**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) för att avgöra om text komplettering sker på en eller två villkor. För förslag bestämmer det fält du väljer innehållet i svaret.

För förslag bör du ytterligare förfina svaret för att undvika dubbletter eller vad som verkar vara orelaterade resultat. Om du vill kontrol lera resultatet inkluderar du fler parametrar i begäran. Följande parametrar gäller för både Autoavsluta och förslag, men är kanske mer nödvändiga för förslag, särskilt när en förslags ställare innehåller flera fält.

| Parameter | Användning |
|-----------|-------|
| **$select** | Om du har flera **sourceFields** i en förslags ställare använder du **$Select** för att välja vilket fält som bidrar med värden ( `$select=GameTitle` ). |
| **searchFields** | Begränsa frågan till vissa fält. |
| **$filter** | Använd matchnings villkor i resultat uppsättningen ( `$filter=Category eq 'ActionAdventure'` ). |
| **$top** | Begränsa resultatet till ett angivet tal ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Lägg till användar interaktions kod

Att automatiskt fylla en frågeterm eller släppa en lista över matchande länkar kräver användar interaktions kod, vanligt vis Java Script, som kan använda förfrågningar från externa källor, t. ex. Autoavsluta-eller förslags frågor mot ett Azure Search kognitivt index.

Även om du kan skriva den här koden internt är det mycket enklare att använda funktioner från befintliga JavaScript-bibliotek. Den här artikeln visar två, en för förslag och en annan för automatisk komplettering. 

+ [Autocomplete-widgeten (JQUERY UI)](https://jqueryui.com/autocomplete/) används i förslags exemplet. Du kan skapa en sökruta och sedan referera till den i en JavaScript-funktion som använder widgeten Autoavsluta. Egenskaperna i widgeten anger källan (en Autoavsluta-eller Suggestions-funktion), minimal längd på angivna tecken innan åtgärden tas och placering.

+ [XDSoft autocomplete-plugin](https://xdsoft.net/jqplugins/autocomplete/) används i exemplet komplettera automatiskt.

Vi använder dessa bibliotek för att bygga sökrutan som stöder både förslag och Autoavsluta. Indata som samlas in i sökrutan är kopplade till förslag och åtgärder för automatisk komplettering.

## <a name="suggestions"></a>Förslag

Det här avsnittet vägleder dig genom en implementering av föreslagna resultat som börjar med Sök Rute definitionen. Den visar också hur och skript som anropar det första biblioteket för Java Script-komplettering som refereras i den här artikeln.

### <a name="create-a-search-box"></a>Skapa en sökruta

Om du antar [JQUERY UI-biblioteket](https://jqueryui.com/autocomplete/) och ett MVC-projekt i C# kan du definiera sökrutan med hjälp av Java Script i filen **index. cshtml** . Biblioteket lägger till interaktionen sökning efter typ i sökrutan genom att göra asynkrona anrop till MVC-kontrollanten för att hämta förslag.

I **index. cshtml** under mappen \Views\Home kan en linje för att skapa en sökruta vara följande:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Det här exemplet är en enkel text ruta med en klass för formatering, ett ID som ska refereras till av Java Script och platshållartext.  

I samma fil bäddar du in Java Script som refererar till sökrutan. Följande funktion anropar det rekommenderade API: t, som begär föreslagna matchnings dokument baserat på indata från delvis term:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

I `source` beskrivs funktionen Komplettera automatiskt i JQUERY UI där du kan hämta listan över objekt som ska visas under sökrutan. Eftersom projektet är ett MVC-projekt anropas funktionen **föreslå** i **HomeController.cs** som innehåller logiken för att returnera fråge förslag. Den här funktionen skickar också några parametrar för att kontrol lera högdagrar, fuzzy Matching och term. JavaScript-API:et för automatisk komplettering lägger till term-parametern.

Det `minLength: 3` säkerställer att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.

### <a name="enable-fuzzy-matching"></a>Aktivera fuzzy-matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan. Redigera avståndet är 1, vilket innebär att det kan finnas en maximal avvikelse på ett av tecknen mellan användarindata och en matchning. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Aktivera markering

Markeringen använder tecken formatet för de tecken i resultatet som motsvarar indatatypen. Om t. ex. den partiella indatamängden är "Micro" visas resultatet **som**mikrosoft, **Micro**-omfattning och så vidare. Markering baseras på parametrarna HighlightPreTag och HighlightPostTag, definierade infogade med förslags funktionen.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Funktionen föreslå

Om du använder C# och ett MVC-program är **HomeController.cs** -filen under katalogen kontrollanter där du kan skapa en klass för föreslagna resultat. I .NET baseras en förslags funktion på [metoden DocumentsOperationsExtensions. föreslå](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

`InitSearch`Metoden skapar en autentiserad HTTP-index-klient till Azure kognitiv sökning-tjänsten. Mer information om .NET SDK finns i [så här använder du Azure kognitiv sökning från ett .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funktionen Suggest (Föreslå) tar två parametrar som bestämmer om träffmarkeringar returneras eller om fuzzy-matchning används utöver sökordsindata. Metoden skapar ett [SuggestParameters-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), som sedan skickas till föreslå API. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

## <a name="autocomplete"></a>Komplettera automatiskt

Hittills har Sök-UX-koden centrerats på förslag. Nästa kodblock visar komplettera automatiskt med funktionen XDSoft jQuery UI komplettera automatiskt och skickar en begäran om automatisk komplettering av Azure-Kognitiv sökning. Som med förslag, i ett C#-program, går kod som stöder användar interaktionen i **index. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funktionen Komplettera automatiskt

Autoavsluta baseras på [metoden DocumentsOperationsExtensions. Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Som med förslag skulle det här kod blocket gå till filen **HomeController.cs** .

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funktionen Autoavsluta tar search term-indatamängden. Metoden skapar ett [AutoCompleteParameters-objekt](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

## <a name="next-steps"></a>Nästa steg

Följ dessa länkar för slut punkt till slut punkts instruktioner eller kod som demonstrerar både sökning efter typ-upplevelser. I båda kod exemplen ingår hybrid implementeringar av förslag och Autoavsluta.

+ [Självstudie: skapa din första app i C# (Lektion 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C#-kod exempel: Azure-Search-dotNet-samples/Create-First-app/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# och Java Script med REST sida vid sida-kod exempel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)