---
title: Delad databas
description: Azure Synapse Analytics tillhandahåller en delad metadata modell där du skapar en databas i Apache Spark, vilket gör den tillgänglig från SQL on-demand (för hands version) och SQL-poolens motorer.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 196577741ae1560232f8ae193aacd51a446431c8
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385542"
---
# <a name="azure-synapse-analytics-shared-database"></a>Delad Azure Synapse Analytics-databas

Med Azure Synapse Analytics kan olika data behandlings arbets Ytors motorer dela databaser och tabeller mellan dess Spark-pooler (för hands version) och SQL on-demand (för hands version)-motorn.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

En databas som skapats med ett Spark-jobb blir synlig med samma namn på alla aktuella och framtida Spark-pooler (för hands version) i arbets ytan samt SQL-motorn på begäran.

Spark-standarddatabasen, `default` som anropas, visas också i SQL-kontexten på begäran som en databas som kallas `default` .

Eftersom databaserna synkroniseras till SQL på begäran asynkront kommer det att finnas en fördröjning tills de visas.

## <a name="manage-a-spark-created-database"></a>Hantera en spark-skapad databas

Använd Spark för att hantera Spark-skapade databaser. Du kan t. ex. ta bort den via ett Spark-jobb och skapa tabeller i det från Spark.

Om du skapar objekt i en spark-databas som skapats med SQL på begäran, eller om du försöker släppa databasen, kommer åtgärden att lyckas. Men den ursprungliga Spark-databasen ändras inte.

## <a name="how-name-conflicts-are-handled"></a>Hur namn konflikter hanteras

Om namnet på en spark-databas står i konflikt med namnet på en befintlig SQL-databas på begäran, läggs ett suffix till i SQL på begäran till Spark-databasen. Suffixet i SQL på begäran är `_<workspace name>-ondemand-DefaultSparkConnector` .

Om t. ex. en spark-databas som heter `mydb` skapas i Azure DataSynapses-arbetsytan `myws` och det redan finns en SQL-databas på begäran med det namnet, måste Spark-databasen i SQL på begäran refereras till med namnet `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Varning: du bör inte ta ett beroende på detta beteende.

## <a name="security-model"></a>Säkerhetsmodell

Spark-databaser och-tabeller, tillsammans med deras synkroniserade representationer i SQL-motorn, kommer att skyddas på den underliggande lagrings nivån.

Det säkerhets objekt som skapar en databas anses vara ägare till databasen och har alla rättigheter till databasen och dess objekt.

Om du vill ge ett säkerhets objekt, till exempel en användare eller en säkerhets grupp, åtkomst till en databas, anger du rätt POSIX-mapp och fil behörigheter för de underliggande mapparna och filerna i `warehouse` katalogen. 

För att ett säkerhets objekt ska kunna läsa en tabell i en databas måste till exempel alla mappar som börjar på databasmappen i `warehouse` katalogen ha `X` och `R` behörighet som tilldelas till säkerhets objekt. Dessutom kräver filer (till exempel tabellens underliggande datafiler) `R` behörigheter. 

Om ett säkerhets objekt kräver möjlighet att skapa objekt eller släppa objekt i en databas, krävs ytterligare `W` behörigheter för mapparna och filerna i `warehouse` mappen.

## <a name="examples"></a>Exempel

### <a name="create-and-connect-to-spark-database-with-sql-on-demand"></a>Skapa och Anslut till Spark-databasen med SQL på begäran

Skapa först en ny Spark-databas `mytestdb` som heter med ett Spark-kluster som du redan har skapat i din arbets yta. Du kan till exempel använda en spark C#-anteckningsbok med följande .NET for Spark-instruktion:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Efter en kort fördröjning kan du se databasen från SQL på begäran. Kör till exempel följande uttryck från SQL på begäran.

```sql
SELECT * FROM sys.databases;
```

Kontrol lera att `mytestdb` ingår i resultaten.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om delade metadata för Azure Synapse Analytics](overview.md)
- [Läs mer om delade metadata-tabeller för Azure Synapse Analytics](table.md)
