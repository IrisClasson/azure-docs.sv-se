---
title: Kopiera data till och från Azure SQL-hanterad instans
description: Lär dig hur du flyttar data till och från Azure SQL-hanterad instans med hjälp av Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: b06a01bf280d6d12e2df122d411e15e3432e61c7
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847081"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL-hanterad instans med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till och från Azure SQL-hanterad instans. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna SQL-hanterade instans anslutning stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Du kan kopiera data från SQL-hanterad instans till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till den SQL-hanterade instansen. En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder SQL Managed instance Connector:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token-autentisering med tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar data med hjälp av en SQL-fråga eller en lagrad procedur. Du kan också välja att parallellt kopiera från SQL MI-källa, se avsnittet [parallell kopia från SQL mi](#parallel-copy-from-sql-mi) för mer information.
- Som mottagare skapar mål tabellen automatiskt om den inte finns, baserat på käll schemat. lägga till data i en tabell eller anropa en lagrad procedur med anpassad logik under kopieringen.

>[!NOTE]
> SQL-hanterad instans [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) stöds inte av den här anslutningen nu. För att lösa problemet kan du använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin via en lokal integration Runtime. Lär dig mer från att [använda Always Encrypted](#using-always-encrypted) avsnittet. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill komma åt den [offentliga slut punkten](../azure-sql/managed-instance/public-endpoint-overview.md)för SQL-hanterad instans kan du använda en Azure Data Factory hanterad Azure integration Runtime. Se till att aktivera den offentliga slut punkten och Tillåt även offentlig slut punkts trafik på nätverks säkerhets gruppen så att Azure Data Factory kan ansluta till databasen. Mer information finns i [den här vägledningen](../azure-sql/managed-instance/public-endpoint-configure.md).

Om du vill komma åt den privata slut punkten för SQL-hanterad instans konfigurerar du en [integration runtime med egen värd](create-self-hosted-integration-runtime.md) som kan komma åt databasen. Om du etablerar integration runtime med egen värd i samma virtuella nätverk som den hanterade instansen kontrollerar du att integration runtime-datorn finns i ett annat undernät än din hanterade instans. Om du etablerar en integration runtime med egen värd i ett annat virtuellt nätverk än din hanterade instans kan du antingen använda en virtuell nätverks-peering eller ett virtuellt nätverk till en virtuell nätverks anslutning. Mer information finns i [ansluta ditt program till SQL-hanterad instans](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Azure Data Factory entiteter som är speciella för SQL Managed instance Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade SQL Managed instance-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureSqlMI**. | Ja |
| Begär |Den här egenskapen anger **ConnectionString** -informationen som behövs för att ansluta till SQL-hanterad instans med hjälp av SQL-autentisering. Mer information finns i följande exempel. <br/>Standardporten är 1433. Om du använder SQL-hanterad instans med en offentlig slut punkt anger du uttryckligen port 3342.<br> Du kan också ange ett lösen ord i Azure Key Vault. Om det är SQL-autentisering, hämtar du `password` konfigurationen från anslutnings strängen. Mer information finns i JSON-exemplet som följer tabellen och [lagrar autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Azure Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| tenant | Ange klient information, t. ex. domän namnet eller klient-ID: t, som ditt program finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| azureCloudType | För tjänstens huvud namns autentisering anger du vilken typ av Azure-moln miljö som ditt AAD-program är registrerat på. <br/> Tillåtna värden är **AzurePublic**, **AzureChina**, **azureusgovernment eller**och **AzureGermany**. Som standard används data fabrikens moln miljö. | Nej |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) används för att ansluta till data lagret. Du kan använda en lokal integration runtime eller en Azure integration Runtime om din hanterade instans har en offentlig slut punkt och ger Azure Data Factory åtkomst till den. Om inget värde anges används standard Azure integration Runtime. |Ja |

För olika typer av autentiseringar, se följande avsnitt om krav respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- [Azure AD Application token-autentisering: tjänstens huvud namn](#service-principal-authentication)
- [Azure AD Application token-autentisering: hanterade identiteter för Azure-resurser](#managed-identity)

### <a name="sql-authentication"></a>SQL-autentisering

**Exempel 1: Använd SQL-autentisering**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: Använd SQL-autentisering med ett lösen ord i Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda en tjänst objekts Azure AD-baserad autentisering av Azure AD-programtoken:

1. Följ stegen för att [etablera en Azure Active Directory administratör för din hanterade instans](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) från Azure Portal. Anteckna program namnet och följande värden som definierar den länkade tjänsten:

    - Program-ID
    - Program nyckel
    - Klientorganisations-ID

3. [Skapa inloggningar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) för den Azure Data Factory hanterade identiteten. I SQL Server Management Studio (SSMS) ansluter du till din hanterade instans med ett SQL Server konto som är en **sysadmin**. I **Master** -databasen kör du följande T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Skapa inneslutna databas användare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) för den Azure Data Factory hanterade identiteten. Anslut till den databas som du vill kopiera data från eller kör följande T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Ge den Data Factory hanterade identiteten nödvändiga behörigheter som du vanligt vis använder för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Konfigurera en länkad SQL-hanterad instans i Azure Data Factory.

**Exempel: Använd tjänstens huvud namns autentisering**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten för autentisering med SQL-hanterad instans. Den angivna fabriken kan komma åt och kopiera data från eller till databasen med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering.

1. Följ stegen för att [etablera en Azure Active Directory administratör för din hanterade instans](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Skapa inloggningar](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) för den Azure Data Factory hanterade identiteten. I SQL Server Management Studio (SSMS) ansluter du till din hanterade instans med ett SQL Server konto som är en **sysadmin**. I **Master** -databasen kör du följande T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Skapa inneslutna databas användare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) för den Azure Data Factory hanterade identiteten. Anslut till den databas som du vill kopiera data från eller kör följande T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Ge den Data Factory hanterade identiteten nödvändiga behörigheter som du vanligt vis använder för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Konfigurera en länkad SQL-hanterad instans i Azure Data Factory.

**Exempel: använder hanterad identitets autentisering**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som kan användas för att definiera data uppsättningar finns i artikeln data uppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av SQL-hanterad instans data uppsättning.

Följande egenskaper stöds för att kopiera data till och från SQL-hanterad instans:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzureSqlMITable**. | Ja |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `schema` belastning `table` . | Nej för källa, Ja för mottagare |

**Exempel**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som kan användas för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av källan för SQL-hanterad instans och mottagare.

### <a name="sql-managed-instance-as-a-source"></a>SQL-hanterad instans som källa

>[!TIP]
>Om du vill läsa in data från SQL MI effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från SQL mi](#parallel-copy-from-sql-mi).

Om du vill kopiera data från SQL-hanterad instans stöds följande egenskaper i avsnittet Kopiera aktivitets Källa:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **SqlMISource**. | Ja |
| sqlReaderQuery |Den här egenskapen använder den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade procedur som läser data från käll tabellen. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |De här parametrarna är för den lagrade proceduren.<br/>Tillåtna värden är namn-eller värdepar. Namn och Skift läge för parametrarna måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Nej |
| isolationLevel | Anger transaktions låsnings beteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Om inget värde anges används databasens standard isolerings nivå. Mer information finns i [det här dokumentet](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | Nej |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från SQL MI. <br>Tillåtna värden är: **ingen** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), kontrol leras graden av parallellitet för att samtidigt läsa in data från SQL mi av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. | Nej |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när alternativet partition inte är det `None` . | Nej |
| ***Under `partitionSettings` :*** | | |
| partitionColumnName | Ange namnet på käll kolumnen **i heltals-eller datum/datum/tid-typ** som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras indexet eller primär nyckeln för tabellen automatiskt och används som partition-kolumn.<br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfDynamicRangePartitionCondition ` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-mi) . | Nej |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för delning av partition intervall. Det här värdet används för att bestämma partitionens kliv, inte för att filtrera raderna i tabellen. Alla rader i tabellen eller frågeresultatet kommer att partitioneras och kopieras. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.  <br>Använd när alternativet partition är `DynamicRange` . Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-mi) . | Nej |
| partitionLowerBound | Det minsta värdet för partition-kolumnen för delning av partition intervall. Det här värdet används för att bestämma partitionens kliv, inte för att filtrera raderna i tabellen. Alla rader i tabellen eller frågeresultatet kommer att partitioneras och kopieras. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.<br>Använd när alternativet partition är `DynamicRange` . Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-mi) . | Nej |

**Observera följande punkter:**

- Om **sqlReaderQuery** har angetts för **SqlMISource**kör kopierings aktiviteten den här frågan mot den SQL-hanterade instans källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- Om du inte anger någon av **sqlReaderQuery** -eller **sqlReaderStoredProcedureName** -egenskapen används kolumnerna som definierats i avsnittet "struktur" i JSON-datauppsättnings-JSON för att skapa en fråga. Frågan `select column1, column2 from mytable` körs mot SQL-hanterad instans. Om definitionen för data uppsättningen inte har "struktur" är alla kolumner markerade från tabellen.

**Exempel: Använd en SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel: använda en lagrad procedur**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlMISource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definitionen av den lagrade proceduren**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-managed-instance-as-a-sink"></a>SQL-hanterad instans som mottagare

> [!TIP]
> Lär dig mer om de Skriv beteenden, konfigurationer och bästa metoder som stöds av [bästa praxis för att läsa in data i SQL-hanterad instans](#best-practice-for-loading-data-into-sql-managed-instance).

För att kunna kopiera data till SQL-hanterad instans, stöds följande egenskaper i avsnittet Kopiera aktivitets mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **SqlMISink**. | Ja |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till en SQL-hanterad instans. Den anropas bara en gång per kopierings körning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |
| tableOption | Anger om [mottagar tabellen ska skapas automatiskt om den](copy-activity-overview.md#auto-create-sink-tables) inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när mottagaren anger den lagrade proceduren. Tillåtna värden är: `none` (standard), `autoCreate` . |Nej |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en mål tabell. <br/>Den här lagrade proceduren *anropas per batch*. För åtgärder som bara körs en gång och som inte har något att göra med källdata, till exempel ta bort eller trunkera, använder du `preCopyScript` egenskapen.<br>Se exempel från [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink). | Nej |
| storedProcedureTableTypeParameterName |Parameter namnet för den tabell typ som anges i den lagrade proceduren.  |Nej |
| sqlWriterTableType |Det tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktiviteten gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga de data som kopieras med befintliga data. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-och värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. | Nej |
| writeBatchSize |Antal rader som ska infogas i SQL-tabellen *per batch*.<br/>Tillåtna värden är heltal för antalet rader. Som standard bestämmer Azure Data Factory dynamiskt rätt batchstorlek baserat på rad storleken.  |Nej |
| writeBatchTimeout |Den här egenskapen anger vänte tiden för åtgärden Infoga som ska slutföras innan tids gränsen uppnås.<br/>Tillåtna värden är för TimeSpan. Ett exempel är "00:30:00", som är 30 minuter. |Nej |

**Exempel 1: Lägg till data**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exempel 2: anropa en lagrad procedur under kopiering**

Läs mer om att [anropa en lagrad procedur från en SQL mi-mottagare](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-mi"></a>Parallell kopiering från SQL MI

Azure SQL Managed instance Connector i kopierings aktivitet tillhandahåller inbyggd data partitionering för att kopiera data parallellt. Du kan hitta alternativ för data partitionering på fliken **källa** i kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-sql-server/connector-sql-partition-options.png)

När du aktiverar partitionerad kopiering kör kopierings aktiviteten parallella frågor mot din SQL MI-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från SQL-mi.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från SQL-MI. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager rekommenderar vi att du skriver till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell med fysiska partitioner.        | **Partitions alternativ**: fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. |
| Fullständig belastning från stor tabell, utan fysiska partitioner, med en heltals-eller datetime-kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Partitionstabell** (valfritt): Ange den kolumn som används för att partitionera data. Om den inte anges används index eller primär nyckel kolumn.<br/>**Partitionens övre gränser** och * * partitionens nedre gränser * * (valfritt): Ange om du vill fastställa partitionens kliv. Detta är inte för att filtrera raderna i tabellen, och alla rader i tabellen kommer att partitioneras och kopieras. Om detta inte anges identifierar kopierings aktiviteten automatiskt värdena.<br><br>Om t. ex. partitionens kolumn "ID" har värden mellan 1 och 100 och du anger den nedre gränser som 20 och den övre gränser som 80, med parallell kopiering som 4, Data Factory hämtar data med 4 partitions-ID: n i intervallet <= 20, [21, 50], [51, 80] och >= 81. |
| Läs in en stor mängd data med hjälp av en anpassad fråga utan fysiska partitioner, medan en heltals-eller datum-/datetime-kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data.<br>**Partitionens övre gränser** och **partitionens nedre gränser** (valfritt): Ange om du vill fastställa partitionens kliv. Detta är inte för att filtrera rader i tabellen, partitioneras och kopieras alla rader i frågeresultatet. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.<br><br>Under körningen ersätts Data Factory `?AdfRangePartitionColumnName` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till SQL mi. <br>Om t. ex. partitionens kolumn "ID" har värden mellan 1 och 100 och du anger den nedre gränser som 20 och den övre gränser som 80, med parallell kopiering som 4, Data Factory hämtar data med 4 partitions-ID: n i intervallet <= 20, [21, 50], [51, 80] och >= 81. |

Metod tips för att läsa in data med partitions alternativ:

1. Välj en distinkt kolumn som partitionstabell (t. ex. primär nyckel eller unik nyckel) för att undvika data skevning. 
2. Om tabellen har en inbyggd partition använder du partition alternativ "fysiska partitioner i tabellen" för att få bättre prestanda.  
3. Om du använder Azure Integration Runtime för att kopiera data, kan du ställa in större "[data integrerings enheter (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) för att utnyttja mer data bearbetnings resurser. Kontrol lera tillämpliga scenarier där.
4. "[Graden av kopierings parallellitet](copy-activity-performance-features.md#parallel-copy)" styr partitionsnumret, vilket anger att det här antalet är för stort för att ont prestanda, rekommenderar att du anger det här antalet som (DIU eller antalet IR-noder med egen värd) * (2 till 4).

**Exempel: fullständig belastning från stor tabell med fysiska partitioner**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "SqlMISource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Bästa praxis vid inläsning av data i SQL-hanterad instans

När du kopierar data till SQL-hanterad instans kan du behöva ett annat Skriv beteende:

- [Lägg till](#append-data): mina källdata har endast nya poster.
- [Upsert](#upsert-data): mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill läsa in hela dimensions tabellen på nytt varje tillfälle.
- [Skriv med anpassad logik](#write-data-with-custom-logic): Jag behöver extra bearbetning före den slutliga infogningen i mål tabellen. 

Se respektive avsnitt för hur du konfigurerar i Azure Data Factory och metod tips.

### <a name="append-data"></a>Lägg till data

Att lägga till data är standard beteendet för SQL Managed instance Sink-anslutningen. Azure Data Factory skapar en Mass infogning för att skriva till tabellen effektivt. Du kan konfigurera källan och mottagaren efter detta i kopierings aktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data som ska kopieras kan du samla in alla poster i en mellanlagringsplats med hjälp av kopierings aktiviteten och sedan köra en lagrad procedur aktivitet för att tillämpa en [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-mi-current) -eller INSERT-Update-instruktion i en bild. 

Kopierings aktiviteten stöder för närvarande inte inläsning av data i en tillfällig databas tabell. Det finns ett avancerat sätt att konfigurera den med en kombination av flera aktiviteter, se [optimera SQL Database upsert-scenarier](https://github.com/scoriani/azuresqlbulkupsert). Nedan visas ett exempel på hur du använder en permanent tabell som mellanlagring.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopierings aktivitet** som är länkad till en **lagrad procedur aktivitet**. Den tidigare kopierar data från din käll lagring till en Azure SQL Managed instance Staging-tabell, till exempel **UpsertStagingTable**, som tabell namn i data uppsättningen. Sedan anropar den senare en lagrad procedur för att slå samman käll data från mellanlagringsplatsen till mål tabellen och rensa mellanlagrings tabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med SAMMANSLAGNINGs logik, som i följande exempel, som pekas från föregående lagrade procedur aktivitet. Anta att målet är **marknadsförings** tabellen med tre kolumner: **profil**, **State**och **Category**. Gör upsert baserat på kolumnen **profil** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Alternativ 2:** Du kan välja att [anropa en lagrad procedur i kopierings aktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden kör varje batch (som styrs av `writeBatchSize` egenskapen) i käll tabellen i stället för att använda Mass infogning som standard metod i kopierings aktiviteten.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i en kopierings aktivitets mottagare. I det här fallet kör Azure Data Factory skriptet först för varje kopierings aktivitet som körs. Sedan kör den kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med den senaste informationen anger du ett skript för att först ta bort alla poster innan du läser in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i avsnittet [upsert data](#upsert-data) . När du behöver tillämpa extra bearbetning före den slutliga infogningen av källdata i mål tabellen, kan du läsa in till en mellanlagringsplats och sedan anropa aktiviteten för lagrad procedur, eller anropa en lagrad procedur i kopierings aktivitets Sink för att tillämpa data.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till SQL-hanterad instans kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar i varje batch i käll tabellen. Funktionen för lagrade procedurer utnyttjar [tabell värdes parametrar](https://msdn.microsoft.com/library/bb675163.aspx).

Du kan använda en lagrad procedur när inbyggda kopierings metoder inte fungerar. Ett exempel är när du vill använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen. Några extra bearbetnings exempel är när du vill slå samman kolumner, leta upp ytterligare värden och infoga i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i den SQL Server databasen. Anta att indata och **marknadsförings** tabellen för mottagare har tre kolumner: **profil**, **State**och **Category**. Gör upsert baserat på kolumnen **profil** och Använd den bara för en viss kategori som kallas "ProduktA".

1. I databasen definierar du tabell typen med samma namn som **sqlWriterTableType**. Schemat för tabell typen är detsamma som det schema som returnerades av indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från din angivna källa och sammanfogar dem i utdatatabellen. Parameter namnet för tabell typen i den lagrade proceduren är samma som **TableName** som definierats i data uppsättningen.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. I Azure Data Factory definierar du avsnittet **SQL mi Sink** i kopierings aktiviteten enligt följande:

    ```json
    "sink": {
        "type": "SqlMISink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-managed-instance"></a>Data typs mappning för SQL-hanterad instans

När data kopieras till och från SQL-hanterad instans används följande mappningar från SQL-hanterade instans data typer för att Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar från käll schema och data typ till mottagaren finns i [mappningar för scheman och data typer](copy-activity-schema-and-type-mapping.md).

| SQL-hanterad instans data typ | Azure Data Factory data typen Interim |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte [] |
| bit |Boolesk |
| char |Sträng, char [] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary (max)) |Byte [] |
| Float |Double |
| image |Byte [] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, char [] |
| ntext |Sträng, char [] |
| numeric |Decimal |
| nvarchar |Sträng, char [] |
| real |Enskilt |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objekt |
| text |Sträng, char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Sträng, char [] |
| xml |Sträng |

>[!NOTE]
> För data typer som mappar till typen decimal, stöder för tillfället kopierings aktiviteten precisionen upp till 28. Om du har data som kräver precision som är större än 28, bör du överväga att konvertera till en sträng i en SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Använda Always Encrypted

När du kopierar data från/till Azure SQL-hanterad instans med [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)använder du [allmän ODBC-anslutning](connector-odbc.md) och SQL Server ODBC-drivrutin via lokal integration Runtime. Den här Azure SQL Managed instance Connector stöder inte Always Encrypted nu. 

Mer specifikt:

1. Konfigurera en egen värd Integration Runtime om du inte har någon. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .

2. Hämta 64-bitars ODBC-drivrutinen för [SQL Server härifrån och](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-mi-current)installera på den integration runtime datorn. Läs mer om hur driv rutinen fungerar när [du använder Always Encrypted med ODBC-drivrutinen för SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current#using-the-azure-key-vault-provider).

3. Skapa länkad tjänst med ODBC-typ för att ansluta till din SQL-databas, se följande exempel:

    - Använda **SQL-autentisering**: Ange ODBC-anslutningssträngen enligt nedan och välj **grundläggande** autentisering för att ange användar namn och lösen ord.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Använda **Data Factory hanterad identitets autentisering**: 

        1. Följ samma [krav](#managed-identity) för att skapa databas användare för den hanterade identiteten och ge rätt roll i databasen.
        2. I länkad tjänst anger du ODBC-anslutningssträngen enligt nedan och väljer **Anonym** autentisering som själva anslutnings strängen anger `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Skapa data uppsättning och kopiera aktivitet med ODBC-typ enligt detta. Läs mer från artikeln om [ODBC-koppling](connector-odbc.md) .

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
