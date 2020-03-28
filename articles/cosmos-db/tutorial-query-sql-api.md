---
title: 'Självstudiekurs: Hur frågar du med SQL i Azure Cosmos DB?'
description: 'Självstudiekurs: Lär dig hur du frågar med SQL-frågor i Azure Cosmos DB med thw-frågelekplats'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 7e83ed0f9e635ed24b7e6115eeaaa9057d422c69
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74870079"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Självstudie: Fråga Azure Cosmos DB med hjälp av SQL API

Azure Cosmos DB [SQL API](documentdb-introduction.md) stöder frågedokument med hjälp av SQL. Den här artikeln innehåller ett dokumentexempel och två exempel på SQL-frågor och resultat.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Fråga efter data med SQL

## <a name="sample-document"></a>Exempeldokument

SQL-frågorna i artikeln använder följande exempeldokument.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>Var kan jag köra SQL-frågor?

Du kan köra frågor med Datautforskaren i Azure-portalen via [REST-API och SDK](sql-api-sdk-dotnet.md), och även [Query Playground](https://www.documentdb.com/sql/demo) som kör frågor på en befintlig uppsättning exempeldata.

Mer information om SQL-frågor finns i:
* [SQL-fråga och SQL-syntax](sql-query-getting-started.md)

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du har ett konto för Azure Cosmos DB och en samling. Har du detta? Slutför [Snabbstart på 5 minuter](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Exempelfråga 1

I exemplet på familjedokumentet ovan returnerar följande SQL-fråga dokument där ID-fältet matchar `WakefieldFamily`. Eftersom det är en `SELECT *`-instruktion är utdatan från frågan ett komplett JSON-dokument:

**Fråga**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Results**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Exempelfråga 2

Nästa fråga returnerar alla angivna namn på barnen i familjen vars ID matchar `WakefieldFamily`, sorterade efter deras klass.

**Fråga**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Results**

[ { "givenName": "Jesse" }, { "givenName": "Lisa" } ]


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågar med SQL  

Du kan nu fortsätta till nästa självstudie för att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-sql-api.md)

