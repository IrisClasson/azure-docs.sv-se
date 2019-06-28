---
title: Translator Text API V2.0
titleSuffix: Azure Cognitive Services
description: Referensdokumentation för V2.0 Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 88503c73e2ca9cf04e64ca3a47793e9b10ca325a
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357822"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Den här versionen av Translator Text API är inaktuell. [Visa dokumentationen för Translator Text API v3](v3-0-reference.md).

Translator Text API V2 kan integreras smidigt i dina program, webbplatser, verktyg eller andra lösningar för att tillhandahålla användarupplevelser för flera språk. Utnyttja branschstandarder, kan den användas på valfri maskinvaruplattform och i alla operativsystem som utför översättningar och andra språk-relaterade åtgärder, till exempel språkidentifiering för text eller text till tal. Klicka här för mer information om Microsoft Translator-API.

## <a name="getting-started"></a>Komma igång
Åtkomst till Translator Text API behöver du [registrera dig för Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Authorization
Alla anrop till Translator Text API krävs en prenumerationsnyckel för att autentisera. API: et stöder tre lägen för autentisering:

- En åtkomsttoken. Använd prenumerationsnyckel som refereras i **steg** 9 för att generera en åtkomsttoken genom att göra en POST-begäran till Auktoriseringstjänsten. Se token service-dokumentationen för mer information. Skicka åtkomsttoken till Translator-tjänsten med hjälp av auktoriseringsrubriken eller `access_token` frågeparameter. Åtkomsttoken är giltig i 10 minuter. Skaffa en ny åtkomsttoken var tionde minut och fortsätt att använda samma åtkomst-token för upprepade begäranden under dessa 10 minuter.
- En prenumerationsnyckel direkt. Skicka din prenumerationsnyckel som ett värde i den `Ocp-Apim-Subscription-Key` rubrik som ingår i din begäran om att Translator-API. I det här läget kan behöver du inte anropa token autentiseringstjänsten för att generera en åtkomsttoken.
- En [flera tjänster Cognitive Services-prenumeration](https://azure.microsoft.com/pricing/details/cognitive-services/). Det här läget kan du använda en enda hemlig nyckel för att autentisera begäranden för flera tjänster. <br/>
När du använder en flera tjänster hemlig nyckel, måste du inkludera två autentiseringshuvuden din förfrågan. Rubriken första skickar den hemliga nyckeln. Andra rubriken anger den region som är associerade med din prenumeration:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Regionen måste anges för flera tjänster Text API-prenumeration. Den region som du väljer är den enda regionen som du kan använda för textöversättning när du använder flera tjänster prenumerationsnyckeln och måste vara i samma region som du valde när du registrerat dig för prenumerationen flera tjänster via Azure portal.

De tillgängliga regionerna är `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, och `westus2`.

Överväg att din prenumerationsnyckel och åtkomst-token som hemligheter som ska vara dold från vyn.

## <a name="profanity-handling"></a>Hantering av olämpligt språk
Normalt behåller tjänsten Translator svordomar som finns i källan i översättningen. Graden av svordomar och kontext som gör ord olämpliga skiljer sig åt mellan kulturer och därmed graden av olämpligt språk på språket som mål kan framhävas eller minskas.

Om du vill undvika svordomar i översättning, oavsett förekomsten av svordomar i källtext, kan du använda svordomar filtrering för de metoder som stöder den. Alternativet kan du välja om du vill se svordomar tagits bort eller markerats med lämpliga taggar eller någon åtgärd krävs. Godkända värden för `ProfanityAction` är `NoAction` (standard), markerat och `Deleted`.


|ProfanityAction    |Åtgärd |Exempel källan (japanska)  |Exempel Translation (på engelska)  |
|:--|:--|:--|:--|
|NoAction   |Standard. Samma som inte ange alternativet. Svordomar skickas från källan till målet.        |彼はジャッカスです。     |Han är en jackass.   |
|Markerad     |Olämpliga ord. ska omges av XML-taggar \<svordomar > och \</profanity >.       |彼はジャッカスです。 |Han är en \<svordomar > jackass\</profanity >.  |
|Borttaget    |Olämpliga ord. tas bort från utdata utan ersättning.     |彼はジャッカスです。 |Han är en.   |

    
## <a name="excluding-content-from-translation"></a>Exkludera innehåll från översättning
Vid översättning av innehåll med taggar som HTML (`contentType=text/html`), kan det vara praktiskt att utesluta translation specifikt innehåll. Du kan använda attributet `class=notranslate` och ange innehåll som ska vara på dess ursprungliga språk. I följande exempel innehållet i först `div` elementet kommer inte att översättas när innehållet i andra `div` elementet kommer att översättas.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Implementeringsanteckningar
Omvandlar en textsträng från ett språk till ett annat.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Returvärdet:** En sträng som representerar den översatta texten.

Om du tidigare använt `AddTranslation` eller `AddTranslationArray` att ange en översättning med en klassificering på 5 eller högre för samma käll-mening `Translate` returnerar endast toppvalet som är tillgänglig i systemet. ”Samma källa mening” innebär att exakt samma (100%-matchning), förutom versaler, blanksteg, taggvärden och skiljetecken i slutet av en mening. Om ingen klassificering lagras med en klassificering på 5 eller senare vara automatisk översättning av Microsoft Translator returnerade resultat.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

string

Innehållstyp för svar: application/xml 

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning    |Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid  |(tom)    |Krävs. Om auktorisering eller Ocp-Apim-Subscription-Key-huvud används, lämna appid fältet tomt eller innehålla en sträng som innehåller ”ägar” + ”” + ”access_token”.|DocumentDB|string|
|text|(tom)   |Krävs. En sträng som representerar text för översättning. Storleken på texten får inte överstiga 10000 tecken.|DocumentDB|string|
|from|(tom)   |Valfri. En sträng som representerar språkkoden för Översättningstext. Till exempel en för engelska.|DocumentDB|string|
|till|(tom) |Krävs. En sträng som representerar språkkoden att översätta text i.|DocumentDB|string|
|contentType|(tom)    |Valfri. Formatet på den text som översätts. Format som stöds är text/plain (standard) och text/html. All HTML-kod måste vara en korrekt strukturerad, fullständig element.|DocumentDB|string|
|category|(tom)   |Valfri. En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är ”Allmänt”.|DocumentDB|string|
|Authorization|(tom)  |Krävs om appid fält eller Ocp-Apim-Subscription-Key-huvud inte har angetts. Autentiseringstoken:  ”Ägar” + ”” + ”access_token”.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om appid fält eller auktoriseringsrubrik inte har angetts.|sidhuvud|string|


### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `TranslateArray` metod för att hämta översättningar för flera källa texter.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Formatet för förfrågans text bör vara följande:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Element i den `TranslateArrayRequest` är:


* `appid`: Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `from`: Valfri. En sträng som representerar språkkoden att översätta text från. Om fältet lämnas tomt innehåller svaret resultatet av automatisk identifiering av språk.
* `options`: Valfri. En `Options` objekt som innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard. Angivna elementen måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller kategorin (domän) för översättningen. Som standard `general`.
    - `ContentType`: Formatet på den text som översätts. Format som stöds är `text/plain` (standard), `text/xml` och `text/html`. All HTML-kod måste vara en korrekt strukturerad, fullständig element.
    - `ProfanityAction`: Anger hur profanities hanteras enligt beskrivningen ovan. Godkända värden för `ProfanityAction` är `NoAction` (standard), `Marked` och `Deleted`.
    - `State`: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
    - `Uri`: Filtrera resultaten av den här URI: N. Standard: `all`.
    - `User`: Filtrera resultaten av den här användaren. Standard: `all`.
* `texts`: Krävs. En matris som innehåller text för översättning. Alla strängar måste vara av samma språk. Summan av alla texter översättas får inte överstiga 10000 tecken. Det maximala antalet matriselement är 2000.
* `to`: Krävs. En sträng som representerar språkkoden att översätta text i.

Valfria element kan utelämnas. Element som är direkt underordnade TranslateArrayRequest måste anges i alfabetisk ordning.

TranslateArray metoden godkänner `application/xml` eller `text/xml` för `Content-Type`.

**Returvärdet:** En `TranslateArrayResponse` matris. Varje `TranslateArrayResponse` har följande element:

* `Error`: Anger ett fel om ett har inträffat. Annars inställt på null.
* `OriginalSentenceLengths`: En matris av heltal som anger längden på varje mening i den ursprungliga källa texten. Längden på matrisen anger hur många meningar.
* `TranslatedText`: Den översatta texten.
* `TranslatedSentenceLengths`: En matris av heltal som anger längden på varje mening i den översatta texten. Längden på matrisen anger hur många meningar.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Returnerar samma innehåll som begäran.

Formatet för svarstexten är som följer.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
Ett lyckat svar innehåller en matris med `TranslateArrayResponse` i formatet som beskrivs ovan.

string

Innehållstyp för svar: application/xml

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Authorization|(tom)) |Krävs om appid fält eller Ocp-Apim-Subscription-Key-huvud inte har angetts. Autentiseringstoken:  ”Ägar” + ”” + ”access_token”.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om appid fält eller auktoriseringsrubrik inte har angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod   |Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret. Vanliga fel är: <ul><li>Matriselement får inte vara tomt</li><li>Ogiltig kategori</li><li>Från språk är ogiltigt</li><li>Språk är ogiltigt</li><li>Förfrågan innehåller för många element</li><li>From-språket stöds inte</li><li>Till språket stöds inte</li><li>Översätt begäran har för mycket data</li><li>HTML har inte rätt format</li><li>För många strängar skickades i översätta begäran</li></ul>|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar egna namn för språk som har skickats in som parameter `languageCodes`, och lokalisering med språket du skickade nationella inställningar.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Begärandetexten innehåller en strängmatris som representerar ISO 639-1 språkkoder att hämta de egna namn för. Exempel:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Returvärdet:** En strängmatris som innehåller namn på språk som stöds av tjänsten Translator lokaliserad till det begärda språket.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En strängmatris som innehåller namn på språk som stöds av tjänsten Translator lokaliserad till det begärda språket.

string

Innehållstyp för svar: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|Nationella inställningar|(tom) |Krävs. En sträng som representerar en kombination av en ISO 639 gemener kultur för två bokstäver kod som är associerad med ett språk och en ISO 3166 två bokstäver versaler subkultur kod för att hitta Språknamnen eller en ISO 639 gemener Kulturkod ensamt.|DocumentDB|string|
|Authorization|(tom)  |Krävs om fältet appid eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om fältet appid eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämta en lista med språkkoder som representerar språk som stöds av tjänsten översättning.  `Translate` och `TranslateArray` kan översätta mellan två av dessa språk.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Returvärdet:** En strängmatris som innehåller språkkoder som stöds av Translator-tjänster.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En strängmatris som innehåller språkkoder som stöds av Translator-tjänster.

string

Innehållstyp för svar: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|Authorization|(tom)  |Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar språk som är tillgängliga för talsyntes.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Returvärdet:** En strängmatris som innehåller språkkoder som stöds för talsyntes av Translator-tjänsten.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En strängmatris som innehåller språkkoder som stöds för talsyntes av Translator-tjänsten.

string

Innehållstyp för svar: application/xml

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|Authorization|(tom)|Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|
 
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401|Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Implementeringsanteckningar
Returnerar en wave eller mp3 dataström textens skickas in som sägs i önskat språk.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Returvärdet:** En wave eller mp3 dataström textens skickas in som sägs i önskat språk.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

binary

Innehållstyp för svar: application/xml 

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|text|(tom)   |Krävs. En sträng som innehåller en mening eller meningar för angivna språket som ska läsas för wave-dataström. Storleken på texten som ska tala får inte överskrida 2 000 tecken.|DocumentDB|string|
|language|(tom)   |Krävs. En sträng som representerar den språkkod som stöds för att tala texten i. Koden måste finnas i listan över koder som returneras från metoden `GetLanguagesForSpeak`.|DocumentDB|string|
|format|(tom)|Valfri. En sträng som anger innehållstypen-ID. För närvarande `audio/wav` och `audio/mp3` är tillgängliga. Standardvärdet är `audio/wav`.|DocumentDB|string|
|options|(tom)    |<ul><li>Valfri. En sträng som anger egenskaperna för syntetiskt tal:<li>`MaxQuality` och `MinSize` är tillgängliga för att ange ljud signaler kvalitet. Med `MaxQuality`, du kan hämta röster med högsta kvalitet och `MinSize`, du kan hämta röster med den minsta storleken. Standardvärdet är `MinSize`.</li><li>`female` och `male` är tillgängliga för att ange den önskade kön för röst. Standardvärdet är `female`. Använder vertikalstreck <code>\|</code> till innehåller flera alternativ. Till exempel `MaxQuality|Male`.</li></li></ul> |DocumentDB|string|
|Authorization|(tom)|Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `Detect` metod för att identifiera språket i en vald typ av text.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Returvärdet:** En sträng som innehåller en två-teckens språkkod för den angivna texten. .

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

string

Innehållstyp för svar: application/xml

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)  |Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|text|(tom)|Krävs. En sträng som innehåller text vars språk ska identifieras. Storleken på texten får inte överstiga 10000 tecken.|DocumentDB| string|
|Authorization|(tom)|Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key  |(tom)    |Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `DetectArray` metod för att identifiera språket i en strängmatris på samma gång. Utför oberoende identifiering av varje enskild matriselement och returnerar ett resultat för varje rad i matrisen.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Formatet för förfrågans text ska vara på följande sätt.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Storleken på texten får inte överstiga 10000 tecken.

**Returvärdet:** En strängmatris som innehåller en två-teckens språkkoder för varje rad i Indatamatrisen.

Formatet för svarstexten är som följer.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
DetectArray lyckades. Returnerar en strängmatris som innehåller en två-teckens språkkoder för varje rad i Indatamatrisen.

string

Innehållstyp för svar: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|Authorization|(tom)|Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om de `appid` fält eller auktoriseringsrubrik har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-addtranslation"></a>Hämta /AddTranslation

### <a name="implementation-notes"></a>Implementeringsanteckningar

> [!IMPORTANT]
> **UTFASNINGEN OBS:** Den här metoden ska inte ta emot nya mening bidrag efter den 31 januari 2018 och du får ett felmeddelande. Läs det här meddelandet om ändringar av funktionerna Collaborative Translation.

Lägger till en translation translation-minne.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

string

Innehållstyp för svar: program: xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp   |
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|originalText|(tom)|Krävs. En sträng som innehåller texten att översätta från. Strängen har en maximal längd på 1000 tecken.|DocumentDB|string|
|translatedText|(tom) |Krävs. En sträng som innehåller översatt text på språket som mål. Strängen har en maximal längd på 2 000 tecken.|DocumentDB|string|
|from|(tom)   |Krävs. En sträng som representerar språkkoden för Översättningstext. en = engelska, de = tyska osv...|DocumentDB|string|
|till|(tom)|Krävs. En sträng som representerar språkkoden att översätta text i.|DocumentDB|string|
|rating|(tom) |Valfri. Ett heltal som representerar den kvalitet klassificeringen för den här strängen. Värde mellan -10 och 10. Standardvärdet är 1.|DocumentDB|integer|
|contentType|(tom)    |Valfri. Formatet på den text som översätts. Format som stöds är ”text/plain” och ”text/html”. All HTML-kod måste vara en korrekt strukturerad, fullständig element.   |DocumentDB|string|
|category|(tom)|Valfri. En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är ”Allmänt”.|DocumentDB|string|
|Användare|(tom)|Krävs. En sträng som används för att spåra avsändaren av överföringen.|DocumentDB|string|
|URI: N|(tom)|Valfri. En sträng som innehåller innehållsplats av denna översättning.|DocumentDB|string|
|Authorization|(tom)|Krävs om fältet appid eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.    |sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|410|AddTranslation stöds inte längre.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Implementeringsanteckningar

> [!IMPORTANT]
> **UTFASNINGEN OBS:** Den här metoden ska inte ta emot nya mening bidrag efter den 31 januari 2018 och du får ett felmeddelande. Läs det här meddelandet om ändringar av funktionerna Collaborative Translation.

Lägger till en matris med översättningar för att lägga till translation minne. Det här är en matris version av `AddTranslation`.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Formatet för förfrågans text är som följer.

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Element i AddtranslationsRequest-elementet är:

* `AppId`: Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Krävs. En sträng som innehåller språkkoden för källspråket. Måste vara något av de språk som returneras av den `GetLanguagesForTranslate` metoden.
* `To`: Krävs. En sträng som innehåller språkkod för språket som mål. Måste vara något av de språk som returneras av den `GetLanguagesForTranslate` metoden.
* `Translations`: Krävs. En matris med översättningar för att lägga till i översättningsminnen. Varje översättning måste innehålla: originalText translatedText och klassificering. Storleken på varje originalText och translatedText är begränsad till 1000 tecken. Summan av alla originalText(s) och translatedText(s) får inte överstiga 10000 tecken. Det maximala antalet matriselement är 100.
* `Options`: Krävs. En uppsättning alternativ, inklusive kategori, ContentType, Uri och användare. Användaren måste anges. Kategori, ContentType och URI: N är valfria. Angivna elementen måste anges i alfabetisk ordning.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
AddTranslationArray metoden har slutförts. Efter den 31 januari 2018 mening bidrag tas inte emot. Tjänsten svarar med felkoden 410.

string

Innehållstyp för svar: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Authorization|(tom)|Krävs om appid fält eller Ocp-Apim-Subscription-Key-huvud inte har angetts. Autentiseringstoken:  ”Ägar” + ”” + ”access_token”.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om appid fält eller auktoriseringsrubrik inte har angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|410    |AddTranslation stöds inte längre.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Implementeringsanteckningar
Delar upp en del av texten i meningar och returnerar en matris som innehåller längder i varje mening.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Returvärdet:** En matris med heltal som representerar längden på meningarna. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En matris med heltal som representerar längden på meningarna. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

integer

Innehållstyp för svar: application/xml 

### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)  |Krävs. Om auktorisering eller Ocp-Apim-Subscription-Key-huvud används, lämna appid fältet tomt eller innehålla en sträng som innehåller ”ägar” + ”” + ”access_token”.|DocumentDB| string|
|text|(tom)   |Krävs. En sträng som representerar texten som ska delas upp i meningar. Storleken på texten får inte överstiga 10000 tecken.|DocumentDB|string|
|language   |(tom)    |Krävs. En sträng som representerar språkkoden för indatatext.|DocumentDB|string|
|Authorization|(tom)|Krävs om appid fält eller Ocp-Apim-Subscription-Key-huvud inte har angetts. Autentiseringstoken:  ”Ägar” + ”” + ”access_token”.    |sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om appid fält eller auktoriseringsrubrik inte har angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401|Ogiltiga autentiseringsuppgifter|
|500|Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID i rubriken X-MS-Trans-Info.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar en matris med översättningar för ett visst språk par från arkivet och MT-motorn. GetTranslations skiljer sig från Översätt som returnerar alla tillgängliga översättningar.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Brödtexten i begäran innehåller valfria TranslationOptions-objektet, vilket har följande format.

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

Den `TranslateOptions` objektet innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard. Angivna elementen måste anges i alfabetisk ordning.

* `Category`: En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är ”Allmänt”.
* `ContentType`: Det går endast att och standard, alternativet är ”text/plain”.
* `IncludeMultipleMTAlternatives`: boolesk flagga för att avgöra om fler än en alternativ ska returneras från MT-motorn. Giltiga värden är true och false (skiftlägeskänsligt). Standard är FALSKT och innehåller endast 1 alternativ. Om flaggan till true kan för att generera artificiella alternativ i översättning, helt integrerat med samarbetsfunktioner översättningar framework (CTF). Funktionen kan för att returnera alternativ för meningar som har inga alternativ i CTF, genom att lägga till artificiella alternativ från listan över avkodaren n bästa.
    - Klassificeringarna klassificeringarna som tillämpas på följande sätt: (1) den bästa automatisk översättningen har en klassificering på 5. 2) alternativ från CTF återspeglar den granskare från att -10 + 10-utfärdaren. 3) alternativ de automatiskt genererade (n-bäst) translation har en klassificering på 0 och har en matchning på 100.
    - Antal alternativ antalet returnerade alternativ som är upp till maxTranslations, men kan vara mindre.
    - Språkpar den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningarna. Det är tillgängligt för alla andra språkpar med Microsoft Translator stöds.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* `Uri`: Filtrera resultaten av den här URI: N. Standardvärdet är alla om inget värde har angetts.
* `User`: Filtrera resultaten av den här användaren. Standardvärdet är alla om inget värde har angetts.

Begär `Content-Type` ska vara `text/xml`.

**Returvärdet:** Formatet på svaret är som följer.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Detta inkluderar en `GetTranslationsResponse` element som innehåller följande värden:

* `Translations`: En matris med matchningar hittas, lagras i TranslationMatch (se nedan)-objekt. Översättningarna kan omfatta liten varianter av den ursprungliga texten (partiell matchning). Översättningarna ska sorteras: 100% matchar först fuzzy matchningar nedan.
* `From`: Om metoden inte angav en From-språk, är det här resultatet av automatisk språkidentifiering. Annars blir det den angivna från språk.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Innehåller samma värde som anges i parametern TranslateOptions.

TranslationMatch består av följande:

* `Error`: Om ett fel har uppstått för en specifik Indatasträngen lagras felkoden. Annars är fältet tomt.
* `MatchDegree`: Systemet matchar inkommande meningar mot butiken, inklusive inexakt matchningar.  MatchDegree anger hur nära indatatexten matchar den ursprungliga texten finns i arkivet. Värdet som returneras adressintervallen från 0 till 100, där 0 är inga likheter och 100 är en exakt skiftlägeskänslig matchning.
MatchedOriginalText: Originaltexten som var matchade för det här resultatet. Returneras bara om den ursprungliga matchade texten har skiljer sig från den inmatade texten. Används för att returnera en ungefärlig matchning källtext. Inte returneras för Microsoft Translator resultat.
* `Rating`: Anger behörighet för den person som du beslutar kvalitet. Maskinöversättning resultat kommer att ha en klassificering på 5. Anonymt angivna översättningar har vanligtvis en klassificering på 1 till 4, även om auktoritärt angivna översättningar har vanligtvis en klassificering på 6 och 10.
* `Count`: Antal gånger som den här translation med denna klassificering har valts. Värdet ska vara 0 för automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En `GetTranslationsResponse` objekt i formatet som beskrivs ovan.

string

Innehållstyp för svar: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|string|
|text|(tom)|Krävs. En sträng som representerar text för översättning. Storleken på texten får inte överstiga 10000 tecken.|DocumentDB|string|
|from|(tom)|Krävs. En sträng som representerar språkkoden för Översättningstext.|DocumentDB|string|
|till |(tom)    |Krävs. En sträng som representerar språkkoden att översätta text i.|DocumentDB|string|
|maxTranslations|(tom)|Krävs. Ett heltal som representerar det maximala antalet översättningar ska returneras.|DocumentDB|integer|
|Authorization| (tom)|Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|string| sidhuvud|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `GetTranslationsArray` metod för att hämta flera translation kandidater för flera källa texter.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Formatet för förfrågans text är som följer.

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` innehåller följande element:

* `AppId`: Krävs. Om auktoriseringsrubrik används, lämna appid fältet tomt eller innehålla en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Krävs. En sträng som representerar språkkoden för Översättningstext.
* `MaxTranslations`: Krävs. Ett heltal som representerar det maximala antalet översättningar ska returneras.
* `Options`: Valfri. Ett alternativ för objekt som innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard. Angivna elementen måste anges i alfabetisk ordning.
    - Kategorin ': En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är allmänna.
    - `ContentType`: Det går endast att och standard, alternativet är text/plain.
    - `IncludeMultipleMTAlternatives`: boolesk flagga för att avgöra om fler än en alternativ ska returneras från MT-motorn. Giltiga värden är true och false (skiftlägeskänsligt). Standard är FALSKT och innehåller endast 1 alternativ. Om flaggan till true kan för att generera artificiella alternativ i översättning, helt integrerat med samarbetsfunktioner översättningar framework (CTF). Funktionen kan för att returnera alternativ för meningar som har inga alternativ i CTF, genom att lägga till artificiella alternativ från listan över avkodaren n bästa.
        - Klassificeringarna klassificeringarna som tillämpas på följande sätt: (1) den bästa automatisk översättningen har en klassificering på 5. 2) alternativ från CTF återspeglar den granskare från att -10 + 10-utfärdaren. 3) alternativ de automatiskt genererade (n-bäst) translation har en klassificering på 0 och har en matchning på 100.
        - Antal alternativ antalet returnerade alternativ som är upp till maxTranslations, men kan vara mindre.
        - Språkpar den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningarna. Det är tillgängligt för alla andra språkpar med Microsoft Translator stöds.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* `Uri`: Filtrera resultaten av den här URI: N. Standardvärdet är alla om inget värde har angetts.
* `User`: Filtrera resultaten av den här användaren. Standardvärdet är alla om inget värde har angetts.
* `Texts`: Krävs. En matris som innehåller text för översättning. Alla strängar måste vara av samma språk. Summan av alla texter översättas får inte överstiga 10000 tecken. Det maximala antalet matriselement är 10.
* `To`: Krävs. En sträng som representerar språkkoden att översätta text i.

Valfria element kan utelämnas. Element som är direkt underordnade till `GetTranslationsArrayRequest` måste anges i alfabetisk ordning.

Begär `Content-Type` ska vara `text/xml`.

**Returvärdet:** Formatet på svaret är som följer.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Varje `GetTranslationsResponse` elementet innehåller följande värden:

* `Translations`: En matris med matchningar hittas, lagras i `TranslationMatch` objekt (se nedan). Översättningarna kan omfatta liten varianter av den ursprungliga texten (partiell matchning). Översättningarna ska sorteras: 100% matchar först fuzzy matchningar nedan.
* `From`: Om metoden inte angav en `From` språk, detta kan bero på automatisk språkidentifiering. Annars blir det den angivna från språk.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Innehåller samma värde som anges i den `TranslateOptions` parametern.

`TranslationMatch` objektet består av följande:
* `Error`: Om ett fel har uppstått för en specifik Indatasträngen lagras felkoden. Annars är fältet tomt.
* `MatchDegree`: Systemet matchar inkommande meningar mot butiken, inklusive inexakt matchningar.  `MatchDegree` Anger hur nära indatatexten matchar den ursprungliga texten finns i arkivet. Värdet som returneras adressintervallen från 0 till 100, där 0 är inga likheter och 100 är en exakt skiftlägeskänslig matchning.
* `MatchedOriginalText`: Originaltexten som var matchade för det här resultatet. Returneras bara om den ursprungliga matchade texten har skiljer sig från den inmatade texten. Används för att returnera en ungefärlig matchning källtext. Inte returneras för Microsoft Translator resultat.
* `Rating`: Anger behörighet för den person som du beslutar kvalitet. Maskinöversättning resultat kommer att ha en klassificering på 5. Anonymt angivna översättningar har vanligtvis en klassificering på 1 till 4, även om auktoritärt angivna översättningar har vanligtvis en klassificering på 6 och 10.
* `Count`: Antal gånger som den här translation med denna klassificering har valts. Värdet ska vara 0 för automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.


### <a name="response-class-status-200"></a>Svaret klass (Status 200)

string

Innehållstyp för svar: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Value|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Authorization  |(tom)    |Krävs om de `appid` fält eller `Ocp-Apim-Subscription-Key` huvud har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om de `appid` fält eller `Authorization` huvud har inte angetts.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till v3 API för textöversättning](../migrate-to-v3.md)










