---
title: Fil format som stöds i Azure Data Factory
description: Det här avsnittet beskriver filformat och komprimering koder som stöds av filbaserade anslutningar i Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 4a81cc9887610036007b92e43b8bd44f0a8b7740
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075541"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Filformat som stöds och komprimering codec-enheter i Azure Data Factory

*Den här artikeln gäller följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

>[!NOTE]
>Data Factory introducerade ny formaterad data uppsättnings modell, se motsvarande format artikel som är länkad ovan med information. Följande konfigurationer på filbaserad data lagrings data uppsättning som nämns i den här artikeln stöds fortfarande som-är för bakåtkompatibilitet compabitility. Du rekommenderas att använda den nya modellen som går framåt. 

## <a name="text-format"></a>Textformat

>[!NOTE]
>Data Factory introducerade en ny avgränsad text formats data uppsättning, se [avgränsad text format](format-delimited-text.md) artikel med information. Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill läsa från en textfil eller skriva till en textfil, anger den `type` -egenskapen i den `format` avsnittet på datauppsättningen till **TextFormat**. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [TextFormat-exempel](#textformat-example).

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| columnDelimiter |Det tecken som används för att avgränsa kolumner i en fil. Du kan överväga för att använda ett sällsynt icke utskrivbart tecken som inte kan finnas i dina data. Ange till exempel ”\u0001” som representerar Start av rubriken (SOH). |Endast ett tecken är tillåtet. **Standardvärdet** är **kommatecken (,)** . <br/><br/>Om du vill använda ett Unicode-tecken, som avser [Unicode-tecken](https://en.wikipedia.org/wiki/List_of_Unicode_characters) att hämta motsvarande kod för den. |Nej |
| rowDelimiter |Det tecken som används för att avgränsa rader i en fil. |Endast ett tecken är tillåtet. **Standardvärdet** är något av följande värden vid läsning: **["\r\n", "\r", "\n"]** och **"\r\n"** vid skrivning. |Nej |
| escapeChar |Det specialtecken som används för att undanta en kolumnavgränsare i innehållet i indatafilen. <br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Exempel: Om du använder kommatecken (,) som kolumnavgränsare, men vill använda ett kommatecken i texten (till exempel: "Hello, world") kan du definiera "$" som escape-tecken och använda strängen "$Hello, world" i källan. |Nej |
| quoteChar |Det tecken som används för att referera till ett strängvärde. Kolumn- och radavgränsarna innanför citattecknen behandlas som en del av strängvärdet. Den här egenskapen gäller både in- och utdatauppsättningar.<br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Om du till exempel använder kommatecken (,) som kolumnavgränsare, men vill använda ett kommatecken i texten (till exempel <Hello, world>), kan du definiera " (dubbla citattecken) som citattecknet och använda strängen "Hello, world" i källan. |Nej |
| nullValue |Ett eller flera tecken som används för att representera ett null-värde. |Ett eller flera tecken. **Standardvärdena** är **"\N" och "NULL"** vid läsning och **"\N"** vid skrivning. |Nej |
| encodingName |Ange kodningsnamnet. |Ett giltigt kodningsnamn. Se [Egenskapen Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exempel: windows-1250 or shift_jis. **Standardvärdet** är **UTF-8**. |Nej |
| firstRowAsHeader |Anger om den första raden ska behandlas som en rubrik. För en indatauppsättning läser Data Factory den första raden som en rubrik. För en utdatauppsättning skriver Data Factory den första raden som en rubrik. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (standard)</b> |Nej |
| skipLineCount |Anger hur många **icke-tomma** rader att hoppa över vid läsning av data från indatafiler. Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Nej |
| treatEmptyAsNull |Anger om du vill hantera null-strängar eller tomma strängar som ett null-värde vid läsning av data från en indatafil. |**True (standard)**<br/>False |Nej |

### <a name="textformat-example"></a>TextFormat-exempel

I följande JSON-definition för en datauppsättning anges några av valfria egenskaper.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Om du vill använda ett `escapeChar` i stället för `quoteChar` ersätter du raden med `quoteChar` med följande escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Användningsscenarier för firstRowAsHeader och skipLineCount

* Du kopierar från en källa som inte är filbaserad till en textfil och vill lägga till en rubrikrad som innehåller schemametadata (till exempel: SQL-schema). Ange `firstRowAsHeader` som true i utdatauppsättningen för det här scenariot.
* Du kopierar från en textfil som innehåller en rubrikrad till en mottagare som inte är filbaserad och vill ignorera raden. Ange `firstRowAsHeader` som true i indatauppsättningen.
* Du kopierar från en textfil och vill hoppa över några rader i början som antingen inte innehåller några data eller som innehåller rubrikinformation. Ange `skipLineCount` för att ange antalet rader som ska hoppas över. Om resten av filen innehåller en rubrikrad kan du också ange `firstRowAsHeader`. Om både `skipLineCount` och `firstRowAsHeader` anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen

## <a name="json-format"></a>JSON-format

>[!NOTE]
>Data Factory introducerade en ny data uppsättning för JSON-format, se [JSON](format-json.md) -artikel med information. Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Att **en JSON-fil som för import/export-är till/från Azure Cosmos DB**, finns i avsnittet för Import/export JSON-dokument i [flytta data till och från Azure Cosmos DB](connector-azure-cosmos-db.md) artikeln.

Om du vill parsa JSON-filerna eller skriva data i JSON-format anger den `type` -egenskapen i den `format` avsnitt **JsonFormat**. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example).

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| filePattern |Ange mönstret för de data som lagras i varje JSON-fil. Tillåtna värden är: **setOfObjects** och **arrayOfObjects**. **Standardvärdet** är **setOfObjects**. Detaljerad information om dessa mönster finns i avsnittet om [JSON-filmönster](#json-file-patterns). |Nej |
| jsonNodeReference | Om du vill iterera och extrahera data från objekten i ett matrisfält med samma mönster anger du JSON-sökvägen för matrisen. Den här egenskapen stöds endast när du kopierar data **från** JSON-filer. | Nej |
| jsonPathDefinition | Ange JSON-sökvägsuttrycket för varje kolumnmappning med ett anpassat kolumnnamn (inled med liten bokstav). Den här egenskapen stöds endast när du kopierar data **från** JSON-filer och du kan extrahera data från objekt eller matris. <br/><br/> För fält under rotobjektet börjar du med $; för fält inuti matrisen som väljs av egenskapen `jsonNodeReference` börjar du från matriselementet. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example). | Nej |
| encodingName |Ange kodningsnamnet. En lista över giltiga kodningsnamn finns i avsnittet om egenskapen [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exempel: windows-1250 or shift_jis. **Standardvärdet** är **UTF-8**. |Nej |
| nestingSeparator |Tecken som används för att avgränsa kapslingsnivåer. Standardvärdet är ”.” (punkt). |Nej |

>[!NOTE]
>Vid kors användning av data i matrisen i flera rader (fall 1 – > exempel 2 i JsonFormat- [exempel](#jsonformat-example)) kan du bara välja att expandera en enskild matris med hjälp av egenskapen `jsonNodeReference`.

### <a name="json-file-patterns"></a>JSON-filmönster

Kopieringsaktiviteten kan parsa JSON-filmönster följande mönster:

- **Typ I: setOfObjects**

    Varje fil som innehåller ett enskilt objekt eller flera radavgränsade/sammanfogade objekt. När det här alternativet väljs i en utdatauppsättning genererar kopieringsaktiviteten en enskild JSON-fil med ett objekt per rad (radavgränsade).

    * **Exempel på JSON med enskilda objekt**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exempel med radavgränsad JSON**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exempel med sammanfogad JSON**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Varje fil innehåller en matris med objekt.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat-exempel

**Fall 1: Kopiera data från JSON-filer**

**Exempel 1: hämta data från objektet och matrisen**

I det här exemplet mappas ett JSON-rotobjekt till en enskild post i tabellformat. Om du har en JSON-fil med följande innehåll:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att extrahera data från både objekten och matrisen:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i [mappa kolumner för datauppsättningar för källa till datauppsättningen målkolumner](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. Du kan använda för att kopiera data från en matris, `array[x].property` och få värdefull information för en viss egenskap från den `xth` objekt, eller du kan använda `array[*].property` att hitta värdet från alla objekt som innehåller sådan egenskap.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Exempel 2: korstillämpa flera objekt med samma mönster från en matris**

I det här exemplet omvandlas ett JSON-rotobjekt till flera poster i tabellform. Om du har en JSON-fil med följande innehåll:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

som du vill kopiera till en Azure SQL-tabell i följande format genom att förenkla data i matrisen och korskoppla med den gemensamma rotinformationen:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i [mappa kolumner för datauppsättningar för källa till datauppsättningen målkolumner](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` Anger om du vill iterera och extrahera data från objekten med samma mönster under **matris** `orderlines`.
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. I det här exemplet `ordernumber`, `orderdate`, och `city` under rotobjektet med JSON sökväg som börjar med `$.`, medan `order_pd` och `order_price` definieras med sökvägen från matriselementet utan `$.` .

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Observera följande:**

* Om `structure` och `jsonPathDefinition` inte har definierats i Data Factory-datauppsättningen identifierar kopieringsaktiviteten schemat från det första objektet och förenklar hela objektet.
* Om JSON-indata har en matris konverterar kopieringsaktiviteten som standard hela matrisvärdet till en sträng. Du kan välja att extrahera data från den med hjälp av `jsonNodeReference` och/eller `jsonPathDefinition`, eller hoppa över det genom att inte ange den i `jsonPathDefinition`.
* Om det finns dubblettnamn på samma nivå väljer kopieringsaktiviteten det sista.
* Egenskapsnamn är skiftlägeskänsliga. Två egenskaper med samma namn men med olika skiftlägen behandlas som två olika egenskaper.

**Fall 2: Skriva data till JSON-fil**

Om du har följande tabell i SQL-databas:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

och för varje post anger du förväntar dig att skriva till ett JSON-objekt i följande format:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Utdatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt `structure` avsnittet definierar de anpassade egenskapsnamnen i målfilen, `nestingSeparator` (standardvärdet är ””.) används för att identifiera kapslingslagret från namnet. Det här avsnittet är **valfritt** såvida du inte vill ändra egenskapsnamnet som jämförs med källkolumnnamnet, eller kapsla vissa av egenskaperna.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format"></a>Parquet-format

>[!NOTE]
>Data Factory introducerade en ny Parquet format-datauppsättning, se [Parquet format](format-parquet.md) artikel med information. Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill parsa Parquet-filer eller skriva data i Parquet-format anger du egenskapen `format` `type` till **ParquetFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Observera följande punkter:

* Komplexa data typer stöds inte (MAP, LIST).
* Tomt utrymme i kolumn namnet stöds inte.
* Parquet-filer har följande komprimeringsrelaterade alternativ: NONE, SNAPPY, GZIP och LZO. Data Factory har stöd för att läsa data från Parquet-filen i någon av dessa komprimerade format förutom LZO – den använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en Parquet-fil väljer Data Factory SNAPPY, som är standard för Parquet-formatet. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

> [!IMPORTANT]
> Om du inte kopierar Parquet-filer i **befintligt skick som är som är**integration runtime behöriga för den lokala datorn och data lager i molnet, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller OPENJDK** på din IR-dator. Se följande stycke med mer information.

För kopiering som körs på egen värd-IR med Parquet-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera system variabel *`JAVA_HOME`* för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera JVM. dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och ange system miljö variabeln JAVA_HOME.

>[!TIP]
>Om du kopierar data till/från Parquet-format med hjälp av självbetjäning Integration Runtime och träffa fel som säger "ett fel uppstod vid anrop till Java, meddelande: **Java. lang. OutOfMemoryError: Java heap-utrymme**" kan du lägga till en miljö variabel `_JAVA_OPTIONS` i en dator som är värd för IR med egen värd för att justera den minsta/högsta heap-storleken för JVM för att ge en sådan kopia och sedan köra pipelinen igen.

![Ange JVM heap-storlek för IR med egen värd](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: ange variabel `_JAVA_OPTIONS` med värde `-Xms256m -Xmx16g`. Flaggan `Xms` anger den första poolen för minnesallokering för en Java Virtual Machine (JVM), medan `Xmx` anger den maximala poolen för minnesallokering. Det innebär att JVM startas med `Xms` mängden minne och kommer att kunna använda maximalt `Xmx` mängd minne. Som standard använder ADF den minsta 64 MB och Max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Datatypen mappning för Parquet-filer

| Data factory tillfälliga datatyp | Parquet primitiv typ | Parquet ursprungliga typen (deserialisera) | Parquet ursprungliga typen (serialisera) |
|:--- |:--- |:--- |:--- |
| Boolesk | Boolesk | Saknas | Saknas |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binära | UInt64 | decimaltal |
| Enkel | Float | Saknas | Saknas |
| Double | Double | Saknas | Saknas |
| decimaltal | Binär | decimaltal | decimaltal |
| Sträng | Binär | Utf8 | Utf8 |
| DateTime | Int96 | Saknas | Saknas |
| TimeSpan | Int96 | Saknas | Saknas |
| DateTimeOffset | Int96 | Saknas | Saknas |
| ByteArray | Binär | Saknas | Saknas |
| GUID | Binär | Utf8 | Utf8 |
| char | Binär | Utf8 | Utf8 |
| CharArray | Stöds inte | Saknas | Saknas |

## <a name="orc-format"></a>ORC-format

>[!NOTE]
>Data Factory introducerade en ny ORC format-datauppsättning, se [Orc format](format-orc.md) artikel med information. Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill parsa ORC-filerna eller skriva data i ORC-format ange du egenskapen `format` `type` till **OrcFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "OrcFormat"
}
```

Observera följande punkter:

* Komplexa data typer stöds inte (STRUCT, MAP, LIST, UNION).
* Tomt utrymme i kolumn namnet stöds inte.
* ORC-filen har tre [komprimeringsrelaterade alternativ](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB och SNAPPY. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en ORC-fil väljer Data Factory ZLIB, som är standard för ORC. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

> [!IMPORTANT]
> Om du inte kopierar ORC-filer i **befintligt skick som är som är**integration runtime behöriga för den lokala datorn och data lager i molnet, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller OPENJDK** på din IR-dator. Se följande stycke med mer information.

För kopiering som körs på egen värd-IR med ORC-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera system variabel *`JAVA_HOME`* för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera JVM. dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och ange system miljö variabeln JAVA_HOME.

### <a name="data-type-mapping-for-orc-files"></a>Datatypen mappning för ORC-filer

| Data factory tillfälliga datatyp | ORC-typer |
|:--- |:--- |
| Boolesk | Boolesk |
| SByte | Byte |
| Byte | Kort |
| Int16 | Kort |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Lång |
| Int64 | Lång |
| UInt64 | Sträng |
| Enkel | Float |
| Double | Double |
| decimaltal | decimaltal |
| Sträng | Sträng |
| DateTime | Tidsstämpel |
| DateTimeOffset | Tidsstämpel |
| TimeSpan | Tidsstämpel |
| ByteArray | Binär |
| GUID | Sträng |
| char | Char(1) |

## <a name="avro-format"></a>AVRO-format

>[!NOTE]
>Data Factory introducerade en ny Avro format-datauppsättning, se [Avri format](format-avro.md) artikel med information. Följande konfigurationer på filbaserad data lager data uppsättning stöds fortfarande som-är för bakåtkompatibla compabitility. Du rekommenderas att använda den nya modellen som går framåt.

Om du vill parsa Avro-filerna eller skriva data i Avro-format anger du egenskapen `format` `type` till **AvroFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "AvroFormat",
}
```

Om du vill använda Avro-formatet i en Hive-tabell går du [självstudiekursen om Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observera följande punkter:

* [Komplexa datatyper](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, mappningar, unioner, och fasta).

## <a name="binary-format"></a>Binärt format

Mer information finns i artikeln om [binärt format](format-binary.md) .

## <a name="compression-support"></a>Stöd för komprimering

Azure Data Factory stöder compress/dekomprimera data vid kopiering. När du anger `compression` egenskapen i en indatauppsättning kopieringsaktiviteten läsa komprimerade data från källan och expandera den; och när du anger egenskapen i en utdatauppsättning kopieringsaktiviteten komprimera och sedan skriva data till mottagaren. Här följer några exempelscenarier:

* Läs GZIP-komprimerade data från en Azure-blob, expandera den och skriva Resultatdata till Azure SQL database. Du definierar den inkommande Azure Blob-datauppsättningen med den `compression` `type` egenskapen som GZIP.
* Läsa data från en oformaterad textfil från den lokala filsystem, komprimeras med GZip-format och skriva komprimerade data till en Azure-blob. Du definierar en Azure Blob-datauppsättning för utdata med den `compression` `type` egenskapen som GZip.
* Läs .zip-filen från FTP-servern, expandera den för att hämta filer inuti och landa filerna i Azure Data Lake Store. Du definierar en indatauppsättning FTP med den `compression` `type` egenskapen som ZipDeflate.
* Läsa en GZIP-komprimerade data från en Azure-blob, expandera den, komprimera den med hjälp av BZIP2 och skriva Resultatdata till en Azure-blob. Du definierar den inkommande Azure Blob-datauppsättningen med `compression` `type` GZIP och datauppsättningen för utdata med `compression` `type` inställd på BZIP2.

Om du vill ange komprimering för en datauppsättning, använder den **komprimering** egenskap i JSON-datauppsättningen som i följande exempel:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

Den **komprimering** avsnittet har två egenskaper:

* **Typ:** komprimerings-codec som kan vara **GZIP**, **Deflate**, **BZIP2**, eller **ZipDeflate**.
* **Nivå:** komprimeringsförhållandet, vilket kan vara **Optimal** eller **snabbast**.

  * **Snabbaste:** komprimeringsåtgärden bör slutföras så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad.
  * **Optimal**: komprimeringsåtgärden ska optimalt komprimeras, även om åtgärden tar längre tid att slutföra.

    Mer information finns i [komprimeringsnivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) avsnittet.

> [!NOTE]
> Inställningarna för komprimering stöds inte för data i den **AvroFormat**, **OrcFormat**, eller **ParquetFormat**. Vid läsning av filer i formaten, Data Factory identifierar och använder komprimerings-codec i metadata. Vid skrivning till filer i formaten, väljer Data Factory standard komprimerings-codec för formatet. Till exempel ZLIB för OrcFormat och SNAPPY för ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Filtyper och komprimerings format som inte stöds

Du kan använda utöknings funktionerna i Azure Data Factory för att transformera filer som inte stöds.
Två alternativ är Azure Functions och anpassade aktiviteter med hjälp av Azure Batch.

Du kan se ett exempel som använder en Azure-funktion för att [extrahera innehållet i en tar-fil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Mer information finns i [Azure Functions-aktivitet](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Du kan också bygga den här funktionen med en anpassad dotNet-aktivitet. Ytterligare information finns [här](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om filbaserat datalager som stöds av Azure Data Factory:

- [Azure Blob Storage-anslutningsapp](connector-azure-blob-storage.md)
- [Azure Data Lake Store-koppling](connector-azure-data-lake-store.md)
- [Amazon S3-koppling](connector-amazon-simple-storage-service.md)
- [Filsystemets anslutningsapp](connector-file-system.md)
- [FTP-anslutningsappen](connector-ftp.md)
- [SFTP-anslutningsapp](connector-sftp.md)
- [HDFS-koppling](connector-hdfs.md)
- [HTTP-anslutningsappen](connector-http.md)
