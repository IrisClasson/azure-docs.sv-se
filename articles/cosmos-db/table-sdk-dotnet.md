---
title: Azure Cosmos DB Table API .NET SDK och resurser
description: Läs mer om Azure Cosmos DB Table API inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: db7cc556525ab57f14984232bf1797764865fca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606248"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB-tabell .NET API: Ladda ned och viktig information

> [!div class="op_single_selector"]
> * [NET](table-sdk-dotnet.md)
> * [.NET-standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API-dokumentation**|[.NET API-referensdokumentation](https://aka.ms/acdbtableapiref)|
|**Snabbstart**|[Azure Cosmos DB: Skapa en app med .NET och tabell-API](create-table-dotnet.md)|
|**Självstudie**|[Azure Cosmos DB: Utveckla med tabell-API i .NET](tutorial-develop-table-dotnet.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) är i underhållsläge läge och det upphör att gälla snart. Uppgradera till det nya .NET Standard-biblioteket [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) fortsätta att få de senaste funktionerna som stöds av tabell-API.

> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="release-notes"></a>Viktig information

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Felkorrigeringar

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Stöd har lagts till flera regioner skrivning
* Fast NuGet-paketberoenden på Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Fast NuGet-paketberoenden på Microsoft.Azure.Storage.Common och Microsoft.Azure.DocumentDB.
* Felkorrigeringar på tabellen serialisering när JsonConvert.DefaultSettings konfigureras.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Har lagts till verifieringen av felaktig ETAGs i direkt-läge.
* Åtgärdat fel på grund av LINQ-frågan i Gateway-läge.
* Synkron API: er kan nu köra på trådpoolen med SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Add TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism, and TableQueryContinuationTokenLimitInKb to TableRequestOptions
* Felkorrigeringar

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Allmänt tillgänglig version

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview

* Inledande förhandsversion

## <a name="release-and-retirement-dates"></a>Versionen och dras tillbaka datum

Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Den `Microsoft.Azure.CosmosDB.Table` biblioteket är för närvarande tillgängligt för .NET Framework, och är i underhållsläge och upphör att gälla snart. Nya funktioner och funktionalitet och optimeringar läggs endast till .NET Standard-biblioteket [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), som sådana du rekommenderas att du uppgraderar till [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Den [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) förhandsversion paketet är inaktuell. WindowsAzure.Storage-PremiumTable SDK kommer att dras tillbaka den 15 November 2018, vid vilken tid förfrågningar om att dras tillbaka SDK: N tillåts inte. 

Alla begäranden till Azure Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.
<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.1.0](#2.1.0) |Den 22 januari 2019|01 april 2020 |
| [2.0.0](#2.0.0) |Den 26 september 2018|01 mars 2020 |
| [1.1.3](#1.1.3) |17 juli 2018|01 december 2019 |
| [1.1.1](#1.1.1) |26 mars 2018|01 december 2019 |
| [1.1.0](#1.1.0) |21 februari 2018|01 december 2019 |
| [1.0.0](#1.0.0) |Den 15 november 2017|Den 15 november 2019 |
| 0.9.0-Preview |11 november 2017 |11 november 2019 |

## <a name="troubleshooting"></a>Felsökning

Om du får felet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

När du försöker använda Microsoft.Azure.CosmosDB.Table NuGet-paketet har du två alternativ för att åtgärda problemet:

* Använd paketet hantera konsolen för att installera Microsoft.Azure.CosmosDB.Table-paketet och dess beroenden. Om du vill göra detta, skriver du följande i Package Manager-konsolen för din lösning. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Använder din önskade hanteringsverktyg för NuGet-paketet, installera Microsoft.Azure.Storage.Common NuGet-paketet innan du installerar Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också

Läs mer om Azure Cosmos DB Table API i [introduktion till Azure Cosmos DB Table API](table-introduction.md). 