---
title: Node.js exempel för att hantera data i Azure Cosmos Database
description: Hitta Node.js-exempel på GitHub för vanliga uppgifter i Azure Cosmos DB, inklusive CRUD-åtgärder.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.custom: devx-track-javascript
ms.openlocfilehash: 2c0e2b7a63f02559f95b647bc1b4ef46b8a157cd
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421832"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Node.js exempel för att hantera data i Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET v2 SDK-exempel](sql-api-dotnet-samples.md)
> * [.NET v3 SDK-exempel](sql-api-dotnet-v3sdk-samples.md)
> * [Exempel för Java v4 SDK](sql-api-java-sdk-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Exempellösningarna som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser på GitHub-lagringsplatsen [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Node.js-exempelprojektfilerna.
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du behöver även [JavaScript SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Därmed gör exemplen flera anrop till [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest). Varje gång det sker faktureras din prenumeration för en timmes användning per prestandanivå för den container som skapas.
   > 
   > 

## <a name="database-examples"></a>Databasexempel

[DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) -filen visar hur du utför CRUD-åtgärder på databasen. Information om Azure Cosmos-databaser innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en databas om den inte finns](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#createifnotexists-databaserequest--requestoptions-) |
| [Lista databaser för ett konto](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#readall-feedoptions-) |
| [Läsa en databas via ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest#read-requestoptions-) |
| [Ta bort en databas](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest#delete-requestoptions-) |

## <a name="container-examples"></a>Containerexempel

[ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) -filen visar hur du utför CRUD-åtgärder på behållaren. Mer information om Azure Cosmos-samlingarna innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en container om den inte finns](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#createifnotexists-containerrequest--requestoptions-) |
| [Lista containrar för ett konto](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#readall-feedoptions-) |
| [Läs en behållar definition](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest#read-requestoptions-) |
| [Ta bort en container](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest#delete-requestoptions-) |

## <a name="item-examples"></a>Objektexempel

[ItemManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) -filen visar hur du utför CRUD-åtgärder på objektet. Information om Azure Cosmos-dokumenten innan du kör följande exempel finns i avsnittet [arbeta med databaser, behållare och objekt](databases-containers-items.md) konceptuell artikel. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa objekt](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest#create-t--requestoptions-) |
| [Läsa alla objekt i en container](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest#readall-feedoptions-) |
| [Läsa ett objekt via ID](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-) |
| [Läsa objekt bara om objektet har ändrats](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Fråga för dokument](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Ersätta ett objekt](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Ersätta objekt med villkorlig ETag-kontroll](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Ta bort ett objekt](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Indexeringsexempel

[IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) -filen visar hur du hanterar indexering. Om du vill veta mer om indexering i Azure Cosmos DB innan du kör följande exempel, se [indexerings principer](index-policy.md), [indexerings typer](index-types.md)och [indexerings Sök vägar](index-paths.md) konceptuella artiklar. 

| Uppgift | API-referens |
| --- | --- |
| [Indexera ett enskilt objekt manuellt](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Undanta ett enskilt objekt manuellt från indexet](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Undanta en sökväg från indexet](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Skapa ett intervallindex i en strängsökväg](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Skapa en container med standardindexpolicy och uppdatera den sedan online](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Programmeringsexempel på serversidan

[Index. TS](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) -filen för [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) -projektet visar hur du utför följande uppgifter. Mer information om programmering på Server sidan i Azure Cosmos DB innan du kör följande exempel finns i avsnittet [lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md) konceptuell artikel. 

| Uppgift | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Köra en lagrad procedur](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Mer information om programmering på serversidan finns på sidan om [Azure Cosmos DB-programmering på serversidan: lagrade procedurer, databasutlösare och UDF:er](stored-procedures-triggers-udfs.md).

