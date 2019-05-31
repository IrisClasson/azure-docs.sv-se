---
title: API för textöversättning transkribera metod
titlesuffix: Azure Cognitive Services
description: Använd metoden transkribera till Translator Text API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-pawal
ms.openlocfilehash: 138a04cca1bbbaf7b59f628f491a5f13d73fb6f7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387394"
---
# <a name="translator-text-api-30-transliterate"></a>Translator Text API 3.0: Transliterate

Konverterar text på ett språk från ett skript till ett annat skript.

## <a name="request-url"></a>URL för begäran

Skicka en `POST` begäran om att:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
    <td>language</td>
    <td>*Obligatoriska parametern*.<br/>Anger språket i texten som ska konverteras från ett skript till en annan. Möjliga språk visas i den `transliteration` omfång hämtas genom att fråga service för dess [språk som stöds](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Obligatoriska parametern*.<br/>Anger det skript som används av indatatexten. Leta upp [språk som stöds](./v3-0-languages.md) med hjälp av den `transliteration` omfattning, att hitta inkommande skript är tillgängliga för det valda språket.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Obligatoriska parametern*.<br/>Anger det utgående skriptet. Leta upp [språk som stöds](./v3-0-languages.md) med hjälp av den `transliteration` omfattning, att hitta utdata skript är tillgängliga för den valda kombinationen av språk och indata-skript.</td>
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
    <td>*Valfritt*.<br/>En klientgenererade GUID för unik identifiering på begäran. Observera att du kan utelämna den här rubriken om du inkluderar trace-ID i frågesträngen med hjälp av en frågeparameter som heter `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Begärandetext

Brödtexten i begäran är en JSON-matris. Varje matriselement är ett JSON-objekt med en strängegenskap med namnet `Text`, som representerar strängen som ska konverteras.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Följande begränsningar gäller:

* Matrisen kan ha högst 10 element.
* Textvärdet för ett matriselement får inte överskrida 1 000 tecken inklusive blanksteg.
* Hela texten i begäran får inte överskrida 5 000 tecken inklusive blanksteg.

## <a name="response-body"></a>Svarstext

Ett lyckat svar är en JSON-matris med ett resultat för varje element i Indatamatrisen. En resultatobjektet innehåller följande egenskaper:

  * `text`: En sträng som är resultatet från konverteringen Indatasträngen till det utgående skriptet.
  
  * `script`: En sträng som anger skript som används i utdata.

Ett exempel JSON-svar är:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Svarshuvuden

<table width="100%">
  <th width="20%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>X-RequestId</td>
    <td>Värde som genereras av tjänsten för att identifiera begäran. Den används för felsökning.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Svarsstatuskoder

Här följer möjliga HTTP-statuskoder som returnerar en begäran. 

<table width="100%">
  <th width="20%">Statuskod</th>
  <th>Beskrivning</th>
  <tr>
    <td>200</td>
    <td>Lyckades.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>En av frågeparametrarna är saknas eller är inte giltig. Korrigera parametrarna innan du försöker igen.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Det gick inte att autentisera begäran. Kontrollera att autentiseringsuppgifterna är angivna och giltiga.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Begäran har inte behörighet. Finns information om felmeddelandet. Detta innebär ofta att alla kostnadsfria översättningar som medföljer en utvärderingsprenumeration har förbrukats.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Servern avvisade begäran eftersom klienten har överskridit begärandebegränsningar.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Det uppstod ett oväntat fel. Om felet kvarstår bör du rapportera det med: datum och tid för fel, begärande-ID från svarshuvud `X-RequestId`, och klient-ID från begärandehuvudet `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servern är inte tillgänglig för tillfället. Gör om begäran. Om felet kvarstår bör du rapportera det med: datum och tid för fel, begärande-ID från svarshuvud `X-RequestId`, och klient-ID från begärandehuvudet `X-ClientTraceId`.</td>
  </tr>
</table> 

Om ett fel inträffar, returneras också en JSON-felsvar i begäran. Felkoden är en 6-siffrig nummer kombinera 3-siffriga HTTP-statuskoden följt av ett 3-siffriga tal till ytterligare kategorisera felet. Vanliga felkoder finns på den [v3 Translator Text API-referenssida](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exempel

I följande exempel visas hur du konverterar två japanska strängar till Romanized japanska.

# <a name="curltabcurl"></a>[curl](#tab/curl)

JSON-nyttolast för begäran i det här exemplet:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Om du använder cURL i ett kommandoradsfönster som inte stöder Unicode-tecken, vidta följande JSON-nyttolasten och spara den till en fil med namnet `request.txt`. Glöm inte att spara filen med `UTF-8` kodning.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
