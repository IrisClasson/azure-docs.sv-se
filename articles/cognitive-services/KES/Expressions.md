---
title: Strukturerade frågeuttryck - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder strukturerade frågeuttryck i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815115"
---
# <a name="structured-query-expression"></a>Strukturerade frågeuttryck

Ett strukturerade frågeuttryck anger en uppsättning åtgärder som ska utvärderas mot dataIndex.  Den består av attributet frågeuttryck och funktioner på högre nivå.  Använd den [ *utvärdera* ](evaluateMethod.md) metod för att beräkna de objekt som matchar uttrycket.  Följande är ett exempel från domänen akademiska publikationer som returnerar publikationer som skapats av Jaime Teevan sedan året 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Strukturerade frågeuttryck kan hämtas från [ *tolka* ](interpretMethod.md) begäranden, där semantiska utdata från varje tolkning är ett strukturerade frågeuttryck som returnerar index-objekt som matchar den fråga med naturligt språk.  Du kan också kan de vara manuellt författade med den syntax som beskrivs i det här avsnittet.

## <a name="attribute-query-expression"></a>Attributet frågeuttryck

Ett attribut frågeuttryck identifierar en uppsättning objekt baserat på matchning mot ett specifikt attribut.  Olika matchande åtgärder stöds, beroende på typ av attribut och indexerade åtgärd som angetts i den [schemat](SchemaFormat.md):

| Typ | Åtgärd | Exempel |
|------|-------------|------------|
| String | är lika med | Title='latent semantic analysis'  (canonical + synonyms) |
| String | är lika med | Author.Name=='susan t dumais (canonical endast)|
| String | starts_with | Title='latent s'... |
| Double-Int32/Int64 | är lika med | År = 2000 |
| Double-Int32/Int64 | starts_with | År = ”20”... (ett decimalvärde som börjar med ”20”) |
| Double-Int32/Int64 | is_between | År&lt;2000 <br/> År&lt;= 2000 <br/> År&gt;2000 <br/> År&gt;= 2000 <br/> Year=[2010,2012) *(innehåller endast vänstra gränsens värde: 2010, 2011)* <br/> År = [2000,2012] *(omfattar både gränsvärden: 2010, 2011, 2012)* |
| Date | är lika med | BirthDate='1984-05-14' |
| Date | is_between | Födelsedatumet&lt;=' 2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | är lika med | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Till exempel uttrycket ”Title = 'latent s'...” matchar alla akademiska publikationer vars titeln börjar med strängen ”latent s”.  För att utvärdera det här uttrycket måste attributet rubrik ange ”starts_with”-åtgärden i schemat som används för att skapa indexet.

För attribut med associerade synonymer kan ett frågeuttryck Ange objekt vars canonical värdet matchar en given sträng med ”==” operator eller objekt där någon av dess canonical/synonymen värden matchar med operatorn ”=”.  Kräver båda operatorn ”lika med” anges i attributdefinitionen.


## <a name="functions"></a>Functions

Det finns en inbyggd uppsättning funktioner som tillåter konstruktion av mer sofistikerade frågeuttryck från grundläggande attributet frågor.

### <a name="and-function"></a>Och fungerar

`And(expr1, expr2)`

Returnerar skärningspunkten för två inkommande frågeuttryck.

I följande exempel returneras akademiska publikationer som publicerats i år 2000 om Informationshämtning:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Eller funktion

`Or(expr1, expr2)`

Returnerar unionen av två inkommande frågeuttryck.

I följande exempel returneras akademiska publikationer som publicerats i år 2000 om informationshämtning eller användaren modellering:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Sammansatt funktion

`Composite(expr)`

Returnerar ett uttryck som innehåller ett uttryck som inre består av frågor mot underordnade attribut för ett gemensamt sammansatta attribut.  Inkapslingen kräver sammansatta attributet för alla matchande dataobjektet har minst ett värde som uppfyller det inre uttrycket individuellt.  Observera att ett frågeuttryck på underordnade attribut för en sammansatt attributet måste vara inkapslade funktionen Composite() innan den kan kombineras med andra frågeuttryck.

Följande uttryck returnerar till exempel akademiska publikationer efter ”harry shum” medan han var på ”microsoft”:

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Följande uttryck returnerar å andra sidan akademiska publikationer där en av författarna är ”harry shum” och en av anknytningar är ”microsoft”:

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
