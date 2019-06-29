---
title: Translator Text API ordlista exempel metod
titlesuffix: Azure Cognitive Services
description: Använd exempel för Translator Text API ordlista-metoden.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: e4665157803409b884c3333d9a3514403e5630bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435109"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Translator Text API 3.0: Ordlisteexempel

Innehåller exempel som visar hur termer i ordlistan används i kontexten. Den här åtgärden används tillsammans med [ordlista lookup](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL för begäran

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Begäranparametrar

Parametrarna som skickades mot frågesträngen är:

<table width="100%">
  <th width="20%">Frågeparameter</th>
  <th>Beskrivning</th>
  <tr>
    <td>API-versionen</td>
    <td>*Obligatoriska parametern*.<br/>Versionen av API: et som begärs av klienten. Värdet måste vara `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Obligatoriska parametern*.<br/>Anger språket i indatatexten. Källspråk måste vara något av de [språk som stöds](./v3-0-languages.md) ingår i den `dictionary` omfång.</td>
  </tr>
  <tr>
    <td>till</td>
    <td>*Obligatoriska parametern*.<br/>Anger språket i utdata texten. Målspråket som måste vara något av de [språk som stöds](./v3-0-languages.md) ingår i den `dictionary` omfång.</td>
  </tr>
</table>

Begärandehuvuden är:

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>Rubriker för autentisering</td>
    <td><em>Nödvändiga begärandehuvudet</em>.<br/>Se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">tillgängliga alternativ för autentisering</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Anger innehållstypen för nyttolasten. Möjliga värden är: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Nödvändiga begärandehuvudet*.<br/>Längden på det begärda innehållet.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Valfritt*.<br/>En klientgenererade GUID för unik identifiering på begäran. Du kan utelämna den här rubriken om du inkluderar trace-ID i frågesträngen med hjälp av en frågeparameter som heter `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement är ett JSON-objekt med följande egenskaper:

  * `Text`: En sträng som anger perioden för sökning. Detta bör vara värdet för en `normalizedText` från tillbaka-översättningar av ett tidigare [ordlista lookup](./v3-0-dictionary-lookup.md) begäran. Det kan också vara värdet för den `normalizedSource` fält.

  * `Translation`: En sträng som anger den översatta texten som tidigare returnerats av den [ordlista lookup](./v3-0-dictionary-lookup.md) igen. Detta bör vara värdet från den `normalizedTarget` i den `translations` lista över de [ordlista lookup](./v3-0-dictionary-lookup.md) svar. Exempel för specifika källa / mål-ordpar returnerar tjänsten.

Ett exempel är:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överskrida 100 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstext

Ett lyckat svar är en JSON-matris med ett resultat för varje sträng i Indatamatrisen. En resultatobjektet innehåller följande egenskaper:

  * `normalizedSource`: En sträng som ger formuläret normaliserade källa har löpt ut. I allmänhet bör detta vara identiskt med värdet för den `Text` fältet med det matchande lista indexet i brödtexten i begäran.
    
  * `normalizedTarget`: En sträng som ger formuläret normaliserade mål har löpt ut. I allmänhet bör detta vara identiskt med värdet för den `Translation` fältet med det matchande lista indexet i brödtexten i begäran.
  
  * `examples`: En lista med exempel (källa perioden, måltermen) par. Varje element i listan är ett objekt med följande egenskaper:

    * `sourcePrefix`: Strängen att sammanfoga _innan_ värdet för `sourceTerm` för att bilda ett komplett exempel. Lägg inte till ett blanksteg eftersom det redan finns det när det ska vara. Det här värdet kan vara en tom sträng.

    * `sourceTerm`: En sträng som motsvarar den faktiska termen söktes. Strängen läggs till med `sourcePrefix` och `sourceSuffix` för att skapa det fullständiga exemplet. Dess värde är avgränsat, så den kan markeras i ett användargränssnitt, t.ex. genom att fetstil den.

    * `sourceSuffix`: Strängen att sammanfoga _när_ värdet för `sourceTerm` för att bilda ett komplett exempel. Lägg inte till ett blanksteg eftersom det redan finns det när det ska vara. Det här värdet kan vara en tom sträng.

    * `targetPrefix`: En sträng som liknar `sourcePrefix` men för målet.

    * `targetTerm`: En sträng som liknar `sourceTerm` men för målet.

    * `targetSuffix`: En sträng som liknar `sourceSuffix` men för målet.

    > [!NOTE]
    > Om det finns inga exempel i ordlistan, svaret är 200 (OK) men `examples` listan är en tom lista.

## <a name="examples"></a>Exempel

Det här exemplet visar hur du leta upp exempel för paret består av engelska termen `fly` och dess spanska translation `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Svarstexten (förkortat för tydlighetens skull) är:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
