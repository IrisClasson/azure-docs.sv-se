---
title: Databasmigreringsverktyg för Azure Cosmos DB
description: Lär dig att använda datamigreringsverktyg för öppen källkod i Azure Cosmos DB till att importera data till Azure Cosmos DB från olika källor, bland annat MongoDB, SQL Server, tabellagring, Amazon DynamoDB, CSV och JSON-filer. Konvertering av CSV till JSON.
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: dech
ms.openlocfilehash: 792dca41a052930bf2c853846cdd0c09661c5cd3
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954498"
---
# <a name="use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Migrera data till Azure Cosmos DB med hjälp av migreringsverktyget

Den här självstudien innehåller anvisningar för hur du använder datamigreringsverktyget i Azure Cosmos DB, som kan importera data från olika källor till Azure Cosmos DB-samlingar och tabeller. Du kan importera från JSON-filer, CSV-filer, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB och till och med Azure Cosmos DB SQL API-samlingar. Du migrerar dessa data till samlingar och tabeller för användning med Azure Cosmos DB. Datamigreringsverktyget kan också användas när du migrerar från en enda partitionssamling till en samling med flera partitioner för SQL API.

Vilken API ska du använda med Azure Cosmos DB?

* **[SQL API](documentdb-introduction.md)**  – Du kan använda något av källalternativen i datamigreringsverktyget när du importerar data.
* **[Tabell-API](table-introduction.md)**  – Du kan använda datamigreringsverktyget eller AzCopy när du importerar data. Se [Importera data för användning med Azure Cosmos DB Table-API](table-import.md) för mer information.
* **[Azure Cosmos DB-API för MongoDB](mongodb-introduction.md)**  – Datamigreringsverktyget har för närvarande inte stöd för Azure Cosmos DB API för MongoDB vare sig som en källa eller som ett mål. Om du vill migrera data inom eller utanför samlingar i Azure Cosmos DB hittar du instruktioner i [Hur du migrerar MongoDB-data till en Cosmos-databas med Azure Cosmos DB API för MongoDB](mongodb-migrate.md). Du kan fortfarande använda datamigreringsverktyget till att exportera data från MongoDB till Azure Cosmos DB SQL API-samlingar för användning med SQL API.
* **[Gremlin API](graph-introduction.md)** – Datamigreringsverktyget är ett importverktyg som saknar stöd för Gremlin API-konton för närvarande.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Installera datamigreringsverktyget
> * Importera data från olika datakällor
> * Exportera från Azure Cosmos DB till JSON

## <a id="Prerequisites"></a>Förhandskrav

Innan du följer anvisningarna i den här artikeln bör du se till att du utför följande steg:

* **Installera** [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) eller högre.

* **Öka dataflödet:** Hur lång tid datamigreringen tar beror på hur stort dataflöde du anger för en enskild samling eller en uppsättning samlingar. Du bör öka dataflödet för större datamigreringar. När du har slutfört migreringen kan du minska dataflödet för att sänka kostnaderna. Mer information om hur du ökar dataflödet i Azure-portalen finns i avsnitten om [prestandanivåer](performance-levels.md) och [prisnivåer](https://azure.microsoft.com/pricing/details/cosmos-db/) i Azure Cosmos DB.

* **Skapa Azure Cosmos DB-resurser:** Innan du börjar migrera data skapar du alla dina samlingar i förväg från Azure-portalen. För att migrera till ett Azure Cosmos DB-konto som har dataflöde på databasnivå anger du en partitionsnyckel när du skapar Azure Cosmos DB-samlingar.

## <a id="Overviewl"></a>Översikt

Datamigreringsverktyget är en lösning med öppen källkod som importerar data till Azure Cosmos DB från olika källor, inklusive:

* JSON-filer
* MongoDB
* SQL Server
* CSV-filer
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos DB-samlingar

Även om importverktyget innehåller ett grafiskt användargränssnitt (dtui.exe), kan det också köras från kommandoraden (dt.exe). Det finns faktiskt ett alternativ till att få utdata från ett associerat kommando när du har konfigurerat en import via användargränssnittet. Du kan transformera tabellkälldata, till exempel SQL Server- eller CSV-filer, för att skapa hierarkiska relationer (underdokument) vid import. Om du vill kan du läsa mer om källalternativ, exempelkommandon för att importera från varje källa, målalternativ och hur du ser de importerade resultaten.

## <a id="Install"></a>Installation

Källkoden för migreringsverktyget finns i GitHub på [den här lagringsplatsen](https://github.com/azure/azure-documentdb-datamigrationtool). Du kan ladda ned och kompilera lösningen lokalt, eller [ladda ned en förkompilerad binär kod](https://aka.ms/csdmtool) och sedan köra något av följande:

* **Dtui.exe**: Grafisk gränssnittsversion av verktyget
* **Dt.exe**: Kommandoradsversion av verktyget

## <a name="select-data-source"></a>Välj datakälla

När du har installerat verktyget är det dags att importera dina data. Vilken typ av data vill du importera?

* [JSON-filer](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-exportfiler](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-filer](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos DB-samlingar](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB-massimport](#SQLBulkTarget)
* [Azure Cosmos DB:s import av sekventiella poster](#SQLSeqTarget)

## <a id="JSON"></a>Importera JSON-filer

Med importverktygets alternativ för JSON-filkällor kan du importera ett eller flera dokument med JSON-filer, eller flera JSON-filer som var och en har en matris med JSON-dokument. När du lägger till mappar som har JSON-filer som ska importeras har du möjlighet att rekursivt söka efter filerna i undermappar.

![Skärmbild av alternativ för JSON-filkällor – Verktyg för databasmigrering](./media/import-data/jsonsource.png)

Anslutningssträngen har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* Den `<CosmosDB Endpoint>` är URI-slutpunkten. Du kan få det här värdet från Azure-portalen. Navigera till ditt Azure Cosmos-konto. Öppna den **översikt** fönstret och kopiera den **URI** värde.
* Den `<AccountKey>` är ”Password” eller **PRIMÄRNYCKEL**. Du kan få det här värdet från Azure-portalen. Navigera till ditt Azure Cosmos-konto. Öppna den **anslutningssträngar** eller **nycklar** fönstret och kopiera ”Password” eller **PRIMÄRNYCKEL** värde.
* Den `<CosmosDB Database>` är namnet på CosmosDB-databasen.

Exempel: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Använd kommandot Kontrollera så att Cosmos DB-kontot som angetts i strängfält anslutning är tillgänglig.

Här följer några kommandoradsexempel för import av JSON-filer:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a id="MongoDB"></a>Importera från MongoDB

> [!IMPORTANT]
> Om du importerar till ett Cosmos-konto som har konfigurerats med Azure Cosmos DB API för MongoDB, följer du dessa [instruktioner](mongodb-migrate.md).

Med importverktygets alternativ för MongoDB-källor kan du importera från en enskild MongoDB-samling, filtrera dokument med hjälp av en fråga (valfritt) och ändra dokumentstrukturen med hjälp av en projektion.  

![Skärmbild av MongoDB-källalternativ](./media/import-data/mongodbsource.png)

Anslutningssträngen är i standardformatet för MongoDB:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna MongoDB-instansen är tillgänglig i anslutningssträngens fält.

Ange namnet på den samling som data ska importeras från. Du kan om du vill ange en fil för en fråga, till exempel `{pop: {$gt:5000}}`, eller en projektion såsom `{loc:0}`, för att både filtrera och utforma de data som du importerar.

Här följer några kommandoradsexempel för import från MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a id="MongoDBExport"></a>Importera MongoDB-exportfiler

> [!IMPORTANT]
> Om du importerar till ett Azure Cosmos DB-konto med stöd för MongoDB följer du dessa [anvisningar](mongodb-migrate.md).

Med importverktygets alternativ för MongoDB-export av JSON-filkällor, kan du importera en eller flera JSON-filer som skapats med verktyget mongoexport.  

![Skärmbild av MongoDB-exportkällans alternativ](./media/import-data/mongodbexportsource.png)

När du lägger till mappar som har MongoDB-exportens JSON-filer som ska importeras har du möjlighet att rekursivt söka efter filerna i undermappar.

Här följer ett kommandoradsexempel när import görs från MongoDB-exportens JSON-filer:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a id="SQL"></a>Importera från SQLServer

Med importverktygets alternativ för SQL-källor kan du importera från en enskild SQL Server-databas och du kan också filtrera de poster som ska importeras med hjälp av en fråga. Du kan dessutom ändra dokumentets struktur genom att ange en kapslad avgränsare (mer information om detta kommer längre fram).  

![Skärmbild av SQL-källans alternativ – Verktyg för databasmigrering](./media/import-data/sqlexportsource.png)

Formatet för anslutningssträngen är standardformatet för SQL-anslutningssträngar.

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna SQL Server-instansen är tillgänglig i anslutningssträngens fält.

Egenskapen för kapslade avgränsare används för att skapa hierarkiska relationer (underdokument) under importen. Fundera över följande SQL-fråga:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Som returnerar följande (partiella) värden:

![Skärmbild av SQL-frågeresultat](./media/import-data/sqlqueryresults.png)

Observera alias som t.ex. Address.AddressType och Address.Location.StateProvinceName. När du anger den kapslade avgränsaren '.', skapar importverktyget underdokumenten Address och Address.Location under importen. Här är ett exempel på ett resulterande dokument i Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Här följer några kommandoradsexempel för att importera från SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a id="CSV"></a>Importera CSV-filer och konvertera CSV till JSON

Med importverktygets alternativ för CSV-filkällor kan du importera en eller flera CSV-filer. När du lägger till mappar som har CSV-filer som ska importeras har du möjlighet att rekursivt söka efter filerna i undermappar.

![Skärmbild av CSV-källans alternativ – CSV till JSON](media/import-data/csvsource.png)

På samma sätt som med SQL-källan kan egenskapen för kapslade avgränsare användas till att skapa hierarkiska relationer (underdokument) under importen. Fundera över följande CSV-rubrikrad och datarader:

![Skärmbild av CSV-exemplets poster – CSV till JSON](./media/import-data/csvsample.png)

Observera alias som t.ex. DomainInfo.Domain_Name och RedirectInfo.Redirecting. När du anger den kapslade avgränsaren '.', skapar importverktyget underdokumenten DomainInfo och RedirectInfo under importen. Här är ett exempel på ett resulterande dokument i Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Importverktyget försöker härleda typinformationen för värden utan citattecken i CSV-filer (värden inom citattecken behandlas alltid som strängar).  Typer identifieras i följande ordning: nummer, datetime, booleskt värde.  

Det finns två saker att lägga märke till när det gäller CSV-import:

1. Som standard tas värden utan citattecken alltid bort för flikar och blanksteg, medan värden inom citattecken sparas som de är. Den här funktionen kan åsidosättas med kryssrutan Trim quoted values (Rensa värden inom citattecken) eller kommandoradsalternativet /s.TrimQuoted.
2. Som standard behandlas null utan citattecken som ett null-värde. Den här funktionen kan åsidosättas (dvs. behandla null utan citattecken som en null-sträng) med kryssrutan Treat unquoted NULL as string (Behandla NULL utan citattecken som en sträng) eller kommandoradsalternativet /s.NoUnquotedNulls.

Här följer ett kommandoradsexempel för CSV-import:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a id="AzureTableSource"></a>Importera från Azure Table Storage

Med importverktygets alternativ för Azure Table Storage-källor kan du importera från en enskild Azure Table Storage-tabell. Du kan också du filtrera tabellentiteterna som ska importeras.

Du kan mata ut data som importerades från Azure Table Storage till Azure Cosmos DB-tabeller och -entiteter för användning med tabell-API. Importerade data kan även vara utdata till samlingar och dokument för användning med SQL API. Dock är tabell-API endast tillgängligt som mål i kommandoradsverktyget. Du kan inte exportera till tabell-API med hjälp av användargränssnittet för datamigreringsverktyget. Se [Importera data för användning med Azure Cosmos DB Table-API](table-import.md) för mer information.

![Skärmbild av Azure Table Storage-källans alternativ](./media/import-data/azuretablesource.png)

Formatet för anslutningssträngen till Azure Table Storage är:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Table Storage-instansen är tillgänglig i anslutningssträngens fält.

Ange namnet på Azure-tabellen som du ska importera från. Du kan också ange ett [filter](../vs-azure-tools-table-designer-construct-filter-strings.md).

Importverktygets alternativ för Azure Table Storage-källor innehåller dessutom följande extra alternativ:

1. Inkludera interna fält
   1. Alla – Inkludera alla interna fält (PartitionKey, RowKey och Timestamp)
   2. Ingen – Undanta alla interna fält
   3. RowKey – Inkludera bara fältet RowKey
2. Välja kolumner
   1. Azure Table Storage-filter stöder inte projektioner. Om du endast vill importera specifika entitetsegenskaper från Azure-tabellen, lägger du till dem i listan Välj kolumner. Alla andra entitetsegenskaper ignoreras.

Här är ett kommandoradsexempel på hur du importerar från Azure Table Storage:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a id="DynamoDBSource"></a>Importera från Amazon DynamoDB

Med alternativet för Amazon DynamoDB-källimport kan du importera från en enskild Amazon DynamoDB-tabell. Det kan även filtrera de entiteter som ska importeras om du väljer det. Det finns flera olika mallar att välja bland, för att konfigurationen av importen ska vara så enkel som möjligt.

![Skärmbild av alternativ på Amazon DynamoDB-källor – Verktyg för databasmigrering](./media/import-data/dynamodbsource1.png)

![Skärmbild av alternativ på Amazon DynamoDB-källor – Verktyg för databasmigrering](./media/import-data/dynamodbsource2.png)

Formatet på Amazon DynamoDB-anslutningssträngen är:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Amazon DynamoDB-instansen är tillgänglig i anslutningssträngens fält.

Här är ett kommandoradsexempel på hur du importerar från Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a id="BlobImport"></a>Importera från Azure Blob Storage

Med importverktygets alternativ för källor från JSON-filen, MongoDB-exportfilen och CSV-filen, kan du importera en eller flera filer från Azure Blob Storage. När du har angett en URL för blobcontainern och en kontonyckel, använder du ett reguljärt uttryck till att välja vilka filer du vill importera.

![Skärmbild över alternativ för blobfilens källa](./media/import-data/blobsource.png)

Här är ett kommandoradsexempel på hur du kan importera JSON-filer från Azure Blob Storage:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a id="SQLSource"></a>Importera från en SQL API-samling

Med importverktygets alternativ för Azure Cosmos DB-källor kan du importera data från en eller flera Azure Cosmos DB-samlingar och du kan också filtrera dokument med hjälp av en fråga.  

![Skärmbild över alternativ för Azure Cosmos DB-källor](./media/import-data/documentdbsource.png)

Anslutningssträngen för Azure Cosmos DB har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Du kan hämta Azure Cosmos DB-kontoanslutningssträngen från sidan Nycklar i Azure-portalen, enligt beskrivningen i [Så här hanterar du ett Azure Cosmos DB-konto](manage-account.md). Dock måste namnet på databasen läggas till i anslutningssträngen i följande format:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Cosmos DB-instansen är tillgänglig i anslutningssträngens fält.

Ange namnet på samlingen som du ska importera data från när du importerar från en enda Azure Cosmos DB-samling. När du ska importera från fler än en Azure Cosmos DB-samling anger du ett reguljärt uttryck som matchar ett eller flera samlingsnamn (till exempel collection01 | collection02 | collection03). Du kan om du vill ange en fil för en fråga för att både filtrera och utforma de data som du importerar.

> [!NOTE]
> Eftersom samlingsfältet accepterar reguljära uttryck kommer dessa tecken att hoppas över, om du importerar från en enda samling vars namn innehåller tecken i reguljära uttryck.

Importverktygets alternativ för Azure Cosmos DB-källor innehåller följande avancerade alternativ:

1. Inkludera interna fält: Anger huruvida du vill inkludera Azure Cosmos DB-dokumentegenskaper för system i exporten (till exempel _rid, _ts).
2. Antal återförsök vid fel: Anger hur många gånger systemet försöker återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
3. Återförsöksintervall: Anger hur lång väntetiden är vid försök att återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
4. Anslutningsläge: Anger det anslutningsläge som ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutningslägena är snabbare, medan gatewayläget är mer brandväggsanpassat eftersom det endast använder port 443.

![Skärmbild över avancerade alternativ för Azure Cosmos DB-källor](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Importverktyget använder som standard anslutningsläget DirectTcp. Om det uppstår brandväggsproblem kan du växla till anslutningsläget Gateway, eftersom det endast kräver port 443.

Här följer några kommandoradsexempel på hur du importerar från Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos DB collection to a single Azure Cosmos DB collection
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos DB collection to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500
```

> [!TIP]
> Dataimportverktyget i Azure Cosmos DB har även stöd för import av data från [Azure Cosmos DB-emulatorn](local-emulator.md). När du importerar data från en lokal emulator anger du slutpunkten till `https://localhost:<port>`.

## <a id="HBaseSource"></a>Importera från HBase

Med importverktygets alternativ för HBase-källor kan du importera data från en HBase-tabell och du kan också filtrera datan. Det finns flera olika mallar att välja bland, för att konfigurationen av importen ska vara så enkel som möjligt.

![Skärmbild över alternativ för HBase-källor](./media/import-data/hbasesource1.png)

![Skärmbild över alternativ för HBase-källor](./media/import-data/hbasesource2.png)

Anslutningssträngen för HBase Stargate har följande format:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna HBase-instansen är tillgänglig i anslutningssträngens fält.

Här är ett kommandoradsexempel på hur du importerar från HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a id="SQLBulkTarget"></a>Importera till SQL API (massimport)

Med massimportverktyget för Azure Cosmos DB kan du importera från något av källalternativen, med hjälp av en Azure Cosmos DB-lagrad procedur som effektiviserar processen. Verktyget stöder import till en Azure Cosmos DB-samling med en enda partition. Det stöder även horisontellt partitionerad import där data är partitionerade över fler än en Azure Cosmos DB-samling med en enda partition. Mer information om partitionering av data finns i [Partitionering och skalning i Azure Cosmos DB](partition-data.md). Verktyget skapar, kör och tar sedan bort den lagrade proceduren från målsamlingarna.  

![Skärmbild över alternativ för Azure Cosmos DB-massimport](./media/import-data/documentdbbulk.png)

Anslutningssträngen för Azure Cosmos DB har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Anslutningssträngen för Azure Cosmos DB-kontot kan hämtas från sidan Nycklar i Azure Portal, enligt beskrivningen i [Så här hanterar du ett konto i Azure Cosmos DB](manage-account.md), men namnet på databasen måste läggas till i anslutningssträngen i följande format:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Cosmos DB-instansen är tillgänglig i anslutningssträngens fält.

Om du ska importera till en enskild samling anger du namnet på samlingen som du ska importera data från. Klicka sedan på knappen Lägg till. Om du vill importera till fler än en samling anger du antingen varje samlingsnamn individuellt, eller så använder du följande syntax för att ange fler än en samling: *collection_prefix*[start index - end index]. Tänk på följande riktlinjer när du anger fler än en samling med hjälp av ovannämnda syntax:

1. Det är bara namnmönster i heltalsintervall som stöds. Om du till exempel anger collection[0-3] skapas följande samlingar: collection0, collection1, collection2 och collection3.
2. Du kan använda en förkortad syntax: collection[3] skapar samma uppsättning samlingar som nämns i steg 1.
3. Mer än en ersättning kan anges. Till exempel genererar collection[0-1] [0-9] 20 samlingsnamn med inledande nollor (collection01, ..02, ..03).

När samlingsnamnen har angetts väljer du önskad dataflöde för samlingarna (från 400 till 10 000 RU:er). Välj ett högre dataflöde för att få bästa prestandan för importen. Mer information om prestandanivåer finns i avsnittet [Prestandanivåer i Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Inställningen av prestandans dataflöde gäller bara när samlingar skapas. Om den angivna samlingen redan finns ändras inte dess dataflöde.

När du importerar till fler än en samling stöder importverktyget hash-baserad horisontell partitionering. I det här scenariot anger du den dokumentegenskap som du vill använda som partitionsnyckel. (Om partitionsnyckeln lämnas tom partitioneras dokument horisontellt över målsamlingarna.)

Du kan om du vill ange vilket fält i importkällan som ska användas som Azure ID-egenskap för Cosmos DB-dokument under importen. Om dokument inte har den här egenskapen genererar importverktyget ett GUID som ID-egenskapsvärde.

Det finns ett antal avancerade alternativ under importen. Även om verktyget innehåller en standardlagrad procedur för massimport (BulkInsert.js), kan du ange en egen lagrad importprocedur:

 ![Skärmbild över sproc-alternativ för Azure Cosmos DB-massinfogning](./media/import-data/bulkinsertsp.png)

När du importerar datumtyper (till exempel från SQL Server eller MongoDB) kan du dessutom välja mellan tre importalternativ:

 ![Skärmbild över importalternativ för datum och tid i Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Sträng: Spara som ett strängvärde
* Epok: Spara som ett epoknummervärde
* Båda: Spara både strängvärden och epoknummervärden. Det här alternativet skapar ett underdokument, till exempel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Massimportverktyget för Azure Cosmos DB innehåller följande avancerade extra alternativ:

1. Batchstorlek: Verktyget har som standard en batchstorlek på 50.  Om de dokument som ska importeras är stora kan du minska batchstorleken. Om de dokument som ska importeras är små kan du öka batchstorleken.
2. Maxstorlek för skript (byte): Verktyget har som standard en maxstorlek för skript på 512 kB.
3. Inaktivera automatisk ID-generering: Om alla dokument som ska importeras har ett ID-fält kan det här alternativet öka prestanda. Dokument som saknar ett fält för unikt ID importeras inte.
4. Uppdatera befintliga dokument: Verktyget ersätter som standard inte befintliga dokument med ID-konflikter. Med det här alternativet kan du skriva över befintliga dokument med matchande ID:n. Funktionen är användbar vid schemalagda datamigreringar som uppdaterar befintliga dokument.
5. Antal återförsök vid fel: Anger hur ofta systemet försöker återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
6. Återförsöksintervall: Anger hur lång väntetiden är vid försök att återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
7. Anslutningsläge: Anger det anslutningsläge som ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutningslägena är snabbare, medan gatewayläget är mer brandväggsanpassat eftersom det endast använder port 443.

![Skärmbild över avancerade alternativ för Azure Cosmos DB-massimport](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Importverktyget använder som standard anslutningsläget DirectTcp. Om det uppstår brandväggsproblem kan du växla till anslutningsläget Gateway, eftersom det endast kräver port 443.

## <a id="SQLSeqTarget"></a>Importera till SQL API (import av sekventiella poster)

Med importverktyget för sekventiella poster i Azure Cosmos DB kan du importera från ett tillgängligt källalternativ post för post. Du kan välja det här alternativet om du importerar till en befintlig samling som har uppnått sin kvot av lagrade procedurer. Verktyget stöder import till en enda Azure Cosmos DB-samling (både med en partition och flera partitioner). Det stöder även horisontellt partitionerad import där data är partitionerade över fler än en Azure Cosmos DB-samling med en enda partition eller flera partitioner. Mer information om partitionering av data finns i [Partitionering och skalning i Azure Cosmos DB](partition-data.md).

![Skärmbild över importalternativ för sekventiella poster i Azure Cosmos DB](./media/import-data/documentdbsequential.png)

Anslutningssträngen för Azure Cosmos DB har följande format:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Du kan hämta anslutningssträngen för Azure Cosmos DB-konto från sidan Nycklar i Azure-portalen, enligt beskrivningen i [Så här hanterar du ett Azure Cosmos DB-konto](manage-account.md). Dock måste namnet på databasen läggas till i anslutningssträngen i följande format:

`Database=<Azure Cosmos DB Database>;`

> [!NOTE]
> Använd kommandot Kontrollera för att se att den angivna Azure Cosmos DB-instansen är tillgänglig i anslutningssträngens fält.

Om du ska importera till en enskild samling anger du namnet på den samling som data ska importeras till och klickar sedan på knappen Lägg till. Ange varje samlingsnamn separat om du vill importera till mer än en samling. Du kan även använda följande syntax för att ange mer än en samling: *collection_prefix*[start index - end index]. Tänk på följande riktlinjer när du anger fler än en samling via ovannämnda syntax:

1. Det är bara namnmönster i heltalsintervall som stöds. Om du till exempel anger collection[0-3] skapas följande samlingar: collection0, collection1, collection2 och collection3.
2. Du kan använda en förkortad syntax: collection[3] skapar samma uppsättning samlingar som nämns i steg 1.
3. Mer än en ersättning kan anges. Till exempel skapar samling [0-1] [0-9] 20 samlingsnamn med inledande nollor (collection01, ..02, ..03).

När samlingsnamnen har angetts väljer du önskat dataflöde för samlingarna (från 400 till 250 000 RU:er). Välj ett högre dataflöde för att få bästa prestandan för importen. Mer information om prestandanivåer finns i avsnittet [Prestandanivåer i Azure Cosmos DB](performance-levels.md). Om du importerar till samlingar med dataflöde > 10 000 RU:er måste du ha en partitionsnyckel. Om du vill ha fler än 250 000 RU:er, måste du skicka en begäran i portalen om att öka ditt konto.

> [!NOTE]
> Inställningen av dataflödet gäller bara när samlingar eller databaser skapas. Om den angivna samlingen redan finns ändras inte dess dataflöde.

Vid import till fler än en samling stöder importverktyget hash-baserad horisontell partitionering. I det här scenariot anger du den dokumentegenskap som du vill använda som partitionsnyckel. (Om partitionsnyckeln lämnas tom partitioneras dokument horisontellt över målsamlingarna.)

Du kan om du vill ange vilket fält i importkällan som ska användas som Azure ID-egenskap för Cosmos DB-dokument under importen. (Om dokument inte har den här egenskapen genererar importverktyget ett GUID som ID-egenskapsvärde.)

Det finns ett antal avancerade alternativ under importen. När du importerar datumtyper (till exempel från SQL Server eller MongoDB) kan du välja mellan tre importalternativ:

 ![Skärmbild över importalternativ för datum och tid i Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Sträng: Spara som ett strängvärde
* Epok: Spara som ett epoknummervärde
* Båda: Spara både strängvärden och epoknummervärden. Det här alternativet skapar ett underdokument, till exempel: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB – Importverktyget för sekventiella poster innehåller följande avancerade alternativ:

1. Antal parallella begäranden: Verktyget kan som standard hantera två parallella begäranden. Om de dokument som ska importeras är små, kan du öka antalet parallella begäranden. Om antalet ökas för mycket kan en nätverksbegränsning uppstå vid importen.
2. Inaktivera automatisk ID-generering: Om alla dokument som ska importeras har ett ID-fält kan det här alternativet öka prestanda. Dokument som saknar ett fält för unikt ID importeras inte.
3. Uppdatera befintliga dokument: Verktyget ersätter som standard inte befintliga dokument med ID-konflikter. Med det här alternativet kan du skriva över befintliga dokument med matchande ID:n. Funktionen är användbar vid schemalagda datamigreringar som uppdaterar befintliga dokument.
4. Antal återförsök vid fel: Anger hur ofta systemet försöker återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
5. Återförsöksintervall: Anger hur lång väntetiden är vid försök att återupprätta anslutningen till Azure Cosmos DB vid tillfälliga fel (till exempel avbrott i nätverksanslutningen).
6. Anslutningsläge: Anger det anslutningsläge som ska användas med Azure Cosmos DB. Tillgängliga alternativ är DirectTcp, DirectHttps och Gateway. Direktanslutningslägena är snabbare, medan gatewayläget är mer brandväggsanpassat eftersom det endast använder port 443.

![Skärmbild över avancerade alternativ för import av sekventiella poster i Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Importverktyget använder som standard anslutningsläget DirectTcp. Om det uppstår brandväggsproblem kan du växla till anslutningsläget Gateway, eftersom det endast kräver port 443.

## <a id="IndexingPolicy"></a>Ange en indexeringsprincip

Om du tillåter att migreringsverktyget skapar Azure Cosmos DB SQL API-samlingar under importen, kan du ange indexeringsprincipen för samlingarna. Gå till Indexeringsprincip i avsnittet med avancerade alternativ i alternativen för Azure Cosmos DB-massimport och sekventiella poster i Azure Cosmos DB.

![Skärmbild över avancerade alternativ i Azure Cosmos DB:s indexeringsprincip](./media/import-data/indexingpolicy1.png)

I de avancerade alternativen för indexeringsprinciper kan du välja en indexeringsprincipfil, manuellt ange en indexeringsprincip, eller välja bland en uppsättning standardmallar (genom att högerklicka i textrutan för indexeringsprinciper).

Principmallarna i verktyget är:

* Standard. Den här principen är bäst när du utför likhetsfrågor mot strängar. Den fungerar även om du använder ORDER BY, intervall och likhetsfrågor för tal. Den här principen har lägre omkostnad för indexlagring än Intervall.
* Intervall. Den här principen passar bäst när du använder ORDER BY, intervall och likhetsfrågor för både tal och strängar. Principen har högre omkostnad för indexlagring än Standard och Hash.

![Skärmbild över avancerade alternativ i Azure Cosmos DB:s indexeringsprincip](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Om du inte anger någon indexeringsprincip används standardprincipen. Mer information om indexeringsprinciper finns i [Azure Cosmos DB-indexeringsprinciper](index-policy.md).

## <a name="export-to-json-file"></a>Exportera till JSON-fil

Med exportverktyget Azure Cosmos DB JSON kan du exportera alla tillgängliga källalternativ till en JSON-fil som har en matris av JSON-dokument. Verktyget hanterar exporten åt dig. Alternativt kan du visa resulterande migreringskommando och köra kommandot själv. Den resulterande JSON-filen kan vara lagrad lokalt eller i Azure Blob Storage.

![Skärmbild över exportalternativ för lokal fil i Azure Cosmos DB JSON](./media/import-data/jsontarget.png)

![Skärmbild över exportalternativ för Azure Cosmos DB Azure Blob Storage](./media/import-data/jsontarget2.png)

Du kan välja att förenkla resulterande JSON. Den här åtgärden ökar storleken på det resulterande dokumentet samtidigt som innehållet blir mer läsbart.

* Standard JSON-export

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Förenklad JSON-export

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Här är ett kommandoradsexempel på hur du kan exportera JSON-filen till Azure Blob Storage:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Avancerad konfiguration

På skärmen Avancerad konfiguration anger du platsen för loggfilen där du vill att eventuella fel skrivs. Följande regler gäller för den här sidan:

1. Om ett filnamn inte anges returneras alla fel till sidan Resultat.
2. Om ett filnamn anges utan en katalog, skapas filen (eller skrivs över) i den aktuella katalogen i miljön.
3. Om du väljer en befintlig fil skrivs den över. Det finns inte något alternativ för att lägga till filen.
4. Välj sedan om du vill logga alla, kritiska eller inga felmeddelanden. Bestäm slutligen hur ofta den på överföringsmeddelandet på skärmen ska uppdateras med förloppet.

   ![Skärmbild av skärmen för avancerad konfiguration](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Bekräfta importinställningarna och visa kommandoraden

1. När du har angett källinformation, målinformation och avancerad konfiguration granskar du migreringsöversikten och visar eller kopierar det resulterande migreringskommandot om du vill. (Att kopiera kommandot är användbart för att automatisera importåtgärder.)

    ![Skärmbild av översiktsskärm](./media/import-data/summary.png)

    ![Skärmbild av översiktsskärm](./media/import-data/summarycommand.png)

2. När du är nöjd med dina käll- och målalternativ klickar du på **Importera**. Förfluten tid, antal överförda och felinformation (om du inte angav ett filnamn i Avancerad konfiguration) uppdateras medan importen pågår. När installationen är klar kan du exportera resultaten (till exempel för att åtgärda eventuella importfel).

    ![Skärmbild över exportalternativ i Azure Cosmos DB JSON](./media/import-data/viewresults.png)

3. Du kan även starta en ny import genom att antingen återställa alla värden eller behålla de befintliga inställningarna. (Till exempel kan du behålla information om anslutningssträng, val av källa och mål och mer.)

    ![Skärmbild över exportalternativ i Azure Cosmos DB JSON](./media/import-data/newimport.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Installerat datamigreringsverktyget
> * Importerat data från olika datakällor
> * Exporterat från Azure Cosmos DB till JSON

Nu kan du gå vidare till nästa självstudie och lära dig att fråga efter data med hjälp av Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Hur frågar jag efter data?](../cosmos-db/tutorial-query-sql-api.md)
