---
title: Flytta data från PostgreSQL med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från PostgreSQL-databas med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bd39b0aae5b76f37e2153f8e4c4502be994fa5b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462011"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Flytta data från PostgreSQL med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-postgresql-connector.md)
> * [Version 2 (aktuell version)](../connector-postgresql.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [PostgreSQL connector i V2](../connector-postgresql.md).


Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en lokal PostgreSQL-databas. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från ett datalager för lokal PostgreSQL till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data factory stöder för närvarande flyttar data från en PostgreSQL-databas till datalager, men inte för att flytta data från andra datalager till en PostgreSQL-databas.

## <a name="prerequisites"></a>Nödvändiga komponenter

Data Factory-tjänsten stöder anslutning till den lokala PostgreSQL-källor med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) du lär dig om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar gatewayen.

Gateway krävs även om PostgreSQL-databasen finns i en Azure IaaS-VM. Du kan installera gatewayen på samma IaaS VM som datalager eller på en annan virtuell dator, förutsatt att gatewayen kan ansluta till databasen.

> [!NOTE]
> Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning/gateway-relaterade problem.

## <a name="supported-versions-and-installation"></a>Versioner som stöds och installation
Data Management Gateway att ansluta till PostgreSQL-databasen, installera den [Ngpsql dataprovider för PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) med version mellan 2.0.12 och 3.1.9 på samma system som Data Management Gateway. PostgreSQL version 7.4 och senare stöds.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett datalager för den lokala PostgreSQL med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.
- Du kan också använda följande verktyg för att skapa en pipeline:
  - Azure Portal
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-mall
  - .NET-API
  - REST-API

    Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager för lokal PostgreSQL finns [JSON-exempel: Kopiera data från PostgreSQL till Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för ett datalager för PostgreSQL:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för PostgreSQL länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| type |Type-egenskapen måste anges till: **OnPremisesPostgreSql** |Ja |
| server |Namnet på PostgreSQL-servern. |Ja |
| database |Namnet på PostgreSQL-databasen. |Ja |
| schema |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänsligt. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden: Anonym, Basic och Windows. |Ja |
| username |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av.

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **RelationalTable** (som innehåller PostgreSQL datauppsättning) har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i PostgreSQL-databasinstansen som den länkade tjänsten refererar till. TableName är skiftlägeskänsligt. |Nej (om **fråga** av **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

När källan är av typen **RelationalSource** (som innehåller PostgreSQL), följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| query |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nej (om **tableName** av **datauppsättning** har angetts) |

> [!NOTE]
> Schema och tabellnamn är skiftlägeskänsliga. Sätter dem inom `""` (dubbla citattecken) i frågan.

**Exempel:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-exempel: Kopiera data från PostgreSQL till Azure Blob
Det här exemplet innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från PostgreSQL-databas till Azure Blob Storage. Dock datan kan kopieras till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Stegvisa instruktioner för att skapa data factory omfattas inte. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner.

Exemplet har följande data factory-entiteter:

1. En länkad tjänst av typen [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Den [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i PostgreSQL-databas till en blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg att konfigurera data management gateway. Anvisningarna finns i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

**PostgreSQL-länkad tjänst:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Azure Blob storage-länkade tjänst:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL datauppsättningen för indata:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i PostgreSQL och innehåller en kolumn med namnet ”timestamp” för time series-data.

Ange `"external": true` informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline med Kopieringsaktivitet:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **RelationalSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer vilka data från tabellen public.usstates i PostgreSQL-databasen.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mappning för PostgreSQL
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln Kopieringsaktivitet utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När data flyttas till PostgreSQL, används följande mappningar från PostgreSQL-typ till .NET-typen.

| Typ av PostgreSQL-databas | PostgresSQL alias | .NET framework-typ |
| --- | --- | --- |
| abstime | |DateTime |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bitars [(n)] | |Byte[], String |
| bit varierande [(n)] |varbit |Byte[], String |
| boolesk |bool |Boolean |
| Box | |Byte[], String |
| bytea | |Byte[], String |
| tecknet [(n)] |char [(n)] |String |
| tecknet varierande [(n)] |varchar [(n)] |String |
| CID | |String |
| cidr | |String |
| Cirkel | |Byte[], String |
| date | |DateTime |
| DateRange | |String |
| dubbel precision |FLOAT8 |Double |
| inet | |Byte[], String |
| intarry | |String |
| int4range | |String |
| int8range | |String |
| heltal |int, int4 |Int32 |
| intervallet [fält] [(p)] | |Tidsintervall |
| JSON | |String |
| jsonb | |Byte[] |
| linje | |Byte[], String |
| lseg | |Byte[], String |
| macaddr | |Byte[], String |
| money | |Decimal |
| numeriska [(p, s)] |decimal [(p, s)] |Decimal |
| numrange | |String |
| oid | |Int32 |
| path | |Byte[], String |
| pg_lsn | |Int64 |
| punkt | |Byte[], String |
| polygon | |Byte[], String |
| real |FLOAT4 |Single |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| serie |serial4 |Int32 |
| text | |String |

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
