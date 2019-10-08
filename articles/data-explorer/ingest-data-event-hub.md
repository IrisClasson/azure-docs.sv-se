---
title: Mata in data från Event Hub i Azure Data Explorer
description: I den här artikeln får du lära dig hur du matar in data i Azure Datautforskaren från Event Hub.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: a0568d3c3258fe082fe8451820fe7a25390cfe78
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996783"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Mata in data från Event Hub i Azure Data Explorer

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Azure Data Explorer erbjuder inmatning (datainläsning) från Event Hubs, en dataströmningsplattform och händelseinmatningstjänst för stordata. [Event Hubs](/azure/event-hubs/event-hubs-about) kan bearbeta flera miljoner händelser per sekund i nära realtid. I den här artikeln skapar du en Event Hub, ansluter till den från Azure Datautforskaren och ser data flödet genom systemet.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

* [Ett test kluster och en databas](create-cluster-database-portal.md).

* [En exempelapp](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) som genererar data och skickar dem till en händelsehubb. Ladda ned exempelprogrammet till datorn.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) för att köra exempel appen.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

I den här artikeln skapar du exempel data och skickar dem till en Event Hub. Det första steget är att skapa en händelsehubb. Du kan göra detta med hjälp av en Azure Resource Manager-mall på Azure-portalen.

1. Skapa en händelsehubb genom att använda följande knapp för att starta distributionen. Högerklicka och välj **Öppna i nytt fönster** så att du kan följa resten av stegen i den här artikeln.

    [![Distribuera till Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Knappen **Distribuera till Azure** tar dig till Azure-portalen där du kan fylla i ett distributionsformulär.

    ![Distribuera till Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Välj den prenumeration där du vill skapa händelsehubben och skapa en resursgrupp med namnet *test-hub-rg*.

    ![Skapa en resursgrupp](media/ingest-data-event-hub/create-resource-group.png)

1. Fyll i formuläret med följande information.

    ![Distributionsformulär](media/ingest-data-event-hub/deployment-form.png)

    Använd standardinställningarna för alla inställningar som inte visas i följande tabell.

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Subscription | Din prenumeration | Välj den Azure-prenumeration som ska användas för händelsehubben.|
    | Resource group | *test-hub-rg* | Skapa en ny resursgrupp. |
    | Location | *USA, västra* | Välj *västra USA* för den här artikeln. Välj den region som bäst uppfyller dina behov för ett produktionssystem. Skapa händelsehubbens namnområde på samma plats som klustret Kusto för bästa prestanda (viktigast för händelsehubbnamnområden med högt dataflöde).
    | Namn på namnområde | Ett unikt namnområdesnamn | Välj ett unikt namn som identifierar namnområdet. Till exempel *mytestnamespace*. Domännamnet *servicebus.windows.net* läggs till i namnet som du anger. Namnet får endast innehålla bokstäver, siffror och bindestreck. Namnet måste börja med en bokstav och sluta med en bokstav eller siffra. Värdet måste innehålla mellan 6 och 50 tecken.
    | Namn på händelsehubb | *test-hub* | Händelsehubben finns under namnområdet, som tillhandahåller en unik omfångscontainer. Namnet på händelsehubben måste vara unikt inom namnområdet. |
    | Konsumentgruppens namn | *test-group* | Konsumentgrupper gör att flera konsumerande program kan ha en separat vy över händelseströmmen. |
    | | |

1. Välj **Köp**, vilket bekräftar att du skapar resurser i din prenumeration.

1. Välj **Meddelanden** i verktygsfältet för att övervaka etableringsprocessen. Det kan ta flera minuter innan distributionen slutförs, men du kan gå vidare till nästa steg nu.

    ![Aviseringar](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Skapa en måltabell i Azure Data Explorer

Nu ska du skapa en tabell i Azure Data Explorer som Event Hubs skickar data till. Du skapar tabellen i klustret och databasen som etablerades i avsnittet **Förutsättningar**.

1. På Azure-portalen går du till ditt kluster och väljer sedan **Fråga**.

    ![Frågeprogramlänk](media/ingest-data-event-hub/query-explorer-link.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att skapa den tabell (TestTable) som tar emot insamlade data.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Kör genereringsfråga](media/ingest-data-event-hub/run-create-query.png)

1. Kopiera följande kommando till fönstret och välj **Kör** för att mappa inkommande JSON-data till kolumnnamnen och datatyperna i tabellen (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Ansluta till händelsehubben

Nu ansluter du till händelsehubben från Azure-datautforskaren. När den här anslutningen är på plats strömmas data som flödar till händelsehubben till den testtabell som du skapade tidigare i den här artikeln.

1. Välj **Meddelanden** i verktygsfältet för att kontrollera att distributionen av händelsehubben lyckades.

1. Under klustret som du skapade väljer du **Databaser** och sedan **TestDatabase**.

    ![Välj testdatabas](media/ingest-data-event-hub/select-test-database.png)

1. Välj **Datainmatning** och **Lägg till dataanslutning**. Fyll sedan i formuläret med följande information. Välj **Skapa** när du är klar.

    ![Händelsehubbanslutning](media/ingest-data-event-hub/event-hub-connection.png)

    Datakälla:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Namn på dataanslutning | *test-hub-connection* | Namnet på anslutningen som du vill skapa i Azure Data Explorer.|
    | Namnrymd för händelshubb | Ett unikt namnområdesnamn | Namnet som du valde tidigare, som identifierar ditt namnområde. |
    | Händelsehub | *test-hub* | Händelsehubben som du skapade. |
    | Konsumentgrupp | *test-group* | Konsumentgruppen som definierades i hubben som du skapade. |
    | Egenskaper för händelse system | Välj relevanta egenskaper | [System egenskaper för Event Hub](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Om det finns flera poster per händelse meddelande, läggs system egenskaperna till i den första. När du lägger till system egenskaper [skapar](/azure/kusto/management/tables#create-table) eller [uppdaterar](/azure/kusto/management/tables#alter-table-and-alter-merge-table) du tabell schema och [mappning](/azure/kusto/management/mappings) för att inkludera de valda egenskaperna. |
    | | |

    Måltabell:

    Det finns två alternativ för att dirigera inmatade data: *statiska* och *dynamiska*. 
    I den här artikeln använder du statisk routning, där du anger tabell namn, data format och mappning. Låt därför **My data includes routing info** (Mina data innehåller routningsinformation) vara avmarkerat.

     **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Tabell | *TestTable* | Tabellen som du skapade i **TestDatabase**. |
    | Dataformat | *JSON* | Format som stöds är Avro, CSV, JSON, Multiline JSON, PSV, SOHSV, SCSV, TSV, TSVE och TXT. Komprimerings alternativ som stöds: GZip |
    | Kolumnmappning | *TestMapping* | [Mappningen](/azure/kusto/management/mappings) som du skapade i **TestDatabase**, som mappar inkommande JSON-data till kolumn namn och data typer för **TestTable**. Krävs för JSON, Multiline JSON eller AVRO, och valfritt för andra format.|
    | | |

    > [!NOTE]
    > * Välj **mina data innehåller routningsinformation** för att använda dynamisk routning, där dina data innehåller den routningsinformation som krävs som visas i exempel kommentaren för [appen](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Om både statiska och dynamiska egenskaper anges åsidosätter de dynamiska egenskaperna de statiska. 
    > * Endast händelser som står i kö efter att du har skapat data anslutningen matas in.

## <a name="copy-the-connection-string"></a>Kopiera anslutningssträngen

När du kör den [exempelapp](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) som anges i Nödvändiga komponenter behöver du anslutningssträngen för händelsehubbens namnrymd.

1. Under händelsehubbens namnområde som du skapade väljer du **Policyer för delad åtkomst** och sedan **RootManageSharedAccessKey**.

    ![Principer för delad åtkomst](media/ingest-data-event-hub/shared-access-policies.png)

1. Kopiera **Anslutningssträng – primär nyckel**. Du klistrar in den i nästa avsnitt.

    ![Anslutningssträng](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generera exempeldata

Använd den [exempelapp](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) som du laddade ned för att generera data.

1. Öppna exempelapplösningen i Visual Studio.

1. I filen *program.cs* uppdaterar du konstanten `connectionString` till anslutningssträngen som du kopierade från händelsehubbens namnområde.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Skapa och kör appen. Appen skickar meddelanden till händelsehubben, och den skriver ut status var tionde sekund.

1. När appen har skickat några meddelanden går du vidare till nästa steg där du ska granska dataflödet till händelsehubben och testtabellen.

## <a name="review-the-data-flow"></a>Granska dataflödet

Med appen som genererar data kan du nu se flödet av dessa data från händelsehubben till tabellen i ditt kluster.

1. Under händelsehubben på Azure-portalen ser du en aktivitetstopp när appen körs.

    ![Graf för händelsehubb](media/ingest-data-event-hub/event-hub-graph.png)

1. För att kontrollera hur många meddelanden som nått databasen hittills kör du följande fråga i testdatabasen.

    ```Kusto
    TestTable
    | count
    ```

1. Kör följande fråga för att se innehållet i meddelandena:

    ```Kusto
    TestTable
    ```

    Resultatuppsättningen bör se ut så här:

    ![Meddelanderesultat](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Azure Data Explorer har en sammansättningsprincip (batchbearbetning) för datainmatning som är utformad för att optimera inmatningsprocessen. Principen är konfigurerad till 5 minuter eller 500 MB data som standard, så att du kan uppleva en fördröjning. Se [batch-principen](/azure/kusto/concepts/batchingpolicy) för sammansättnings alternativ. 
    > * Inmatningen av Event Hub inkluderar svars tiden för Event Hub på 10 sekunder eller 1 MB. 
    > * Konfigurera din tabell så att den stöder strömning och ta bort fördröjningen i svars tiden. Se [strömmande princip](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda händelsehubben igen rensar du **test-hub-rg** för att undvika kostnader.

1. Välj **Resursgrupper** i Azure Portal längst till vänster och välj sedan den resursgrupp du skapat.  

    Om den vänstra menyn är dold väljer du ![Knappen Expandera](media/ingest-data-event-hub/expand.png) för att expandera den.

   ![Välj den resursgrupp som ska tas bort](media/ingest-data-event-hub/delete-resources-select.png)

1. Under **test-resource-group** väljer du **Ta bort resursgrupp**.

1. I det nya fönstret skriver du namnet på resursgruppen som ska tas bort (*test-hub-rg*) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren](web-query-data.md)
