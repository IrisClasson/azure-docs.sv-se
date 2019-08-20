---
title: 'Azure Cosmos DB: SQL Node. js-API, SDK &-resurser'
description: Lär dig allt om SQL Node. js API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB Node. js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 27b548d03e6b05179da744e636a5c887e6b01ad5
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624678"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node. js SDK för SQL API: Viktig information och resurser
> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Länka  |
|---------|---------|
|Hämta SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-dokumentation  |  [Referens dokumentation för Java Script SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instruktioner för SDK-installation  |  [Installationsinstruktioner](https://github.com/Azure/azure-cosmos-js#installation)
|Bidra till SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exempel | [Node. js-kod exempel](sql-api-nodejs-samples.md)
| Vägledning för att komma igång | [Kom igång med Java Script SDK](sql-api-nodejs-get-started.md)
| Själv studie kurs om webbapp | [Bygg ett Node. js-webbprogram med Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuell plattform som stöds | [Node. js V12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK version 3. x. x<br/>[Node. js v10. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK version 3. x. x<br/>[Node. js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK version 3. x. x<br/>[Node. js v6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK version 2. x. x<br/>[Node. js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)– SDK version 1. x. x<br/> [Node. js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)– SDK version 1. x. x<br/> [Node. js v 0,10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK version 1. x. x

## <a name="release-notes"></a>Viktig information

### <a name="3.1.0"/>3.1.0</a>
* Ange standard-ResponseContinuationTokenLimitInKB till 1 KB. Som standard capping detta till 1 KB för att undvika långa huvuden (Node. js har en storleks gräns för global rubrik). En användare kan ange det här fältet för att tillåta längre rubriker, vilket kan hjälpa till att optimera frågekörning för Server delen.
* Ta bort disableSSLVerification. Det här alternativet har nya alternativ som beskrivs i [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="3.0.4"/>3.0.4</a>
* Tillåt att initialHeaders explicit anger nyckel huvud för partition
* Använd Package. JSON # filer för att förhindra att extra filer publiceras
* Åtgärda sorterings fel i cirkulations kartan på en äldre version av Node + V8
* Korrigerar fel när användaren tillhandahåller alternativ för del återförsök

### <a name="3.0.3"/>3.0.3</a>
* Förhindra WebPack från att lösa moduler som kallas med Kräv

### <a name="3.0.2"/>3.0.2</a>
* Åtgärdar ett långt enastående fel där ru: er alltid rapporteras som 0 för mängd frågor

### <a name="3.0.0"/>3.0.0</a>

🎉 v3-utgåva! 🎉 många nya funktioner, fel korrigeringar och några större ändringar. Primära mål för den här versionen:

* Implementera viktiga nya funktioner
  * DISTINKTa frågor
  * GRÄNS/FÖRSKJUTNINGs frågor
  * Begäran om att avbryta användare
* Uppdatera till den senaste versionen av Cosmos REST API där alla behållare har obegränsad skalning
* Gör det enklare att använda Cosmos från webbläsaren
* Bättre anpassning med de nya Azure JS SDK-rikt linjerna

#### <a name="migration-guide-for-breaking-changes"></a>Migrations guide för att bryta ändringar
##### <a name="improved-client-constructor-options"></a>Förbättrade alternativ för klient-konstruktor

Alternativen för konstruktorn har förenklats:

* masterKey har bytt namn till nyckeln och flyttats till den översta nivån
* Egenskaper tidigare under alternativ. auth har flyttats till den översta nivån

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Förenklad QueryIterator-API
I v2 fanns det många olika sätt att iterera eller hämta resultat från en fråga. Vi har försökt att förenkla v3-API: et och ta bort liknande eller dubbla API: er:

* Ta bort iterator. Next () och iterator. Current (). Använd fetchNext () för att hämta resultat sidor.
* Ta bort iterator. (). Använd asynkrona iteratorer i stället.
* iterator. executeNext () har bytt namn till iterator. fetchNext ()
* iterator. toArray () har bytt namn till iterator. fetchAll ()
* Sidor är nu riktiga svars objekt i stället för vanliga JS-objekt
* CONST container = client. Database (dbId). container (containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Fasta behållare har nu partitioner ATS
Cosmos-tjänsten stöder nu partitionerings nycklar på alla behållare, inklusive de som tidigare har skapats som fasta behållare. V3 SDK uppdaterar till den senaste API-versionen som implementerar den här ändringen, men den bryts inte. Om du inte anger någon partitionsnyckel för åtgärder kommer vi att använda en system nyckel som fungerar med alla befintliga behållare och dokument.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert har tagits bort för lagrade procedurer
Tidigare upsert tilläts för icke-partitionerade samlingar, men med uppdatering av API-versionen partitioneras alla samlingar så att de tas bort helt.

##### <a name="item-reads-will-not-throw-on-404"></a>Objekt läsningar kommer inte att kastas på 404
CONST container = client. Database (dbId). container (containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Standard skrivning i flera regioner
SDK: n kommer nu att skriva till flera regioner som standard om din Cosmos-konfiguration stöder det. Detta var tidigare inaktiverat beteende.

##### <a name="proper-error-objects"></a>Korrekta fel objekt
Misslyckade förfrågningar returnerar nu rätt fel eller underklasser av fel. Tidigare utlöste de vanliga JS-objekt.

#### <a name="new-features"></a>Nya funktioner
##### <a name="user-cancelable-requests"></a>Användare-cancelable-begäranden
Med funktionen flytta till hämtning internt kan vi använda webbläsarens AbortController-API för att stödja åtgärder som kan avbrytas av användaren. Om det finns åtgärder där flera begär Anden är potentiellt pågående (t. ex. kors partitions frågor) avbryts alla förfrågningar för åtgärden. Moderna webb läsar användare har redan AbortController. Node. js-användare måste använda ett polyfyllnings bibliotek

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Ange data flöde som en del av åtgärden för att skapa en databas/container
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Generering av header- @azure/cosmos-signtoken delades upp i ett nytt bibliotek. Alla som anropar Cosmos-REST API direkt kan använda detta för att signera rubriker med samma kod som @azure/cosmosvi kallar inuti.

##### <a name="uuid-for-generated-ids"></a>UUID för genererade ID: n
v2 hade anpassad kod för att generera objekt-ID: n. Vi har bytt till det välkända och bibehållna community Library uuid.

##### <a name="connection-strings"></a>Anslutningssträngar
Nu kan du skicka en anslutnings sträng som har kopierats från Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Förbättrad webb läsar upplevelse
Det var möjligt att använda v2 SDK i webbläsaren, men det var inte en perfekt upplevelse. Du behövde polyfyll flera inbyggda bibliotek i Node. js och använda ett paket som webbpaket eller paket. V3 SDK gör att du får ut så mycket bättre för webb läsar användare.

* Ersätt begär ande interna med Fetch (#245)
* Ta bort användning av buffert (#330)
* Ta bort nodens inbyggda användning för Universal packages/API: er (#328)
* Växla till nod-abort-Controller (#294)

#### <a name="bug-fixes"></a>Felkorrigeringar
* Åtgärda tester för att läsa och ta tillbaka erbjudande (#224)
* Åtgärda EnableEndpointDiscovery (#207)
* Åtgärda saknade ru: er på sid brytnings resultat (#360)
* Expandera SQL-frågeparameter (#346)
* Lägg till TTL till ItemDefinition (#341)
* Korrigera mått för CP-fråga (#311)
* Lägg till activityId till FeedResponse (#293)
* Växla _ts-typ från sträng till tal (#252) (#295)
* Korrigera samling för begär ande avgift (#289)
* Tillåt tomma sträng partitionerings nycklar (#277)
* Lägg till sträng till typ av konflikt fråga (#237)
* Lägg till uniqueKeyPolicy i container (#234)

#### <a name="engineering-systems"></a>Teknik system
Inte alltid de mest synliga ändringarna, men de hjälper vårt team att leverera bättre kod, snabbare.

* Använd sammanslagning för produktions versioner (#104)
* Uppdatera till typescript 3,5 (#327)
* Konvertera till TS-projekt referenser. Extrahera testmapp (#270)
* Aktivera noUnusedLocals och noUnusedParameters (#275)
* Azure pipeline-YAML för CI-versioner (#298)

### <a name="2.1.5"/>2.1.5</a>
* Inga kod ändringar. Åtgärdar ett problem där vissa extra filer ingår i 2.1.4-paketet.

### <a name="2.1.4"/>2.1.4</a>
* Åtgärda regional redundans inom princip för återförsök
* Korrigera ChangeFeed hasMoreResults-egenskap
* Uppdatering av dev-beroenden
* Lägg till PolicheckExclusions. txt

### <a name="2.1.3"/>2.1.3</a>
* Växla _ts-typ från sträng till tal
* Åtgärda standardvärden för indexerings test
* Backport uniqueKeyPolicy till v2
* Fel söknings korrigeringar för demo och demo

### <a name="2.1.2"/>2.1.2</a>
* Backport erbjuder korrigeringar från v3-gren
* Åtgärda fel i executeNext ()-typ signatur
* Skrivfel

### <a name="2.1.1"/>2.1.1</a>
* Bygg om omstrukturering. Gör det möjligt att hämta SDK-versionen vid Bygg tiden.

### <a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nya funktioner
* Stöd för ChangeFeed har lagts till (#196)
* Multipolygon-datatype har lagts till för indexering (#191)
* Lägg till egenskapen Key i konstruktorn som alias för masterKey (#202)

#### <a name="fixes"></a>Korrigeringar
* Åtgärda fel där Next () returnerade ett felaktigt värde för iterator

#### <a name="engineering-improvements"></a>Tekniska förbättringar
* Lägg till integrations test för typescript-förbrukning (#199)
* Aktivera installation direkt från GitHub (#194)

### <a name="2.0.5"/>2.0.5</a>
* Lägger till gränssnitt för Node agent-typ. Typescript-användare behöver inte längre installera @types/node som ett beroende
* Önskade platser är nu korrekt lösta
* Förbättringar av bidrags dokumentationen för utvecklare
* Olika skrivfel

### <a name="2.0.4"/>2.0.4</a>
* Korrigerar typ definitions problem som introducerades i 2.0.3

### <a name="2.0.3"/>2.0.3</a>
* Ta `big-integer` bort beroende
* Växla till referens direktiv för AsyncIterable-typ. Typescript-användare behöver inte längre anpassa sina "lib"-inställningar.
* Skrivfel

### <a name="2.0.2"/>2.0.2</a>
* Åtgärda viktigt-länkar

### <a name="2.0.1"/>2.0.1</a>
* Åtgärda implementering av gränssnitt för nya försök

### <a name="2.0.0"/>2.0.0</a>
* GA of version 2.0.0 av JavaScript SDK
* Stöd har lagts till för skrivningar i flera regioner.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1-2.0.0 för Java Script SDK för offentlig för hands version.
* Ny objekt modell med toppnivå CosmosClient och metoder som delas mellan relevanta databaser, behållare och objekt klasser. 
* Stöd för [löfte](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK konverteras till TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* NPM-dokumentationen har åtgärd ATS.

### <a name="1.14.3"/>1.14.3</a>
* Stöd har lagts till för standard återförsök vid anslutnings problem.
* Stöd har lagts till för att läsa samlingens ändrings flöde.
* Fel vid konsekvens av den fasta sessionen som tillfälligt orsakade "Read session inte tillgänglig".
* Stöd har lagts till för frågans mått.
* Det maximala antalet anslutningar för http-agenten har ändrats.

### <a name="1.14.2"/>1.14.2</a>
* Uppdaterad dokumentation till referens Azure Cosmos DB i stället för Azure-DocumentDB.
* Stöd har lagts till för proxyUrl-inställningen i ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Mindre korrigering för Skift läges känsliga fil system.

### <a name="1.14.0"/>1.14.0</a>
* Lägger till stöd för konsekvens av sessioner.
* Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Dela upp försäkrade kors partitions frågor.
* Lägger till stöd för resurs länkar med inledande och avslutande snedstreck (och motsvarande test).

### <a name="1.12.2"/>1.12.2</a>
*   NPM-dokumentationen har åtgärd ATS.

### <a name="1.12.1"/>1.12.1</a>
* En bugg har åtgärd ATS i executeStoredProcedure där dokument som berörs hade särskilda Unicode-tecken (LS, PS).
* En bugg har åtgärd ATS vid hantering av dokument med Unicode-tecken i partitionsnyckel.
* Fast stöd för att skapa samlingar med namn mediet. GitHub problem #114.
* Fast stöd för auktorisering av behörighets-token. GitHub problem #178.

### <a name="1.12.0"/>1.12.0</a>
* Stöd har lagts till för en ny [konsekvens nivå](consistency-levels.md) som kallas konsekvensprincip.
* Stöd har lagts till för UriFactory.
* Ett fel har åtgärd ATS i Unicode-stöd. GitHub problem #171.

### <a name="1.11.0"/>1.11.0</a>
* Har lagt till stöd för agg regerings frågor (antal, MIN, MAX, SUM och AVG).
* Alternativet för att kontrol lera graden av parallellitet för kors partitions frågor har lagts till.
* Alternativet för att inaktivera SSL-verifiering har lagts till när Azure Cosmos DB emulator körs.
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.
* Korrigerade fel i fortsättnings-token för en samling med en partition. GitHub problem #107.
* Korrigerade executeStoredProcedure-felet i hantering av 0 som en enda param. GitHub problem #155.

### <a name="1.10.2"/>1.10.2</a>
* Fast user-agent-huvud som innehåller SDK-versionen.
* Mindre kod rensning.

### <a name="1.10.1"/>1.10.1</a>
* Inaktiverar SSL-verifiering när du använder SDK för att rikta emulatorn (hostname = localhost).
* Stöd har lagts till för att aktivera skript loggning under körning av lagrad procedur.

### <a name="1.10.0"/>1.10.0</a>
* Tillagt stöd för olika plattformar partition parallella frågor.
* Stöd har lagts till för TOP/ORDER BY-frågor för partitionerade samlingar.

### <a name="1.9.0"/>1.9.0</a>
* Princip stöd för återförsök har lagts till för begränsade begär Anden. (Begränsade begär Anden tar emot en begäran om för stort undantag, felkod 429.) Som standard försöker Azure Cosmos DB nio gånger för varje begäran när felkod 429 påträffas, vilket följer retryAfter-tiden i svars huvudet. Du kan nu ange ett intervall med fasta återförsöksintervall som en del av egenskapen RetryOptions i ConnectionPolicy-objektet om du vill ignorera retryAfter-tiden som returnerades av servern mellan Återförsöken. Azure Cosmos DB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal försök) och returnerar svaret med felkoden 429. Den här tiden kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.
* Cosmos DB Returnerar nu x-MS-begränsning-försök-antal och x-MS-begränsning-försök-wait-Time-MS som svars rubriker i varje begäran om att ange begränsningen för antal försök och den ackumulerade tiden som begäran väntar mellan Återförsöken.
* Klassen RetryOptions lades till, exponerar egenskapen RetryOptions för klassen ConnectionPolicy som kan användas för att åsidosätta några av standard alternativen för återförsök.

### <a name="1.8.0"/>1.8.0</a>
* Lagt till stöd för flera regioner.

### <a name="1.7.0"/>1.7.0</a>
* Har lagt till stöd för TTL-funktionen (Time to Live) för dokument.

### <a name="1.6.0"/>1.6.0</a>
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Fast RangePartitionResolver. resolveForRead-bugg där det inte returnerade länkar på grund av ett felaktigt concat resultat.

### <a name="1.5.5"/>1.5.5</a>
* Fast hashPartitionResolver-resolveForRead (): Om ingen partitionsnyckel angavs som undantag, i stället för att returnera en lista över alla registrerade länkar.

### <a name="1.5.4"/>1.5.4</a>
* Åtgärdar problem [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -dedikerad https-agent: Undvik att ändra den globala agenten för Azure Cosmos DBs syfte. Använd en dedikerad agent för alla förfrågningar från lib.

### <a name="1.5.3"/>1.5.3</a>
* Löser problemet [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – hantera bindestreck i medie-ID: er korrekt.

### <a name="1.5.2"/>1.5.2</a>
* Åtgärdar problem med [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter lyssnar läckor.

### <a name="1.5.1"/>1.5.1</a>
* Åtgärdar problem [#92](https://github.com/Azure/azure-documentdb-node/issues/90) – byta namn på mappens hash-värde till hash för Skift läges känsliga system.

### <a name="1.5.0"/>1.5.0</a>
* Implementera horisontell partitionering-stöd genom att lägga till hash-& intervall partition matchare.

### <a name="1.4.0"/>1.4.0</a>
* Implementera Upsert. Nya upsertXXX-metoder på documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Hoppade över för att placera versions numren i justering med andra SDK: er.

### <a name="1.2.2"/>1.2.2</a>
* Delning av Q-löfte till ny lagrings plats.
* Uppdatera till paket filen för NPM-registret.

### <a name="1.2.1"/>1.2.1</a>
* Implementerar ID-baserad routning.
* Åtgärdar problem [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuell egenskap står i konflikt med metoden Current ().

### <a name="1.2.0"/>1.2.0</a>
* Stöd har lagts till för GeoSpatialt index.
* Verifierar id-egenskapen för alla resurser. ID: n för resurser får inte innehålla?,/ &#47; &#47;, #,, tecken eller slutar med ett blank steg.
* Lägger till ny rubrik ”index omvandling status” ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementerar v2-indexerings princip.

### <a name="1.0.3"/>1.0.3</a>
* Utfärda [#40](https://github.com/Azure/azure-documentdb-node/issues/40) -implementerade eslint-och grunt-konfigurationer i Core-och Promise SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -Promise wrapper innehåller inte något huvud med felet.

### <a name="1.0.1"/>1.0.1</a>
* Implementerad möjlighet att fråga efter konflikter genom att lägga till readConflicts, readConflictAsync och queryConflicts.
* Uppdaterad API-dokumentation.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client. createDocumentAsync-fel.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Fri & Släpp dras tillbaka datum
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

Varje begäran till Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 augusti 2018 |--- |
| [1.14.4](#1.14.4) |Den 03 maj 2018 |--- |
| [1.14.3](#1.14.3) |Den 03 maj 2018 |--- |
| [1.14.2](#1.14.2) |21 december 2017 |--- |
| [1.14.1](#1.14.1) |Den 10 november 2017 |--- |
| [1.14.0](#1.14.0) |9 november 2017 |--- |
| [1.13.0](#1.13.0) |11 oktober 2017 |--- |
| [1.12.2](#1.12.2) |10 augusti 2017 |--- |
| [1.12.1](#1.12.1) |10 augusti 2017 |--- |
| [1.12.0](#1.12.0) |10 maj 2017 |--- |
| [1.11.0](#1.11.0) |16 mars 2017 |--- |
| [1.10.2](#1.10.2) |27 januari 2017 |--- |
| [1.10.1](#1.10.1) |22 december 2016 |--- |
| [1.10.0](#1.10.0) |03 oktober 2016 |--- |
| [1.9.0](#1.9.0) |Den 07 juli 2016 |--- |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |--- |
| [1.5.6](#1.5.6) |08 mars 2016 |--- |
| [1.5.5](#1.5.5) |Februari 02 2016 |--- |
| [1.5.4](#1.5.4) |01 februari 2016 |--- |
| [1.5.2](#1.5.2) |26 januari 2016 |--- |
| [1.5.2](#1.5.2) |Den 22 januari 2016 |--- |
| [1.5.1](#1.5.1) |4 januari 2016 |--- |
| [1.5.0](#1.5.0) |Den 31 december 2015 |--- |
| [1.4.0](#1.4.0) |06 oktober 2015 |--- |
| [1.3.0](#1.3.0) |06 oktober 2015 |--- |
| [1.2.2](#1.2.2) |10 september 2015 |--- |
| [1.2.1](#1.2.1) |15 augusti 2015 |--- |
| [1.2.0](#1.2.0) |05 augusti 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.3](#1.0.3) |04 juni 2015 |--- |
| [1.0.2](#1.0.2) |23 maj 2015 |--- |
| [1.0.1](#1.0.1) |15 maj, 2015 |--- |
| [1.0.0](#1.0.0) |08, 2015 |--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

