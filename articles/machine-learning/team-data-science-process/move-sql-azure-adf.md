---
title: SQL Server-data till SQL Azure med Azure Data Factory - Team Data Science Process
description: Ställ in en ADF-pipeline som composes två data migreringsaktiviteter som tillsammans flyttar data dagligen mellan lokala-databaser och i molnet.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b64aa6c0e6e0e3bf449d44996df3223b12a69923
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982421"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory

Den här artikeln visar hur du flyttar data från en lokal SQL Server-databas till en SQL Azure-databas via Azure Blob Storage med hjälp av Azure Data Factory (ADF): den här metoden är en äldre metod som stöds och som har fördelarna med en replikerad mellanlagrings kopia, men [vi föreslår att titta på vår data migration-sida för de senaste alternativen](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

En tabell som sammanfattar olika alternativ för att flytta data till en Azure SQL Database finns i [flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Introduktion: Vad är ADF och när bör det användas för att migrera data?
Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Viktiga begrepp i ADF-modellen är pipeline. En pipeline är en logisk gruppering av aktiviteter, som definierar åtgärderna som ska utföras på data i datauppsättningar. Länkade tjänster används för att definiera den information som behövs för Data Factory ska kunna ansluta till dataresurser.

Med ADF, kan befintliga tjänster för databearbetning sammanställas i datapipelines som är tillgängliga och hanterade i molnet. Dessa datapipelines kan schemaläggas att mata in, förbereda, transformera, analysera och publicera data och ADF hanterar och samordnar de komplexa data och bearbetning av beroenden. Lösningar kan snabbt byggas och distribueras i molnet, ansluter ett växande antal lokala och molnbaserade datakällor.

Överväg att använda ADF:

* När data ska migreras kontinuerligt i ett scenario med hybridanvändning som har åtkomst till både lokalt och i molnresurser
* När data är överförda eller behöver ändras eller ha affärslogik som läggs till det när du håller på att migreras.

ADF tillåter schemaläggning och övervakning av jobb med hjälp av enkla JSON-skript som hanterar överföringen av data på regelbunden basis. ADF har även andra funktioner, till exempel stöd för komplexa åtgärder. Mer information om ADF finns i dokumentationen på [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scenariot
Vi konfigurerar en ADF-pipeline som composes två aktiviteter för migrering av data. Tillsammans flytta data dagligen mellan en lokal SQL-databas och en Azure SQL Database i molnet. Det finns två aktiviteter:

* Kopiera data från en lokal SQL Server-databas till en Azure Blob Storage-konto
* Kopiera data från Azure Blob Storage-kontot till en Azure SQL Database.

> [!NOTE]
> Anvisningarna som visas här har anpassats från mer detaljerade självstudierna från teamet för ADF: [kopiera data från en lokal SQL Server-databas till Azure Blob storage](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) referenser till de relevanta avsnitten ämnes tillhandahålls när lämpliga.
>
>

## <a name="prereqs"></a>Förhandskrav
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. Du kan använda ett Azure storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure storage-konto kan du läsa den [skapa ett lagringskonto](../../storage/common/storage-account-create.md) artikeln. När du har skapat lagringskontot kan behöva du hämta den kontonyckel som används för att komma åt lagringsutrymmet. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
* Åtkomst till en **Azure SQL Database**. Om du måste konfigurera en Azure SQL Database innehåller avsnittet [komma igång med Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Anvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Den här proceduren använder den [Azure-portalen](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Ladda upp data till din lokala SQL Server
Vi använder den [NYC Taxi datauppsättning](https://chriswhong.com/open-data/foil_nyc_taxi/) att demonstrera migreringsprocessen. NYC Taxi datauppsättningen är tillgänglig, enligt vad som anges i inlägget, på Azure-bloblagring [NYC Taxiinformation](https://www.andresmh.com/nyctaxitrips/). Har två filer, filen trip_data.csv, som innehåller information om resa och filen trip_far.csv, som innehåller information om avgiften betalat för varje resa. Ett exempel och en beskrivning av dessa filer finns i [NYC Taxi och RETUR datauppsättning beskrivning](sql-walkthrough.md#dataset).

Du kan anpassa det förfarande som anges här till en uppsättning med dina egna data, eller så kan du följa stegen som beskrivs med hjälp av NYC Taxi-datauppsättningen. Om du vill överföra NYC Taxi-datauppsättning till din lokala SQL Server-databas, följer du proceduren som beskrivs i [Bulk importera Data till SQL Server-databas](sql-walkthrough.md#dbload). Dessa instruktioner är för en SQL Server på en Azure-dator, men proceduren för att ladda upp till en lokal SQL Server är samma.

## <a name="create-adf"></a> Skapa en Azure Data Factory
Instruktionerna för att skapa en ny Azure-Datafabrik och en resursgrupp i den [Azure-portalen](https://portal.azure.com/) tillhandahålls [skapa en Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Namnge den nya instansen av ADF *adfdsp* och namnet på den resursgrupp som skapade *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installera och konfigurera Azure Data Factory Integration Runtime
Integration Runtime är en kund infrastruktur för dataintegrering används av Azure Data Factory för att tillhandahålla funktioner för dataintegrering i olika nätverksmiljöer. Den här körningen kallades ”Data Management Gateway”.

Konfigurera genom att [följa anvisningarna för att skapa en pipeline](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Skapa länkade tjänster för att ansluta till dataresurser
En länkad tjänst definierar den information som behövs för Azure Data Factory för att ansluta till en data. Vi har tre resurser i det här scenariot som krävs för länkade tjänster:

1. En lokal SQLServer
2. Azure Blob Storage
3. Azure SQL-databas

Stegvisa anvisningar för att skapa länkade tjänster finns i [Skapa länkade tjänster](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definiera och skapa tabeller för att ange hur du kommer åt datauppsättningarna
Skapa tabeller som anger strukturen, plats och tillgänglighet för datauppsättningar med följande skriptbaserade procedurer. JSON-filer används för att definiera tabellerna. Mer information om strukturen för de här filerna finns i [datauppsättningar](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Du bör köra de `Add-AzureAccount` cmdlet innan du kör den [New AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet för att bekräfta att rätt Azure-prenumeration har valts för Kommandokörningen. Dokumentation för denna cmdlet finns i [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

JSON-baserade definitionerna i tabellerna använda följande namn:

* den **tabellnamn** i en lokal SQL server är *nyctaxi_data*
* den **behållarnamn** i Azure Blob Storage-kontot är *containername*

Tre tabelldefinitionerna krävs för den här ADF-pipeline:

1. [En lokal SQL-tabell](#adf-table-onprem-sql)
2. [BLOB-tabell](#adf-table-blob-store)
3. [SQL Azure-tabell](#adf-table-azure-sql)

> [!NOTE]
> Dessa procedurer använder Azure PowerShell för att definiera och skapa ADF-aktiviteter. Men dessa uppgifter kan också utföras med hjälp av Azure-portalen. Mer information finns i [skapa datauppsättningar](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>En lokal SQL-tabell
Tabelldefinitionen för en lokal SQL Server har angetts i följande JSON-filen:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Kolumnnamnen ingick inte. Du kan välja på kolumnnamnen icke genom att inkludera dem här (information finns i [ADF dokumentation](../../data-factory/copy-activity-overview.md) avsnittet.

Kopiera JSON-definition för tabellen till en fil med namnet *onpremtabledef.json* och spara den på en känd plats (här antas vara *C:\temp\onpremtabledef.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Blobbtabell
Definitionen för tabellen för den blob platsen är i följande (detta mappas inmatade data från en lokal plats till Azure-blob):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiera JSON-definition för tabellen till en fil med namnet *bloboutputtabledef.json* och spara den på en känd plats (här antas vara *C:\temp\bloboutputtabledef.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure-tabell
Definitionen för tabellen för SQL Azure-utdata är i följande (det här schemat mappar data från blob):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiera JSON-definition för tabellen till en fil med namnet *AzureSqlTable.json* och spara den på en känd plats (här antas vara *C:\temp\AzureSqlTable.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Definiera och skapa pipelinen
Ange de aktiviteter som tillhör pipelinen och skapar du pipelinen med följande skriptbaserade procedurer. En JSON-fil används för att definiera egenskaperna för pipeline.

* Skriptet förutsätter att den **pipelinenamn** är *AMLDSProcessPipeline*.
* Observera också att vi anger periodiciteten för pipelinen att köras på daglig basis och använda standard körningstid för jobb (12: 00 UTC).

> [!NOTE]
> Följande procedurer använder Azure PowerShell för att definiera och skapa ADF-pipeline. Men den här uppgiften kan också utföras med hjälp av Azure-portalen. Mer information finns i [skapa pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Använder definitioner för storlekstabellen ovan, anges pipeline-definition för ADF enligt följande:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopiera det här JSON-definition för pipelinen till en fil med namnet *pipelinedef.json* och spara den på en känd plats (här antas vara *C:\temp\pipelinedef.json*). Skapa pipelinen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Starta pipelinen
Nu kan köra pipelinen med hjälp av följande kommando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Den *startdate* och *enddate* parametervärden måste de ersättas med de faktiska datum mellan vilka du vill att köra pipelinen.

När pipelinen körs, bör du kunna se data som visas i behållaren som valts för blob-, en fil per dag.

Observera att vi inte utnyttjade funktionerna i ADF till pipe data inkrementellt. Mer information om hur du gör detta och andra funktioner som tillhandahålls av ADF finns i den [ADF dokumentation](https://azure.microsoft.com/services/data-factory/).
