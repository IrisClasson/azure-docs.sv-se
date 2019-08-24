---
title: Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds butiker till Azure SQL Data Warehouse eller SQL Data Warehouse till mottagarens datalager med Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: jingwang
ms.openlocfilehash: 45f7db943499b8a722b8e203d676d1d80eb5091e
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996684"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Aktuell version](connector-azure-sql-data-warehouse.md)

Den här artikeln beskriver hur du kopierar data till och från Azure SQL Data Warehouse. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Azure Blob Connector stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Mer specifikt stöder den här Azure SQL Data Warehouse-anslutningsappen dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och autentisering med Azure Active Directory (Azure AD) Application-token med ett tjänstens huvudnamn eller hanterade identiteter för Azure-resurser.
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som en källa.
- Läs in data med hjälp av PolyBase eller en grupp Infoga som en mottagare. Vi rekommenderar PolyBase för ger kopieringen bättre prestanda.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime kan du konfigurera en [Azure SQL-serverbrandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en lokal integration runtime kan du konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Det här intervallet inkluderar den datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

> [!TIP]
> För att uppnå bästa prestanda kan du använda PolyBase för att läsa in data i Azure SQL Data Warehouse. Den [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) -avsnittet innehåller information om. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory-entiteter som är specifika för en Azure SQL Data Warehouse-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för en länkad Azure SQL Data Warehouse-tjänst:

| Egenskap            | Beskrivning                                                  | Krävs                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Type-egenskapen måste anges till **AzureSqlDW**.             | Ja                                                          |
| connectionString    | Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instansen för den **connectionString** egenskapen. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ange lösen ord/tjänstens huvud namn i Azure Key Vault, och om det är SQL- `password` autentisering hämtar du konfigurationen från anslutnings strängen. Se JSON-exemplet under tabellen och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja                                                          |
| servicePrincipalId  | Ange programmets klient-ID.                         | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| tenant              | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia          | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. | Nej                                                           |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel, respektive:

- [SQL-autentisering](#sql-authentication)
- Azure AD Application token-autentisering: [Tjänstens huvud](#service-principal-authentication)
- Azure AD Application token-autentisering: [Hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du når fel med felkod som ”UserErrorFailedToConnectToSqlServer” och visas som ”sessionens tidsgräns för databasen är XXX och har uppnåtts”., lägga till `Pooling=false` till din anslutningssträng och försök igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Länkad tjänst-exempel som använder SQL-autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Lösen ord i Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Följ dessa steg om du vill använda tokenautentisering för service principal-baserade Azure AD-program:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören har fullständig åtkomst till databasen.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja behörigheter krävs för tjänstens huvudnamn** som du brukar för SQL-användare eller andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurera en länkad Azure SQL Data Warehouse-tjänsten** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Länkad tjänst-exempel som använder autentisering av tjänstens huvudnamn

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

### <a name="managed-identity"></a> Hanterade identiteter för autentisering av Azure-resurser

En data factory kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika fabriken. Du kan använda den här hanterade identiteten för Azure SQL Data Warehouse autentisering. Den angivna datafabriken kan komma åt och kopieringsdata från eller till dina data warehouse med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering:

1. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för Azure SQL-servern på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören har fullständig åtkomst till databasen.

2. **[Skapa inneslutna databas användare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** för den Data Factory hanterade identiteten. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst behörigheten ALTER ANY användare. Kör följande T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Ge den Data Factory hanterade identiteten nödvändiga behörigheter** som du vanligt vis använder för SQL-användare och andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfigurera en länkad Azure SQL Data Warehouse-tjänsten** i Azure Data Factory.

**Exempel:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse-datauppsättningen.

Följande egenskaper stöds för att kopiera data från eller till Azure SQL Data Warehouse:

| Egenskap  | Beskrivning                                                  | Krävs                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Den **typ** egenskap måste anges till **AzureSqlDWTable**. | Ja                         |
| tableName | Namnet på tabellen eller vyn i Azure SQL Data Warehouse-instans som den länkade tjänsten refererar till. | Nej för källa, Ja för mottagare |

#### <a name="dataset-properties-example"></a>Exempel med datauppsättningen egenskaper

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse källa och mottagare.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse som källa

Om du vill kopiera data från Azure SQL Data Warehouse, ange den **typ** -egenskapen i Kopieringsaktiviteten källan till **SqlDWSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap                     | Beskrivning                                                  | Krävs |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej       |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej       |
| storedProcedureParameters    | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej       |

### <a name="points-to-note"></a>Saker att Observera

- Om den **sqlReaderQuery** har angetts för den **SqlSource**, Kopieringsaktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data. Du kan också ange en lagrad procedur. Ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar parametrar för den lagrade proceduren.
- Om du inte anger något **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, kolumnerna som definieras i den **struktur** delen av datauppsättnings-JSON är van vid Skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL Data Warehouse. Om definitionen för datauppsättningen inte har den **struktur**, alla kolumner har valts från tabellen.

#### <a name="sql-query-example"></a>Exempel för SQL-fråga

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Lagrad procedur-exempel

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="stored-procedure-definition"></a>Lagrad Procedurdefinition

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

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse som mottagare

Om du vill kopiera data till Azure SQL Data Warehouse, ange Mottagartyp i Kopieringsaktiviteten till **SqlDWSink**. Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap          | Beskrivning                                                  | Krävs                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Anger om du vill använda PolyBase, när så är tillämpligt, i stället för BULKINSERT mekanism. <br/><br/> Vi rekommenderar att du läser in data i SQL Data Warehouse med PolyBase. Se den [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet begränsningar och information.<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard). | Nej                                            |
| polyBaseSettings  | En grupp egenskaper som kan anges när den **allowPolybase** är inställd på **SANT**. | Nej                                            |
| rejectValue       | Anger det tal eller procentandelen rader som kan avvisas innan frågan inte kunde köras.<br/><br/>Läs mer om Polybases avvisningsalternativen i avsnittet argument i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Tillåtna värden är 0 (standard), 1, 2, osv. | Nej                                            |
| rejectType        | Anger om den **rejectValue** alternativet är ett exakt värde eller en procentandel.<br/><br/>Tillåtna värden är **värdet** (standard) och **procent**. | Nej                                            |
| rejectSampleValue | Anger antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade raden.<br/><br/>Tillåtna värden är 1, 2, osv. | Ja, om den **rejectType** är **procent**. |
| useTypeDefault    | Anger hur du hanterar värden som saknas i avgränsade textfiler när PolyBase hämtar data från textfilen.<br/><br/>Mer information om den här egenskapen från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard).<br><br> | Nej                                            |
| writeBatchSize    | Antal rader som ska infogas i SQL-tabellen **per batch**. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **heltal** (antal rader). Som standard fastställer Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. | Nej                                            |
| writeBatchTimeout | Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **timespan**. Exempel: "00:30:00" (30 minuter). | Nej                                            |
| preCopyScript     | Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan du skriver data till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen för att rensa förinstallerade data. | Nej                                            |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse sink exempel

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Läs mer om hur du använder PolyBase för att effektivt läsa in SQL Data Warehouse i nästa avsnitt.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använda PolyBase för att läsa in data i Azure SQL Data Warehouse

Med hjälp av [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in en stor mängd data till Azure SQL Data Warehouse med högt dataflöde. Du ser en stor vinst i dataflödet med PolyBase i stället för BULKINSERT standardmekanismen. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Om dina källdata finns i **Azure Blob, Azure Data Lake Storage gen1 eller Azure Data Lake Storage Gen2**och **formatet är PolyBase-kompatibelt**, kan du använda kopierings aktivitet för att direkt anropa PolyBase för att låta Azure SQL Data Warehouse hämta data från källan. Mer information finns i  **[dirigera kopiera genom att använda PolyBase](#direct-copy-by-using-polybase)** .
* Om dina källdatalagret och format ursprungligen inte stöds av PolyBase, använder du den **[mellanlagrad kopiering genom att använda PolyBase](#staged-copy-by-using-polybase)** funktionen i stället. Funktionen mellanlagrad kopiering ger dig också bättre genomströmning. Den konverterar automatiskt data till PolyBase-kompatibelt format. Och den lagrar data i Azure Blob storage. Det hämtar sedan data till SQL Data Warehouse.

>[!TIP]
>Lär dig mer om [metod tips för att använda PolyBase](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Direct kopiera genom att använda PolyBase

SQL Data Warehouse PolyBase stöder direkt Azure Blob, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet använder du PolyBase för att kopiera direkt från käll data lagret till Azure SQL Data Warehouse. Annars kan du använda [mellanlagrad kopiering genom att använda PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Om du vill kopiera data på ett effektivt sätt till SQL Data Warehouse kan du läsa mer från [Azure Data Factory gör det ännu enklare och bekvämt att få insikter från data när du använder data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, Azure Data Factory kontrollerar du inställningarna och faller automatiskt tillbaka till BULKINSERT mekanism för dataförflyttning.

1. Den **länkade käll tjänsten** har följande typer och autentiseringsmetoder:

    | Typ av käll data lager som stöds                             | Typ av källtyp som stöds                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Autentisering av konto nyckel, hanterad identitets autentisering |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autentisering av tjänstens huvudnamn                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autentisering av konto nyckel, hanterad identitets autentisering |

    >[!IMPORTANT]
    >Om din Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig mer om de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitets verifiering](connector-azure-blob-storage.md#managed-identity) och [Azure Data Lake Storage Gen2-hanterad identitets verifiering](connector-azure-data-lake-storage.md#managed-identity) .

2. **Käll data formatet** är av **Parquet**, **Orc**eller avgränsad **text**, med följande konfigurationer:

   1. Mappsökvägen innehåller inte något filter för jokertecken.
   2. Fil namnet är tomt eller pekar på en enskild fil. Om du anger jokertecken som fil namn i kopierings aktiviteten, kan `*` det `*.*`bara vara eller.
   3. `rowDelimiter`är **standard**, **\n**, **\r\n**eller **\r**.
   4. `nullValue`är kvar som standard eller inställt på **tom sträng** ("") `treatEmptyAsNull` och är kvar som standard eller anges till sant.
   5. `encodingName`är kvar som standard eller anges till **UTF-8**.
   6. `quoteChar`, `escapeChar`, och `skipLineCount` har inte angetts. Stöd för PolyBase hoppa över rubrikraden, vilket kan konfigureras som `firstRowAsHeader` i ADF.
   7. `compression` kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

3. Om din källa är en mapp `recursive` måste i kopierings aktiviteten vara inställd på sant.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Mellanlagrad kopiering genom att använda PolyBase

När dina källdata inte uppfyller villkoret i föregående avsnitt, aktivera kopiering via en mellanliggande mellanlagring Azure Blob storage-instans. Den kan inte Azure Premium Storage. Azure Data Factory körs i det här fallet automatiskt transformationer på data för att uppfylla formatkraven data med PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse. Slutligen rensar den tillfälliga data från blob-lagringen. Se [mellanlagrad kopiering](copy-activity-performance.md#staged-copy) mer information om hur du kopierar data via en mellanlagrings Azure Blob storage-instans.

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure Storage-kontot med Interim Blob Storage. Ange `enableStaging` sedan egenskaperna och `stagingSettings` för kopierings aktiviteten som det visas i följande kod.

>[!IMPORTANT]
>Om din mellanlagrings Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitetsautentisering](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Metodtips för att använda PolyBase

Följande avsnitt innehåller bästa praxis utöver de som nämns i [Metodtips för Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Behörighet krävs

Användaren som läser in data i SQL Data Warehouse måste ha för att använda PolyBase [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) i måldatabasen. Ett sätt att uppnå som är att lägga till användaren som en medlem i den **db_owner** roll. Lär dig hur du gör den [översikt över SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Ange gränser Radstorleken och data

PolyBase-inläsningar är begränsade till rader som är mindre än 1 MB. Den kan inte användas för att läsa in till VARCHR (MAX), NVARCHAR (MAX) eller VARBINARY (MAX). Mer information finns i [kapacitetsbegränsningar i SQL Data Warehouse-tjänsten](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När dina källdata innehåller rader som är större än 1 MB, kanske du vill dela lodrätt källtabellerna i flera små. Kontrollera att den största storleken på varje rad inte överskrider gränsen. Mindre tabeller kan sedan lästs in med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

Du kan också använda icke-PolyBase för att läsa in data med hjälp av ADF genom att inaktivera inställningen Tillåt PolyBase för data med sådana breda kolumner.

### <a name="polybase-troubleshooting"></a>PolyBase-fel sökning

**Läser in till decimal kolumn**

Om dina källdata är i text format eller andra icke-PolyBase-kompatibla lager (med mellanlagrad kopia och PolyBase) och den innehåller ett tomt värde som ska läsas in i SQL Data Warehouse decimal kolumn, kan du trycka på följande fel:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Lösningen är att avmarkera alternativet**Använd standard**alternativet (som falskt) i kopierings aktiviteten handfat-> PolyBase-inställningar. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" är en PolyBase-inbyggd konfiguration som anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen. 

**Andra**

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklass

Tilldela en större resursklass till användaren som läser in data i SQL Data Warehouse via PolyBase för att uppnå bästa möjliga genomflöde.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** i Azure SQL Data Warehouse

I följande tabell innehåller exempel på hur du anger den **tableName** egenskap i JSON-datauppsättning. Den visar flera kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamn | **tableName** JSON-egenskap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable eller dbo.MyTable eller [dbo].[Tabell] |
| dbo1      | MyTable    | dbo1.MyTable eller [dbo1].[Tabell]          |
| dbo       | My.Table   | [My.Table] eller [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Om du ser följande fel kan problemet vara värdet du angav för den **tableName** egenskapen. Se tabellen ovan för det korrekta sättet att ange värden för den **tableName** JSON-egenskap.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden

PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Ett exempel är en tabell med fyra kolumner där en av dem har definierats med ett standardvärde. Indata måste fortfarande ha fyra kolumner. En indatauppsättning med tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en särskild form av standardvärdet. Om kolumnen är nullbar vara inkommande data i blob för kolumnen tom. Men den kan inte vara saknas från datauppsättningen för indata. PolyBase infogas NULL för värden som saknas i Azure SQL Data Warehouse.

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flödet.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datatypsmappningen för Azure SQL Data Warehouse

När du kopierar data från eller till Azure SQL Data Warehouse, används följande mappningar från Azure SQL Data Warehouse-datatyper till Azure Data Factory tillfälliga-datatyper. Se [schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

>[!TIP]
>Se [tabell data typer i Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) artikel om data typer som stöds av SQL DW och de lösningar som inte stöds.

| Azure SQL Data Warehouse-datatyp    | Data Factory tillfälliga datatyp |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| DateTime                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
