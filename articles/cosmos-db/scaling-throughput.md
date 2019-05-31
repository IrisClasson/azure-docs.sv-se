---
title: Skalning av dataflöden i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB kan skalas dataflöde Elastiskt
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: f930b5c478cc880952b4559be4c6647b260efcf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243496"
---
# <a name="globally-scale-provisioned-throughput"></a>Skala etablerat dataflöde globalt 

I Azure Cosmos DB, etablerat dataflöde representeras som begäran begärandeenheter/sekund (RU/s eller pluralform ru: er). RU: er mäta kostnaden för både läs- och skrivåtgärder mot din Cosmos-behållare som du ser i följande bild:

![Begäransenheter](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Du kan etablera ru: er i en Cosmos-behållare eller en Cosmos-databas. RU som etableras i en behållare är endast tillgängligt för de åtgärder som utförs på den behållaren. RU som etableras för en databas delas mellan alla behållare i databasen (förutom för alla behållare som exklusivt tilldelade ru: er).

För att skala Elastiskt dataflöde, kan du öka eller minska etablerade RU/s när som helst. Mer information finns i [How-to etablera dataflöde](set-throughput.md) och för att skala Cosmos-behållare och databaser. Globalt skalning av dataflöde, kan du lägga till eller ta bort regioner från ditt Cosmos-konto när som helst. Mer information finns i [Lägg till/ta bort regioner från ditt databaskonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associera flera regioner med ett Cosmos-konto är viktigt i många scenarier – få kort svarstid och [hög tillgänglighet](high-availability.md) runt om i världen.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>hur etablerade dataflödet som distribueras i flera regioner

Om du etablerar *”R”* ru: er på en Cosmos-behållare (eller databas), Cosmos DB garanterar att *”R”* ru: er är tillgängliga i *varje* region som är associerade med ditt Cosmos-konto. Varje gång du lägger till en ny region till ditt konto, Cosmos DB automatiskt etablerar *”R”* ru: er i regionen nyligen tillagda. De åtgärder som utförs mot din Cosmos-behållare är garanterade att hämta *”R”* ru: er i varje region. Du kan inte selektivt tilldela ru: er i en specifik region. RU som etableras i en Cosmos-behållare (eller databas) har etablerats i alla regioner som är associerat med ditt Cosmos-konto.

Om vi antar att en Cosmos-behållare har konfigurerats med *”R”* ru: er och det finns *n* regioner som associeras med Cosmos-kontot, sedan:

- Om Cosmos-konto har konfigurerats med en enda skrivregionen, totalt antal ru globalt på behållaren = *R* x *N*.

- Om Cosmos-konto har konfigurerats med flera Skriv-regioner, totalt antal ru globalt på behållaren = *R* x (*N*+ 1). De ytterligare *R* ru: er som automatiskt tilldelas processen Uppdateringskonflikter och anti entropi trafik över regionerna.

Ditt val av [konsekvensmodell](consistency-levels.md) påverkar också dataflödet. Du kan få cirka 2 x läsningsgenomströmning som är mindre konsekvensnivåer (t.ex. *session*, *konsekvent prefix* och *slutlig* konsekvens) jämfört med starkare konsekvensnivåer (t.ex. *begränsad föråldring* eller *stark* konsekvens).

## <a name="next-steps"></a>Nästa steg

Därefter kan du lära dig hur du konfigurerar dataflöde på en behållare eller databasen:

* [Hämta och ange dataflöde för behållare och databaser](set-throughput.md) 

