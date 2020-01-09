---
title: Använda Azure Cosmos DB Tabell-API och Azure Table Storage med python
description: Lagra strukturerade data i molnet med Azure Table Storage eller Azure Cosmos DB Table API.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 6c01b9581795f4ac74bd74757b9116c0d5df586d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444761"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Komma igång med Azure Table Storage och Azure Cosmos DB Table-API:et med hjälp av Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Tjänsterna Azure Table Storage och Azure Cosmos DB lagrar strukturerade NoSQL-data i molnet och tillhandahåller ett nyckel-/attributlager med en schemalös design. Eftersom Table Storage och Azure Cosmos DB är schemalösa kan du enkelt anpassa dina data baserat på hur ditt program utvecklas. Åtkomsten till data i Table Storage och Table-API:et är snabb och kostnadseffektiv för många typer av program, och medför normalt lägre kostnad än traditionell SQL för liknande datavolymer.

Du kan använda Table Storage eller Azure Cosmos DB för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som din tjänst behöver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

### <a name="about-this-sample"></a>Om det här exemplet
Det här exemplet beskriver hur du använder [Azure Cosmos DB Table SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) i vanliga Azure Table Storage-scenarier. SDK-paketets namn indikerar att det ska användas med Azure Cosmos DB, men det fungerar med både Azure Cosmos DB och Azure Table Storage. Enda skillnaden är att tjänsterna har unika slutpunkter. De olika scenarierna utforskas med hjälp av Python-baserade exempel som beskriver hur du:
* Skapar och tar bort tabeller
* Infogar och kör frågor mot entiteter
* Ändrar entiteter

Vi rekommenderar att du använder [referensen för Azure Cosmos DB SDK för Python API](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python) när du går igenom scenarierna i det här exemplet.

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna följa med i det här exemplet:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 eller 3.6
- [Azure Cosmos DB Table SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Detta SDK fungerar med både Azure Table Storage och Azure Cosmos DB Table-API:et.
- Ett [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md) eller [Azure Cosmos DB-konto](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Skapa ett Azure-tjänstkonto
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Skapa ett Azure Cosmos DB Table API-konto
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Installera Azure Cosmos DB Table SDK för Python

När du har skapat ett lagringskonto är nästa steg att installera [Microsoft Azure Cosmos DB Table SDK för Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Mer information om hur du installerar SDK-paketet finns i filen [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) i databasen för Cosmos DB Table SDK för Python på GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importera TableService- och Entity-klasserna

Om du vill arbeta med entiteter i Azure Table service i python använder du [TableService][py_TableService] -och [Entity][py_Entity] -klasserna. Importera båda klasserna genom att lägga till följande kod överst i Python-filen:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Ansluta till Azure Table Storage

Du ansluter till tjänsten Table Storage i Azure Storage genom att skapa ett [TableService][py_TableService]-objekt som innehåller namnet på ditt lagringskonto och din kontonyckel. Ersätt `myaccount` och `mykey` med kontonamnet och nyckeln.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB

Du ansluter till Azure Cosmos DB genom att kopiera den primära anslutningssträngen från Azure Portal och skapar sedan ett [TableService][py_TableService]-objekt genom att använda den kopierade anslutningssträngen:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Skapa en tabell

Anropa [create_table][py_create_table] för att skapa tabellen.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Lägga till en entitet i en tabell

Om du vill lägga till en entitet skapar du först ett objekt som representerar entiteten och skickar sedan objektet till [metoden TableService. insert_entity][py_TableService]. Objektet Entity kan vara en ord lista eller ett objekt av typen [entitet][py_Entity]och definierar entitetens egenskaps namn och värden. Varje entitet måste innehålla de obligatoriska [PartitionKey- och RowKey](#partitionkey-and-rowkey)-egenskaperna, utöver andra egenskaper som du definierar för entiteten.

I det här exemplet skapas ett Dictionary-objekt som representerar en entitet och skickar det sedan till [insert_entity][py_insert_entity] -metoden för att lägga till det i tabellen:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Det här exemplet skapar ett [entitet][py_Entity] -objekt och skickar det sedan till [insert_entity][py_insert_entity] -metoden för att lägga till det i tabellen:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey och RowKey

Du måste ange både en **PartitionKey**- och en **RowKey**-egenskap för varje entitet. Dessa är entiteternas unika identifierare eftersom de tillsammans bildar primärnyckeln för en entitet. Du kan fråga mycket snabbare med dessa värden än du kan fråga andra entitetsegenskaper eftersom endast dessa egenskaper indexeras.

Table Storage använder tabellen **PartitionKey** för att effektivt distribuera tabellentiteter mellan lagringsnoder. Entiteter som har samma **PartitionKey** lagras på samma nod. **RowKey** är det unika ID:t för entiteten i den partition som egenskapen hör till.

## <a name="update-an-entity"></a>Uppdatera en entitet

Om du vill uppdatera alla egenskaps värden för en entitet anropar du [update_entity][py_update_entity] -metoden. Det här exemplet beskriver hur du ersätter en befintlig entitet med en uppdaterad version:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Uppdateringen misslyckas om entiteten som ska uppdateras inte redan finns. Om du vill lagra en entitet oavsett om den finns eller inte, använder du [insert_or_replace_entity][py_insert_or_replace_entity]. I följande exempel ersätter det första anropet den befintliga entiteten. Det andra anropet infogar en ny entitet eftersom det inte finns någon entitet med angiven partitionsnyckel (PartitionKey) och radnyckel (RowKey) i tabellen.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Metoden [update_entity][py_update_entity] ersätter alla egenskaper och värden för en befintlig entitet, som du även kan använda för att ta bort egenskaper från en befintlig entitet. Du kan använda metoden [merge_entity][py_merge_entity] om du vill uppdatera en befintlig entitet med nya eller ändrade egenskaps värden utan att helt ersätta entiteten.

## <a name="modify-multiple-entities"></a>Ändra flera entiteter

Du kan skicka flera åtgärder tillsammans i en batch för att säkerställa atomisk bearbetning av en begäran i Table Storage. Använd först klassen [TableBatch][py_TableBatch] för att lägga till flera åtgärder i en enda batch. Anropa sedan [TableService][py_TableService]. [commit_batch][py_commit_batch] att skicka in åtgärderna i en atomisk åtgärd. Alla entiteter som ska ändras tillsammans i en batch måste finnas i samma partition.

I det här exemplet läggs två entiteter till i en batch:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Batchar kan också användas med kontexthanterarsyntaxen:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Fråga efter en entitet

Om du vill fråga efter en entitet i en tabell skickar du dess PartitionKey och RowKey till [TableService][py_TableService]. [get_entity][py_get_entity] metod.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Fråga efter en uppsättning entiteter

Du kan hämta en uppsättning entiteter genom att ange en filtersträng med parametern **filter**. Det här exemplet hämtar alla aktiviteter i Seattle genom att tillämpa ett filter på PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Fråga en deluppsättning entitetsegenskaper

Du kan också begränsa vilka egenskaper som returneras för varje entitet i en fråga. Den här tekniken, kallad *projektion*, minskar bandbredden och kan förbättra frågeprestanda, i synnerhet för stora entiteter eller resultatuppsättningar. Använd parametern **select** och ange namnen på egenskaperna som ska returneras till klienten.

Frågan i följande kod returnerar bara beskrivningarna av entiteter i tabellen.

> [!NOTE]
> Följande kodfragment fungerar bara mot Azure Storage. Det kan inte användas med lagringsemulatorn.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Ta bort en entitet

Ta bort en entitet genom att skicka dess **PartitionKey** och **RowKey** till metoden [delete_entity][py_delete_entity] .

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Ta bort en tabell

Om du inte längre behöver en tabell eller någon av de entiteter som finns i den, anropar du [delete_table][py_delete_table] -metoden för att ta bort tabellen från Azure Storage permanent.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar – Utveckla med Table-API:et](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Referens för Azure Cosmos DB SDK för Python API](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Python Developer Center](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): Ett kostnadsfritt, plattformsoberoende program som låter dig arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Arbeta med Python i Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
