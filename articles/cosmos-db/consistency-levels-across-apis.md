---
title: Konsekvensnivåer och API:er för Azure Cosmos DB
description: 'För att förstå konsekvensnivåerna i API: er i Azure Cosmos DB.'
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: 1129152c1823fbffb3d6c9ec918d7b8cb4426bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235616"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Azure Cosmos DB erbjuder inbyggt stöd för wire protocol-kompatibla API: er för populära databaser. Dessa inkluderar MongoDB, Apache Cassandra, Gremlin och Azure Table storage. De här databaserna erbjuder inte exakt definierade konsekvensmodeller eller garantier som omfattas av serviceavtalet för konsekvensnivåerna. De tillhandahåller normalt endast en delmängd av de fem konsekvensmodeller erbjuds av Azure Cosmos DB. 

När du använder SQL-API, Gremlin-API och en tabell-API, används standard-konsekvensnivå som konfigurerats för Azure Cosmos-kontot. 

När du använder Cassandra API eller Azure Cosmos DB API för MongoDB, hämta en fullständig uppsättning konsekvensnivåer som Apache Cassandra och MongoDB, finns, med ännu starkare konsekvens och garantier för hållbarhet i program. Det här dokumentet visar motsvarande Azure Cosmos DB-konsekvensnivåer för Apache Cassandra och MongoDB-konsekvensnivåer.


## <a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Azure Cosmos DB-konsekvensnivåer

Till skillnad från AzureCosmos DB ger Apache Cassandra internt inte exakt definierade konsekvensgarantier.  Apache Cassandra ger i stället en konsekvensnivå för skrivning och en skrivskyddad konsekvensnivå, aktivera hög tillgänglighet, konsekvens och svarstid-kompromisser. När du använder Azure Cosmos DB Cassandra-API: 

* Konsekvensnivå skrivning av Apache Cassandra mappas till standard-konsekvensnivå som konfigurerats på ditt Azure Cosmos-konto. 

* Azure Cosmos DB mappar dynamiskt Läs konsekvensnivå som anges av Cassandra-klientdrivrutinen till en Azure Cosmos DB-konsekvensnivåer konfigureras dynamiskt på en läsbegäran. 

I följande tabell visar hur de inbyggda Cassandra-konsekvensnivåerna mappas till Azure Cosmos DB-konsekvensnivåer när du använder Cassandra API:  

[![Cassandra konsekvens Modellmappning](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mappning mellan MongoDB och Azure Cosmos DB-konsekvensnivåer

Till skillnad från Azure Cosmos DB tillhandahåller inte den interna MongoDB exakt definierade konsekvensgarantier. I stället interna MongoDB kan du konfigurera följande konsekvensgarantier: ett problem för skrivning och ett Läs problem direktivet isMaster - att dirigera läsåtgärder till primära eller sekundära repliker för att uppnå den önskade konsekvensnivån. 

När du använder Azure Cosmos DB API för MongoDB, MongoDB-drivrutinen behandlar skrivregion som den primära repliken och alla andra regioner är skrivskyddade replik. Du kan välja vilken region som är associerade med ditt Azure Cosmos-konto som en primär replik. 

När du använder Azure Cosmos DB API för MongoDB:

* Skriv problem är mappad till standard-konsekvensnivå som konfigurerats på ditt Azure Cosmos-konto.
 
* Azure Cosmos DB mappar dynamiskt Läs problem som anges av MongoDB klientdrivrutinen till en Azure Cosmos DB-konsekvensnivåer som konfigureras dynamiskt på en läsbegäran. 

* Du kan kommentera en viss region som är associerade med ditt Azure Cosmos-konto som ”Master” genom att göra regionen som den första skrivbara regionen. 

Följande tabell visar hur interna MongoDB skrivning/läsning frågor mappas till Azure Cosmos-konsekvensnivåer när du använder Azure Cosmos DB API för MongoDB:

[![MongoDB konsekvens Modellmappning](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer och kompatibiliteten mellan Azure Cosmos DB API: er med öppen källkod-API: er. Se följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [MongoDB-funktioner som stöds av Azure Cosmos DB: s API för MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API](cassandra-support.md)