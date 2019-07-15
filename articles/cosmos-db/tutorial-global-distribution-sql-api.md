---
title: Självstudie om global distribution i Azure Cosmos DB för SQL API
description: Lär dig att konfigurera global distribution i Azure Cosmos DB med SQL API:et.
author: rimman
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c4ce60e3532179efe3ac68c21b32850e73f92a69
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881214"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Konfigurera global distribution i Azure Cosmos DB med SQL API:et

I den här artikeln visar vi hur du kan konfigurera global distribution i Azure Cosmos DB med Azure Portal och sedan ansluta med hjälp av SQL-API:et.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [SQL-API:erna](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Ansluta till en önskad region med hjälp av SQL-API:et

I syfte att dra nytta av den [globala distributionen](distribute-data-globally.md) kan klientprogrammen ange den beställda listan med inställningar för regioner som ska användas för att utföra dokumentåtgärder. Detta gör du genom att konfigurera anslutningspolicyn. Utifrån Azure Cosmos DB-kontokonfigurationen, den aktuella regionala tillgängligheten och den angivna listan över inställningar, så väljs den mest optimala slutpunkten för genomförande av skriv- och läsåtgärder av SQL-SDK:n.

Denna lista över inställningar anges när en anslutning initieras med SQL-SDK:erna. SDK:erna accepterar den valfria parametern PreferredLocations, som är en sorterad lista över Azure-regioner.

SDK:n skickar automatiskt alla skrivningar till den aktuella skrivregionen.

Alla läsningar skickas till den första tillgängliga regionen i PreferredLocations-listan. Om begäran misslyckas går klienten nedåt i listan till nästa region osv.

SDK:erna försöker bara läsa från de regioner som anges i PreferredLocations. Så om databaskontot t.ex. är tillgängligt i fyra regioner, men klienten enbart specificerar två läsregioner (skrivskyddade) för PreferredLocations, så hanteras inga läsningar från den läsregion som inte anges i PreferredLocations. Om de läsregioner som anges i PreferredLocations inte är tillgängliga så hanteras läsningarna från skrivregionen.

Programmet kan verifiera den aktuella skrivslutpunkt och lässlutpunkt som valts av SDK:n genom att kontrollera de två egenskaperna WriteEndpoint och ReadEndpoint, som är tillgängliga i SDK-version 1.8 och senare.

Om egenskapen PreferredLocations inte har angetts hanteras alla förfrågningar från den aktuella skrivregionen.

## <a name="net-sdk"></a>.NET SDK
SDK:n kan användas utan några kodändringar. I det här fallet styr SDK:n automatiskt både läsningar och skrivningar till den aktuella skrivregionen.

Parametern ConnectionPolicy för DocumentClient-konstruktorn har en egenskap som kallas Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations i .NET SDK version 1.8 och senare. Den här egenskapen är av typen samling `<string>` och bör innehålla en lista med regionnamn. Strängvärden som är formaterade efter regionnamnskolumnen på den [Azure-regionerna][regions] sida, utan blanksteg före eller efter först och sista tecknet respektive.

De aktuella skriv- och lässlutpunkterna är tillgängliga i DocumentClient.WriteEndpoint respektive DocumentClient.ReadEndpoint.

> [!NOTE]
> Slutpunkternas URL:er ska inte betraktas som långlivade konstanter. Tjänsten kan uppdatera dem när som helst. SDK:n hanterar sådana ändringar automatiskt.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejs-javascript-and-python-sdks"></a>Node.js-, JavaScript- och Python SDK: er
SDK:n kan användas utan några kodändringar. I det här fallet styr SDK:n automatiskt både läsningar och skrivningar till den aktuella skrivregionen.

Parametern ConnectionPolicy för DocumentClient-konstruktorn har en ny egenskap som kallas Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations i varje SDK av version 1.8 eller senare. Den här parametern är en matris med strängar som tar en lista med regionnamn. Namnen är formaterade efter regionnamnskolumnen på den [Azure-regionerna][regions] sidan. Du kan också använda de fördefinierade konstanterna i bekvämlighetsobjektet AzureDocuments.Regions

De aktuella skriv- och lässlutpunkterna är tillgängliga i DocumentClient.getWriteEndpoint respektive DocumentClient.getReadEndpoint.

> [!NOTE]
> Slutpunkternas URL:er ska inte betraktas som långlivade konstanter. Tjänsten kan uppdatera dem när som helst. SDK:n hanterar sådana ändringar automatiskt.
>
>

Nedan visas ett kodexempel för Node.js/Javascript. Python och Java följer samma mönster.

```JavaScript
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="rest"></a>REST
När ett databaskonto har gjorts tillgängligt i flera regioner, kan klienterna fråga efter dess tillgänglighet genom att utföra en GET-begäran för följande URI.

    https://{databaseaccount}.documents.azure.com/

Tjänsten returnerar en lista med regioner och URI:erna för deras motsvarande Azure Cosmos DB-slutpunkter för replikerna. Den aktuella skrivregionen indikeras i svaret. Klienten kan sedan välja lämplig slutpunkt för alla ytterligare REST API-förfrågningar enligt följande.

Exempelsvar

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Alla PUT-, POST- och DELETE-förfrågningar måste gå till den angivna skriv-URI:n
* Alla GET och andra skrivskyddade förfrågningar (t.ex. frågor) kan gå till vilken slutpunkt som helst som klienten väljer

Skrivförfrågningar till skrivskyddade regioner misslyckas med HTTP-felkoden 403 (Forbidden (Förbjudet)).

Om skrivregionen ändras efter klientens inledande identifieringsfas, så kommer efterföljande skrivningar till föregående skrivregion att misslyckas med HTTP-felkoden 403 (Forbidden (Förbjudet)). Klienten bör sedan få listan med regioner igen och därigenom få den uppdaterade skrivregionen.

Och med detta är den här självstudiekursen klar. Mer information om hur du kan hantera ditt globalt replikerade kontos konsekvens finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Mer information om hur global databasreplikering fungerar i Azure Cosmos DB finns i [Distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med SQL-API:erna

Du kan nu fortsätta till nästa självstudiekurs och lära dig hur du utvecklar lokalt med hjälp av den lokala Azure Cosmos DB-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med emulatorn](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

