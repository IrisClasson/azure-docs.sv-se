---
title: Referens för Translator Speech API
titleSuffix: Azure Cognitive Services
description: Referensdokumentation för Translator Speech API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f083a6ca3079128aad4aba3a53013df378a6106
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446909"
---
# <a name="translator-speech-api"></a>Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Den här tjänsten erbjuder en strömmande API för att transkribera konversationsanpassade tal från ett språk i texten i ett annat språk. API: et är integrerat text till tal-funktioner för att tala översatt text tillbaka. Translator Speech API möjliggör scenarier som översättning i realtid av konversationer som visas i Skype Translator.

Med Translator Speech API klientprogram strömma tal ljud till tjänsten och få tillbaka en dataström med textbaserade resultat, som inkluderar den tolkade texten i en källspråket och en översättning som visas på språket som mål. Textresultatet produceras genom automatisk taligenkänning (ASR) som drivs av djupa neurala nätverk för den inkommande ljudströmmen. ASR råutdata ytterligare förbättras av en ny teknik som kallas TrueText för att avspegla närmare användaravsikt. Till exempel TrueText tar bort disfluencies (hmms och coughs) och återställning rätt interpunktion och gemener/versaler. Möjligheten att maskera eller undanta svordomar ingår också. Igenkänningsmotorn och översättningsmotorn är särskilt tränade för att hantera samtal. Talöversättning-tjänsten använder identifiering av tystnad motsvarar slutet av ett uttryck. Efter en paus i röstaktiviteten återger tjänsten ett slutresultat för slutförda påståenden. Tjänsten kan också skicka tillbaka ofullständiga resultat, som ger mellanliggande igenkänningar och översättningar av ett pågående påstående. För slutresultat ger tjänsten möjlighet att syntetisera tal (text) från talat texten i språk som mål. Text till tal-ljud skapas i det format som anges av klienten. WAV- och MP3-format är tillgängliga.

Translator Speech API utnyttjar WebSocket-protokoll för att tillhandahålla en full duplex kommunikationskanalen mellan klienten och servern. Ett program kräver dessa steg för att använda tjänsten:

## <a name="1-getting-started"></a>1. Komma igång
Åtkomst till Translator Text API behöver du [registrera dig för Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Autentisering

Använda prenumerationsnyckeln för att autentisera. Translator Speech API stöder två lägen för autentisering:

* **Med hjälp av en åtkomst-token:** Hämta en åtkomsttoken från tjänsten token i ditt program. Använd din prenumerationsnyckel för Translator Speech API för att hämta en åtkomsttoken från Azure Cognitive Services Autentiseringstjänsten. Åtkomsttoken är giltig i 10 minuter. Skaffa en ny åtkomsttoken var tionde minut och fortsätt att använda samma åtkomst-token för upprepade begäranden inom dessa 10 minuter.

* **Med hjälp av en prenumerationsnyckel direkt:** I ditt program skickar din prenumerationsnyckel som ett värde i `Ocp-Apim-Subscription-Key` rubrik.

Hantera din prenumerationsnyckel och åtkomst-token som hemligheter som ska vara dold från vyn.

## <a name="3-query-languages"></a>3. Frågespråk
**Fråga efter resurs-språk för den aktuella uppsättningen språk som stöds.** Den [språk resource](languages-reference.md) visar antal språk och röster som är tillgängliga för taligenkänning för textöversättning och text till tal. Varje språk eller en röst tilldelas en identifierare som används för att identifiera samma språk eller röst i Translator Speech API.

## <a name="4-stream-audio"></a>4. Stream-ljud
**Öppna en anslutning och börjar spela upp ljud till tjänsten.** Tjänst-URL: en är `wss://dev.microsofttranslator.com/speech/translate`. Parametrar och ljudformat som förväntas av tjänsten är beskrivs nedan, i den `/speech/translate` igen. En av parametrarna används för att skicka åtkomsttoken från steg 2 ovan.

## <a name="5-process-the-results"></a>5. Bearbeta resultaten
**Bearbeta resultaten strömmas tillbaka från tjänsten.** Formatet för ofullständiga resultat, slutliga resultaten och text till tal ljud segment beskrivs i dokumentationen för den `/speech/translate` åtgärden nedan.

Kodexempel som demonstrerar användningen av Translator Speech API är tillgängliga från den [Microsoft Translator GitHub-webbplatsen](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Implementeringsanteckningar

Hämta /speech/translate Establishes en session för talöversättning

### <a name="connecting"></a>Ansluta
Granska listan över parametrar som anges senare i det här avsnittet innan du ansluter till tjänsten. En exempelbegäran är:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Begäran anger att talat engelska kommer strömmas till tjänsten och översättas till italienska. Varje sista igenkänningsresultatet genererar ett text till tal ljud svar med kvinnliga röst med namnet Elsa. Observera att begäran inkluderar autentiseringsuppgifterna i den `Ocp-Apim-Subscription-Key header`. Begäran också följer bästa praxis genom att ange en globalt unik identifierare i huvudet `X-ClientTraceId`. Ett klientprogram ska logga trace-ID så att den kan användas för att felsöka problem när de uppstår.

### <a name="sending-audio"></a>Skickar ljud
När anslutningen har upprättats börjar klienten strömning av ljud till tjänsten. Klienten skickar ljud i segment. Varje segment överförs med hjälp av ett Websocket-meddelande av typen Binary.

Ljudindata är i formatet Wave ljud (WAVE eller mer, ofta kallade WAV på grund av dess filnamnstillägg). Klientprogrammet bör strömma en kanal, signerat 16-bitars PCM ljud samplas med 16 kHz. Den första uppsättningen med byte som strömmas av klienten kommer inkludera WAV-huvud. En 44 byte-rubriken är för en enskild kanal signerat 16 bitar PCM stream samplas med 16 kHz:

|Offset|Värde|
|:---|:---|
|0 - 3|”RIFF”|
|4 - 7|0|
|8 - 11|”WAVE”|
|12 - 15|”fmt”|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

Observera att total filstorlek (byte 4-7) och storleken på ”data” (byte 40-43) är inställda på noll. Det här är OK för strömmande scenariot där den totala storleken inte är nödvändigtvis känd förskott.

När du har skickat rubriken WAV (RIFF), skickar klienten segment av ljuddata. Klienten kommer normalt att strömma fast storlek segment som representerar en fast giltighetstid (t.ex. stream 100 MS med ljud i taget).

### <a name="signal-the-end-of-the-utterance"></a>Signal slutet av uttryck
Translator Speech API returnerar avskriften och översättning av ljudströmmen som du skickar ljudet. Slutlig avskrift och sista översättningen översatta ljudet återgår till dig först efter slutet av uttryck. I vissa fall kanske du vill tvinga slutet av uttryck. Skicka 2,5 sekunders tystnad att tvinga slutet av uttryck.

### <a name="final-result"></a>Slutresultatet
En slutlig tal igenkänningsresultatet genereras i slutet av ett uttryck. Resultatet skickas till klienten med hjälp av ett WebSocket-meddelande av typen Text från tjänsten. Meddelandeinnehåll är JSON-serialisering för ett objekt med följande egenskaper:

* `type`: Strängkonstant att identifiera vilken typ av resultat. Värdet är slutgiltiga för slutliga resultaten.
* `id`: Strängidentifierare som tilldelats att igenkänningsresultatet.
* `recognition`: Tolkade texten i en källspråket. Texten kan vara en tom sträng när det gäller ett falskt erkännande.
* `translation`: Tolkade texten översättas på språket som mål.
* `audioTimeOffset`: Förskjutning i början av taligenkänning i Tick (1 skalstreck = 100 nanosekunder). Förskjutningen är i förhållande till början av strömning.
* `audioTimeSize`: Varaktighet i Tick (100 nanosekunder) erkännande.
* `audioStreamPosition`: Byte-förskjutningen i början av erkännande. Förskjutningen är i förhållande till början av strömmen.
* `audioSizeBytes`: Storlek i byte för erkännande.

Observera att placeringen av taligenkänning i ljudströmmen inte ingår i resultaten som standard. Den `TimingInfo` funktionen måste väljas av klienten (se `features` parametern).

Ett exempel slutresultat är följande:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Delresultat
Partiell eller mellanliggande tal igenkänningsresultat strömmas inte till klienten som standard. Klienten kan använda Frågeparametern funktioner för att begära dem.

En partiell resultatet skickas till klienten med hjälp av ett WebSocket-meddelande av typen Text från tjänsten. Meddelandeinnehåll är JSON-serialisering för ett objekt med följande egenskaper:

* `type`: Strängkonstant att identifiera vilken typ av resultat. Värdet är delvis för ofullständiga resultat.
* `id`: Strängidentifierare som tilldelats att igenkänningsresultatet.
* `recognition`: Tolkade texten i en källspråket.
* `translation`: Tolkade texten översättas på språket som mål.
* `audioTimeOffset`: Förskjutning i början av taligenkänning i Tick (1 skalstreck = 100 nanosekunder). Förskjutningen är i förhållande till början av strömning.
* `audioTimeSize`: Varaktighet i Tick (100 nanosekunder) erkännande.
* `audioStreamPosition`: Byte-förskjutningen i början av erkännande. Förskjutningen är i förhållande till början av strömmen.
* `audioSizeBytes`: Storlek i byte för erkännande.

Observera att placeringen av taligenkänning i ljudströmmen inte ingår i resultaten som standard. Funktionen TimingInfo måste väljas av klienten (se funktioner parametern).

Ett exempel slutresultat är följande:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Text till tal
När funktionen text till tal är aktiverad (se `features` parametern nedan), ett slutresultat följs av ljudet från talat översatt text. Ljuddata chunked och skickas från tjänsten till klienten som en sekvens av Websocket-meddelanden av typen Binary. En klient kan identifiera slutet på strömmen genom att kontrollera FIN-bitars för varje meddelande. Det sista binära meddelandet har dess FIN bit inställd på en till slutet av dataströmmen. Formatet på dataströmmen är beroende av värdet för den `format` parametern.

### <a name="closing-the-connection"></a>Stänga anslutningen
När ett klientprogram har slutförts strömning av ljud och har tagit emot de senaste slutresultatet, bör det stänga anslutningen genom att initiera WebSocket avslutande handskakningen. Det finns villkor som gör att servern för att avsluta anslutningen. Följande WebSocket stängd koder kan tas emot av klienten:

* `1003 - Invalid Message Type`: Servern avslutar anslutningen eftersom den inte kan acceptera den datatyp som togs emot. Detta händer ofta när inkommande ljud inte börjar med rätt rubrik.
* `1000 - Normal closure`: Anslutningen har stängts när begäran har uppfyllts. Servern stänger anslutningen: när inga ljud tas emot från klienten under en längre tid. När tystnad strömmas under en längre tid. När en session når maximal varaktighet tillåtna (cirka 90 minuter).
* `1001 - Endpoint Unavailable`: Indikerar att servern blir otillgänglig. Klientprogrammet kan försöka att återansluta med en gräns för antalet återförsök.
* `1011 - Internal Server Error`: Anslutningen kommer att stängas av servern på grund av ett fel på servern.

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:---|:---|:---|:---|:---|
|API-versionen|1.0|Versionen av API: et som begärs av klienten. Tillåtna värden är: `1.0`.|query   |string|
|from|(tom)   |Anger språket inkommande tal. Värdet är en av de språk som identifierarna från den `speech` omfång i svaret från språk-API: et.|query|string|
|till|(tom)|Anger de språk för att översätta den transkriberade texten till. Värdet är en av de språk som identifierarna från den `text` omfång i svaret från språk-API: et.|query|string|
|funktioner|(tom)   |CSV-uppsättning funktioner som markerats av klienten. Tillgängliga funktioner:<ul><li>`TextToSpeech`: Anger att tjänsten måste returnera översatta ljudet från den sista översatta meningen.</li><li>`Partial`: Anger att tjänsten måste returnera mellanliggande resultat när ljudet strömmar till tjänsten.</li><li>`TimingInfo`: Anger att tjänsten måste returnera tidsinformation som är associerade med varje av.</li></ul>Exempelvis kan en klient anger `features=partial,texttospeech` att ta emot ofullständiga resultat och text till tal, men inga tidsinformation. Observera att strömmas alltid slutliga resultaten till klienten.|query|string|
|voice|(tom)|Identifierar vilka röst för text till tal återgivningen av den översatta texten. Värdet är en röst-identifierare från text till tal-omfång i svaret från språk-API: et. Om en röst inte har angetts systemet kommer automatiskt väljer du en när text till tal-funktionen är aktiverad.|query|string|
|format|(tom)|Anger formatet för text till tal ljudström som returnerats av tjänsten. De tillgängliga alternativen är:<ul><li>`audio/wav`: Ljudström i Wave. Klienten bör använda WAV-huvudet kan tolka ljudformatet. WAV-ljud för text till tal är 16-bitars, enskild kanal PCM med en samplingsfrekvensen av 24 eller 16kHz.</li><li>`audio/mp3`: MP3 ljudström.</li></ul>Standardvärdet är `audio/wav`.|query|string|
|ProfanityAction    |(tom)    |Anger hur tjänsten ska hantera profanities som identifieras i tal. Giltiga åtgärder är:<ul><li>`NoAction`: Profanities lämnas skick.</li><li>`Marked`: Profanities ersätts med en markör. Se `ProfanityMarker` parametern.</li><li>`Deleted`: Profanities tas bort. Till exempel om ordet `"jackass"` behandlas som en svordomar frasen `"He is a jackass."` blir `"He is a .".`</li></ul>Standardvärdet är markerat.|query|string|
|ProfanityMarker|(tom)    |Anger hur identifierade profanities hanteras när `ProfanityAction` är inställd på `Marked`. Giltiga alternativ är:<ul><li>`Asterisk`: Profanities ersätts med strängen `***`. Till exempel om ordet `"jackass"` behandlas som en svordomar frasen `"He is a jackass."` blir `"He is a ***.".`</li><li>`Tag`: Svordomar omges av ett olämpligt språk XML-kod. Till exempel om ordet `"jackass"` behandlas som en svordomar frasen `"He is a jackass."` blir `"He is a <profanity>jackass</profanity>."`.</li></ul>Standardvärdet är `Asterisk`.|query|string|
|Authorization|(tom)  |Anger värdet för klientens ägartoken. Använd prefixet `Bearer` följt av värdet för den `access_token` värdet som returneras av token authentication-tjänsten.|sidhuvud   |string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om de `Authorization` huvud har inte angetts.|sidhuvud|string|
|access_token|(tom)   |Alternativa sätt att skicka en giltig OAuth-åtkomsttoken. Ägartoken tillhandahålls vanligtvis med rubriken `Authorization`. Vissa websocket-bibliotek tillåter inte klientkod för att ange huvuden. I detta fall är klienten kan använda den `access_token` frågeparameter för att skicka en giltig token. När du använder en åtkomsttoken för autentisering, om `Authorization` huvud har inte angetts, sedan `access_token` måste anges. Om både rubrik och frågeparameter har angetts, ignoreras Frågeparametern. Klienter ska bara använda en metod för att skicka token.|query|string|
|prenumerationsnyckel|(tom)   |Alternativa sätt att överföra prenumerationsnyckel. Vissa websocket-bibliotek tillåter inte klientkod för att ange huvuden. I detta fall är klienten kan använda den `subscription-key` frågeparameter för att skicka en giltig prenumeration-nyckel. När du använder en prenumerationsnyckel för autentisering, om `Ocp-Apim-Subscription-Key` huvud har inte angetts och prenumerationsnyckel måste anges. Om både rubrik och frågeparameter har angetts, ignoreras Frågeparametern. Klienter bör endast använda en metod för att skicka den `subscription key`.|query|string|
|X-ClientTraceId    |(tom)    |En klientgenererade GUID som används för att spåra en begäran. Klienter ska för rätt felsökning av problem, ange ett nytt värde för varje begäran och logga den.<br/>Istället för att använda en rubrik, kan det här värdet skickas med Frågeparametern `X-ClientTraceId`. Om både rubrik och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|string|
|X-CorrelationId|(tom)    |En klientgenererade identifierare som används för att kombinera flera kanaler i en konversation. Du kan skapa flera speech translation-sessioner för att aktivera konversationer mellan användare. I sådana fall kan använda alla speech translation-sessioner samma Korrelations-ID för att koppla ihop kanaler. Detta underlättar spårning och diagnostik. Identifieraren ska följa: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Istället för att använda en rubrik, kan det här värdet skickas med Frågeparametern `X-CorrelationId`. Om både rubrik och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|string|
|X-ClientVersion|(tom)    |Identifierar versionen av klientprogrammet. Exempel: "2.1.0.123".<br/>Istället för att använda en rubrik, kan det här värdet skickas med Frågeparametern `X-ClientVersion`. Om både rubrik och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|string|
|X-OsPlatform|(tom)   |Anger namnet och versionen av operativsystemet klientprogrammet körs på. Exempel: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Istället för att använda en rubrik, kan det här värdet skickas med Frågeparametern `X-OsPlatform`. Om både rubrik och frågeparameter har angetts, ignoreras Frågeparametern.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|Responsmodell|Rubriker|
|:--|:--|:--|:--|
|101    |WebSocket-uppgraderingen.|Modellen exempelvärde <br/> Objekt {}|X-RequestId<br/>Ett värde som identifierar begäran för felsökning.<br/>string|
|400    |Felaktig begäran. Kontrollera indataparametrarna för att säkerställa att de är giltiga. Objektet response innehåller en detaljerad beskrivning av felet.|||
|401    |Obehörig. Kontrollera att autentiseringsuppgifterna är inställda att de är giltiga och att din Azure Data marknaden prenumeration är i gott med en tillgänglig balans.|||
|500    |Ett fel uppstod. Om felet kvarstår, rapportera det med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|||
|503    |Servern är inte tillgänglig för tillfället. Försök med förfrågan. Om felet kvarstår, rapportera det med klient-ID för spårning (X-ClientTraceId) eller begäranidentifierare (X-RequestId).|||
