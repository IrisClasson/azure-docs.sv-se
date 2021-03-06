---
title: Sök aktivitet i Azure Data Factory
description: Lär dig hur du använder lookup-aktivitet för att leta upp ett värde från en extern källa. Det går att referera till utdata ytterligare genom att utföra aktiviteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81418009"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Sök aktivitet i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Söknings aktiviteten kan hämta en data uppsättning från någon av de Azure Data Factory data källor som stöds. Använd det i följande scenario:
- Identifiera dynamiskt vilka objekt som ska användas i en efterföljande aktivitet, i stället för att hårdkoda objekt namnet. Några exempel på objekt är filer och tabeller.

Lookup-aktiviteten läser och returnerar innehållet i en konfigurations fil eller tabell. Det returnerar också resultatet av att köra en fråga eller lagrad procedur. Utdata från lookup-aktiviteten kan användas i en efterföljande kopierings-eller omvandlings aktivitet om det är ett singleton-värde. Utdata kan användas i en förgrunds aktivitet om det är en matris med attribut.

## <a name="supported-capabilities"></a>Funktioner som stöds

Följande data källor stöds för lookup-aktivitet. Det största antalet rader som kan returneras av lookup-aktiviteten är 5 000, upp till 2 MB. För närvarande är den längsta varaktigheten för uppslags aktivitet före timeout en timme.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Typ egenskaper

Name | Beskrivning | Typ | Obligatoriskt?
---- | ----------- | ---- | --------
data uppsättning | Tillhandahåller data uppsättnings referensen för sökningen. Hämta information från avsnittet **Egenskaper för data mängd** i varje motsvarande kopplings artikel. | Nyckel/värde-par | Ja
källa | Innehåller data uppsättnings bara käll egenskaper, samma som för kopierings aktivitets källan. Hämta information från avsnittet **Kopiera aktivitets egenskaper** i varje motsvarande kopplings artikel. | Nyckel/värde-par | Ja
firstRowOnly | Anger om bara den första raden eller alla rader ska returneras. | Boolesk | Nej. Standardvärdet är `true`.

> [!NOTE]
> 
> * Käll kolumner med **ByteArray** -typ stöds inte.
> * **Strukturen** stöds inte i data uppsättnings definitioner. För textformat-filer använder du rubrik raden för att ange kolumn namnet.
> * Om din uppslags källa är en JSON-fil `jsonPathDefinition` stöds inte inställningen för att OMFORMA JSON-objektet. Hela objekten kommer att hämtas.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Använd Sök aktivitets resultatet i en efterföljande aktivitet

Sök resultatet returneras i `output` avsnittet i aktivitets körnings resultatet.

* **När `firstRowOnly` är inställt på `true` (standard)** visas utdataformatet som i följande kod. Uppslags resultatet är under en fast `firstRow` nyckel. Använd mönstret i om du vill använda resultatet i efterföljande aktivitet `@{activity('MyLookupActivity').output.firstRow.TableName}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **När `firstRowOnly` är inställt på `false` **visas utdataformatet som i följande kod. Ett `count` fält indikerar hur många poster som returneras. Detaljerade värden visas under en fast `value` matris. I sådana fall följs söknings aktiviteten av en [förgrunds aktivitet](control-flow-for-each-activity.md). Du skickar `value` matrisen till fältet för förgrunds aktiviteter `items` med hjälp av mönstret `@activity('MyLookupActivity').output.value` . Använd följande syntax för att få åtkomst till element i `value` matrisen: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Ett exempel är `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Exempel på kopierings aktivitet
I det här exemplet kopierar kopierings aktiviteten data från en SQL-tabell i din Azure SQL Database-instans till Azure Blob Storage. Namnet på SQL-tabellen lagras i en JSON-fil i Blob Storage. Sök aktiviteten söker efter tabell namnet vid körning. JSON ändras dynamiskt med hjälp av den här metoden. Du behöver inte distribuera pipeliner eller data uppsättningar. 

I det här exemplet visas endast sökning efter den första raden. Om du vill söka efter alla rader och kedja resultaten med förgrunds aktiviteter, se exemplen i [Kopiera flera tabeller i bulk med hjälp av Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Den här pipelinen innehåller två aktiviteter: lookup och Copy. 

- Uppslags aktiviteten konfigureras att använda **LookupDataset**, som refererar till en plats i Azure Blob Storage. Sök aktiviteten läser namnet på SQL-tabellen från en JSON-fil på den här platsen. 
- Kopierings aktiviteten använder resultatet från söknings aktiviteten, vilket är namnet på SQL-tabellen. Egenskapen **TableName** i **SourceDataset** har kon figurer ATS för att använda utdata från lookup-aktiviteten. Kopierings aktiviteten kopierar data från SQL-tabellen till en plats i Azure Blob Storage. Platsen anges av egenskapen **SinkDataset** . 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Sök efter data uppsättning
**Sök** data uppsättningen är **sourcetable.js** i filen i mappen Azure Storage sökning som anges av **AzureStorageLinkedService** -typen. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Käll** data uppsättning för kopierings aktivitet
**Käll** data uppsättningen använder utdata från söknings aktiviteten, vilket är namnet på SQL-tabellen. Kopierings aktiviteten kopierar data från den här SQL-tabellen till en plats i Azure Blob Storage. Platsen anges av **Sink** -datamängden. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Mottagar** data uppsättning för kopierings aktivitet
Kopierings aktiviteten kopierar data från SQL-tabellen till den **filebylookup.csv** filen i **CSV** -mappen i Azure Storage. Filen anges av egenskapen **AzureStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Det här lagrings kontot innehåller JSON-filen med namnen på SQL-tabellerna. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Länkad Azure SQL Database-tjänst
Den här Azure SQL Database-instansen innehåller de data som ska kopieras till Blob Storage. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jspå

#### <a name="set-of-objects"></a>Uppsättning objekt

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Objekt mat ris

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

Här följer några begränsningar för söknings aktiviteten och föreslagna lösningar.

| Begränsning | Lösning |
|---|---|
| Sök aktiviteten har högst 5 000 rader och en maximal storlek på 2 MB. | Utforma en pipeline på två nivåer där den yttre pipelinen upprepas över en inre pipeline, som hämtar data som inte överskrider maximalt antal rader eller storlek. |
| | |

## <a name="next-steps"></a>Nästa steg
Se andra kontroll flödes aktiviteter som stöds av Data Factory: 

- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [ForEach-aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
