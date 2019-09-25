---
title: Vanliga användningsområden och scenarier för Azure Cosmos DB
description: 'Lär dig mer om överst fem användningsfall för Azure Cosmos DB: genererade innehåll, loggning, katalogdata, inställningar för användardata och Internet of Things (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: e22b426b2172c169f9343569fffac57f370afbee
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219878"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Vanliga användningsområden för Azure Cosmos DB
Den här artikeln innehåller en översikt över flera vanliga användningsområden för Azure Cosmos DB.  Rekommendationerna i den här artikeln fungerar som en startpunkt när du utvecklar ditt program med Cosmos DB.   

När du har läst den här artikeln kommer du att kunna besvara följande frågor: 

* Vad är vanliga användningsområden för Azure Cosmos DB?
* Vilka är fördelarna med att använda Azure Cosmos DB för detaljhandel program?
* Vilka är fördelarna med att använda Azure Cosmos DB som ett datalager för Internet of Things (IoT)-system?
* Vilka är fördelarna med att använda Azure Cosmos DB för webbprogram och mobilappar?

## <a name="introduction"></a>Introduktion
[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databastjänst. Tjänsten är utformad att ge kunderna möjlighet att (skala Elastiskt och oberoende) dataflöde och lagring över valfritt antal geografiska regioner. Azure Cosmos DB är den första globalt distribuerade databastjänsten på marknaden idag som erbjuder omfattande [serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) omfattar dataflöde, svarstider, tillgänglighet och konsekvens. 

Azure Cosmos DB är en globalt distribuerad databas som används i en mängd olika program och användningsfall. Det är ett bra val för någon [serverlös](https://azure.com/serverless) program som behöver korta svarstider ordning millisekunder och behöver skalas globalt och snabbt. Den har stöd för flera data modeller (nyckel värde, dokument, grafer och kolumner) och många API: er för data åtkomst, inklusive [Azure Cosmos DB s API för MongoDB](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)och [tabeller API](table-introduction.md) internt, och i en utöknings bar sätt. 

Här följer några attribut för Azure Cosmos DB som gör att den passar bra för program med höga prestanda med globala ambitionsnivå.

* Azure Cosmos DB partitionerar har inbyggt dina data för hög tillgänglighet och skalbarhet. Azure Cosmos DB erbjuder garantier på 99,99% tillgänglighet, dataflöde, kort svarstid och konsekvens på alla konton i en region och alla konton i flera regioner med Avslappnad konsekvens och 99,999% läsningstillgänglighet för alla databaskonton för flera regioner konton.
* Azure Cosmos DB har SSD-uppbackad lagring med låg latens ordning millisekunder svarstider.
* Azure Cosmos DB: s stöd för konsekvensnivåer som slutlig, konsekvent prefix, session och bunden utgång ger fullständig flexibilitet och låg kostnad för prestanda-förhållande. Inga databastjänst erbjuder så mycket flexibilitet som Azure Cosmos DB i nivåer konsekvens. 
* Azure Cosmos DB har en flexibel data-vänlig prismodell som taxor för lagring och dataflöde oberoende av varandra.
* Azure Cosmos DB reserverat Dataflödesmodell kan du tänka i termer av antalet läsningar/skrivningar i stället för CPU/minne/IOPs för den underliggande maskinvaran.
* Azure Cosmos DB-design kan du skala till begäran om stora volymer i den ordning som biljoner begäranden per dag.

Dessa attribut som är nödvändiga i webb-, mobil, spel och IoT-program som kräver korta svarstider och behöver hantera stora mängder läsningar och skrivningar.

## <a name="iot-and-telematics"></a>IoT och telematik
IoT-användningsfall ofta delar vissa mönster i hur de mata in, bearbeta, och lagra data.  Dessa system måste först att mata in ökningar av data från enheten sensorer över olika språk. Sedan dessa system bearbeta och analysera data för att härleda insikter i realtid. Data sedan arkiverad till kall lagring för batchanalyser. Microsoft Azure erbjuder omfattande tjänster som kan användas för användnings fall i IoT, inklusive Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight och Power BI. 

![Azure Cosmos DB IoT-Referensarkitektur](./media/use-cases/iot.png)

Ökningar av data kan matas in av Händelsehubbar i Azure som den erbjuder hög genomströmning datainmatning med kort svarstid. Data som samlas in och som behöver bearbetas för insikter i realtid som kan vara går till Azure Stream Analytics för analys i realtid. Data kan läsas in i Azure Cosmos DB för ad hoc-frågor. När data har lästs in till Azure Cosmos DB, är data redo att efterfrågas. Dessutom kan kan nya data och ändringar i befintliga data läsas på ändringsfeed. Ändra feed är en beständigt, bifogad logg som lagrar ändringar i Cosmos-behållare i sekventiell ordning. Alla data eller bara ändringar av data i Azure Cosmos DB kan användas som för referensdata som en del av analys i realtid. Dessutom kan kan data ytterligare finjusteras och bearbetas genom att ansluta Azure Cosmos DB-data till HDInsight för Pig, Hive eller Map/Reduce-jobb.  Förfinade data läses sedan tillbaka till Azure Cosmos DB för rapportering.   

Ett exempel IoT-lösning med Azure Cosmos DB-, EventHubs- och Storm, finns det [hdinsight-storm-exempel arkivet på GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Läs mer på Azure-erbjudanden för IoT, [skapa i Sakernas Internet](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Handel och marknadsföring
Azure Cosmos DB är vanliga i Microsofts egen e-handelsplattformar, som kör Windows Store och XBox Live. Den används också i detaljhandel för att lagra data catalog och för händelsekällor i ordning bearbetningspipelines.

Användningsscenarier för katalogen data innefattar lagring och hämtning av en uppsättning attribut för entiteter som personer, platser och produkter. Några exempel på katalogdata är användarkonton, produktkataloger, IoT-enhetsregister och faktura material system. Attribut för den här informationen kan variera och kan ändras med tiden så att de passar programkrav.

Överväg ett exempel på en produktkatalog för en bil delar leverantör. Varje del kan ha sin egen attribut utöver de vanliga attribut som delar alla delar. Attribut för en viss del kan dessutom ändra följande året när en ny modell släpps. Azure Cosmos DB stöder flexibla scheman och hierarkiska data och därmed den passar bra för att lagra data för produkt-katalogen.

![Azure Cosmos DB detaljhandel catalog-Referensarkitektur](./media/use-cases/product-catalog.png)

Azure Cosmos DB används ofta för händelsekällor till power händelsedriven arkitektur med hjälp av dess [ändringsflödet](change-feed.md) funktioner. Ändringsflöde innehåller underordnade mikrotjänster möjlighet att på ett tillförlitligt sätt och inkrementellt läsa infogningar och uppdateringar (till exempel ordning händelser) som görs till Azure Cosmos DB. Den här funktionen kan användas för att ange en beständig händelselagret som en asynkron meddelandekö för tillstånd föränderliga händelser och enheten arbetsflödet för orderbearbetningen mellan många mikrotjänster (som kan implementeras som [serverlösa Azure Functions](https://azure.com/serverless)).

![Azure Cosmos DB ordning pipeline-Referensarkitektur](./media/use-cases/event-sourcing.png)

Data som lagras i Azure Cosmos DB kan dessutom integreras med HDInsight för analys av stordata via Apache Spark-jobb. Mer information om Spark-Anslutningsapp för Azure Cosmos DB finns [kör ett Spark-jobb med Cosmos DB och HDInsight](spark-connector.md).

## <a name="gaming"></a>Spel
Databasnivån är en fundamental komponent i spelappar. Moderna spel utföra grafiska bearbetning av klienter för mobila/konsolen, men förlitar sig på molnet för att leverera anpassade och personligt anpassad innehåll som statistik i spelet, integrering av sociala media och hög Poängrekord. Spel kräver ofta svarstider på ensiffriga millisekunder för läsningar och skrivningar för att tillhandahålla en engagerande i spelet upplevelse. En game databasen måste vara snabba och att kunna hantera massiva tillfälliga trafiktopparna vid begäranhastigheter när nya spel startar och funktionsuppdateringar.

Azure Cosmos DB används av spel som [det promenad döda: Ingen man är jord](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) av [Nästa spel](https://www.nextgames.com/)och [Halo 5: Skyddare](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB ger följande fördelar för spelutvecklare:

* Azure Cosmos DB kan prestanda kan skalas upp eller ned Elastiskt. På så sätt kan spel att hantera uppdatering profil och statistik från flera miljoner samtidiga spelare genom att göra en enda API-anrop.
* Azure Cosmos DB stöder millisekund läsningar och skrivningar för att undvika eventuella beräkningstider under spelet.
* Azure Cosmos DB automatisk indexering tillåter filtrering mot flera olika egenskaper i realtid, till exempel hitta spelare med sina interna player ID: N eller deras GameCenter, Facebook, Google-ID: N eller fråga baserat på player medlemskap i en guild. Detta är möjligt utan att skapa komplexa indexering eller infrastruktur för horisontell partitionering.
* Sociala funktioner inklusive i spelet chattmeddelanden, player guild medlemskap, utmaningar som slutförts, hög Poängrekord och sociala diagram är enklare att implementera med ett flexibelt schema.
* Azure Cosmos DB som en hanterad platform-as-a-service (PaaS) krävde minimal installation och hantering fungerar för att möjliggöra snabb iteration och minska tiden till marknaden.

![Referensarkitektur för Azure Cosmos DB-spel](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webb- och mobilprogram
Azure Cosmos DB används ofta i webbprogram och mobilappar och passar bra för modellering kommunikation, integrering med tjänster från tredje part och för att skapa omfattande, anpassade upplevelser. SDK: er för Cosmos DB kan vara använda build omfattande iOS och Android-program med populära [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociala program
Ett vanligt användningsfall för Azure Cosmos DB är att lagra och fråga efter användargenererat innehåll (UGC) för webb-, mobil- och sociala media-program. Några exempel på UGC är chatt-sessioner, tweets, blogginlägg, omdömen och kommentarer. UGC i sociala media-program är ofta en blandning av friformstext, egenskaper, taggar och relationer som inte begränsas av fasta struktur. Innehåll, till exempel chattar, kommentarer och inlägg kan lagras i Cosmos DB utan omvandlingar eller komplexa objektet till relationella mappning lager.  Egenskaper för data kan läggas till eller ändras enkelt för att matcha kraven som utvecklare kan iterera över programkoden, därför befordrar snabb utveckling.  

Program som integreras med tredje parts sociala nätverk måste svara på förändrade scheman från dessa nätverk. Eftersom data indexeras automatiskt som standard i Cosmos DB, är data redo att efterfrågas när som helst. Därför kan har dessa program du flexibiliteten att hämta projektioner enligt deras respektive behov.

Många av de sociala program körs i global skala och kan den orsaka oförutsägbara användningsmönster. Flexibilitet vid skalning av datalagret är viktigt eftersom programnivån skalas så att den matchar användarkrav.  Du kan skala ut genom att lägga till ytterligare data-partitionerna under ett Cosmos DB-konto.  Dessutom kan du också skapa ytterligare Cosmos DB-konton i flera regioner. Cosmos DB-tjänsten regiontillgänglighet finns i [Azure-regionerna](https://azure.microsoft.com/regions/#services).

![Azure Cosmos DB web app-Referensarkitektur](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personanpassning
Nuförtiden, medföljer moderna program komplexa vyer och upplevelser. Det här är vanligtvis dynamiska restaurang användarinställningar eller sinnesstämningar- och anpassning av behov. Därför måste program för att kunna hämta personanpassade inställningar effektivt för att rendera UI-element och upplevelser snabbt. 

JSON, ett format som stöds av Cosmos DB är en effektiv format representerar Användargränssnittet layoutdata som den är inte bara lätt, men även kan enkelt kan tolkas av JavaScript. Cosmos DB erbjuder justerbara konsekvensnivåer att snabb läsningar med låg latens skrivningar. Därför kan är lagra Användargränssnittet layoutdata, inklusive personliga inställningar som JSON-dokument i Cosmos DB ett effektivt sätt att hämta dessa data i ledningen.

![Azure Cosmos DB web app-Referensarkitektur](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Nästa steg

* Kom igång med Azure Cosmos DB, följa våra [snabbstarter](create-sql-api-dotnet.md), som beskriver hur du skapar ett konto och komma igång med Cosmos DB.

* Om du vill läsa mer om kunder som använder Azure Cosmos DB kan du gå till sidan med [fallstudier för kunder](https://azure.microsoft.com/en-us/case-studies/?service=cosmos-db) .
