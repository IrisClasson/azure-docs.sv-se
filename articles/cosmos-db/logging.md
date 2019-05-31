---
title: Diagnostisk loggning i Azure Cosmos DB
description: Läs mer om olika sätt att logga och övervaka data som lagras i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 67a6eec938a4a18455e4063925e21e26fe362f76
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243477"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Diagnostisk loggning i Azure Cosmos DB 

När du börjar använda en eller flera Azure Cosmos DB-databaser, kanske du vill övervaka hur och när dina databaser används. Den här artikeln innehåller en översikt över de loggar som är tillgängliga på Azure-plattformen. Du lär dig hur du aktiverar diagnostikloggning för att skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), så strömma loggar till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), och hur du exporterar loggar till [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Loggar som är tillgängliga i Azure

Innan vi pratar om hur du övervakar dina Azure Cosmos DB-konto, ska vi tydliggöra några saker om loggning och övervakning. Det finns olika typer av loggar på Azure-plattformen. Det finns [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Azure diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure-mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), händelser, övervakning av pulsslag, åtgärdsloggar och så vidare. Det finns en mängd olika loggar. Du kan se den fullständiga listan med loggar i [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) i Azure-portalen. 

Följande bild visar de olika slags Azure loggar som är tillgängliga:

![Olika typer av Azure-loggar](./media/logging/azurelogging.png)

I bild, den **beräkningsresurser** representerar Azure-resurser som du kan komma åt Microsoft gäst-OS. Till exempel Azure-datorer, VM-skalningsuppsättningar, Azure Container Service, och så vidare är räknas som beräkningsresurser. Compute-resurser genererar aktivitetsloggar, diagnostikloggar och programloggar. Mer information finns i [källor för övervakningsdata i Azure](../azure-monitor/platform/data-sources.md) artikeln.

Den **icke-beräkningsresurser** finns resurser som du inte kan komma åt det underliggande Operativsystemet och arbeta direkt med resursen. Nätverkssäkerhetsgrupper, Logic Apps och så vidare. Azure Cosmos DB är en icke-compute-resurs. Du kan visa loggar för icke-beräkningsresurser i aktivitetsloggen eller aktiverar diagnostikloggar i portalen. Mer information finns i [datakällor i Azure Monitor](../azure-monitor/platform/data-sources.md) artikeln.

Aktivitetsloggen registrerar åtgärderna på prenumerationsnivå för Azure Cosmos DB. Åtgärder som Listnycklar och skriva DatabaseAccounts loggas. Diagnostikloggar ger mer detaljerad loggning och kan du logga DataPlaneRequests (skapa, läsa, fråga och så vidare) och MongoRequests.


I den här artikeln ska fokusera vi på de Azure-aktivitetsloggen, diagnostikloggar för Azure och Azure-mått. Vad är skillnaden mellan dessa tre loggar? 

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen

Azure-aktivitetsloggen är en prenumerationslogg som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Aktivitetsloggen rapporterar kontrollplanet händelser för dina prenumerationer under den administrativa kategorin. Du kan använda aktivitetsloggen för att fastställa den ”vad, vem, och när” för någon skriva åtgärden (PUT, POST, ta bort) för de resurser i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. 

Aktivitetsloggen skiljer sig från diagnostikloggar. Aktivitetsloggen tillhandahåller data om åtgärder på en resurs från utsidan (den _kontrollplanet_). I Azure Cosmos DB-kontexten kontrollplanet åtgärder är bland annat skapa behållare, lista över nycklar, ta bort nycklar, lista databas och så vidare. Diagnostikloggar som genereras av en resurs och ange information om användningen av den här resursen (den _dataplanet_). Några exempel på kontrollplansåtgärder data i diagnostikloggen är Delete, Insert och ReadFeed.

Aktivitetsloggar (kontrollplanåtgärder) kan vara bredare sin natur och kan innehålla den fullständiga e-postadressen anroparen, anropares IP-adress, resursnamn, åtgärdens namn, TenantId och mer. Aktivitetsloggen innehåller flera [kategorier](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) av data. Fullständig information om scheman av dessa kategorier finns i [Azure-aktivitetsloggen Händelseschema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnostikloggar kan dock vara begränsande sin natur som personliga data ofta tas bort från de här loggarna. Du kan ha IP-adressen för anroparen, men den senaste octant tas bort.

### <a name="azure-metrics"></a>Azure-mått

[Azure-mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) har viktigaste Azure telemetridata-typ (kallas även _prestandaräknare_) som genereras av de flesta Azure-resurser. Mått kan du visa information om dataflöde, lagring, konsekvens, tillgänglighet och svarstid för dina Azure Cosmos DB-resurser. Mer information finns i [övervakning och felsöka med mått i Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Azure diagnostikloggar genereras av en resurs och tillhandahåller omfattande, frekventa data om användningen av den här resursen. Innehållet i de här loggarna varierar efter resurstyp. Resursnivå diagnostikloggar skiljer sig också från-nivån för gästoperativsystemet diagnostikloggar. Gäst-OS diagnostiska loggar samlas in av en agent som körs i en virtuell dator eller andra stöds resurstyp. Resursnivå diagnostikloggar kräver ingen agent och avbilda resurs-specifika data från själva Azure-plattformen. Diagnostikloggar för gäst-OS-nivå samla in data från operativsystemet och programmen som körs på en virtuell dator.

![Diagnostisk loggning till lagring, Event Hubs eller Azure Monitor-loggar](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Vad loggas av Azure-diagnostikloggar?

* Alla autentiserade backend-förfrågningar (TCP/REST) i alla API: er är inloggad, inklusive misslyckade begäranden på grund av åtkomstbehörigheter, systemfel eller ogiltiga förfrågningar. Stöd för användarinitierad Graph, Cassandra och tabell-API-begäranden är inte tillgänglig för tillfället.
* Åtgärder i databasen, bland annat CRUD-åtgärder på alla dokument, behållare och databaser.
* Åtgärder på nycklar, bland annat skapa, ändra eller ta bort nycklar.
* Oautentiserade förfrågningar som resulterar i ett 401-svar. Till exempel förfrågningar som inte har någon ägartoken, eller har fel format eller har upphört att gälla eller har en ogiltig token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Aktivera loggning i Azure portal

Om du vill aktivera diagnostikloggning, måste du ha följande resurser:

* En befintlig Azure Cosmos DB-konto, databas och behållare. Anvisningar om hur du skapar dessa resurser finns i [skapa ett databaskonto med hjälp av Azure portal](create-sql-api-dotnet.md#create-account), [Azure CLI-exempel](cli-samples.md), eller [PowerShell-exempel](powershell-samples.md).

Om du vill aktivera Diagnostisk loggning i Azure-portalen, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com)i din Azure Cosmos DB-konto, markera **diagnostikloggar** i det vänstra navigeringsfönstret och välj sedan **slå på diagnostik**.

    ![Aktivera Diagnostisk loggning för Azure Cosmos DB i Azure portal](./media/logging/turn-on-portal-logging.png)

2. I den **diagnostikinställningar** gör du följande steg: 

    * **Namn på**: Ange ett namn att skapa loggarna.

    * **Arkivera till ett lagringskonto**: Om du vill använda det här alternativet om behöver du ett befintligt lagringskonto för att ansluta till. Om du vill skapa ett nytt lagringskonto i portal, [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md) och följ anvisningarna för att skapa en Azure Resource Manager, Allmänt konto. Gå sedan tillbaka till den här sidan i portalen för att välja ett lagringskonto. Det kan ta några minuter innan nyligen skapade lagringskonton ska visas i den nedrullningsbara menyn.
    * **Stream till en händelsehubb**: Om du vill använda det här alternativet behöver du en befintlig Event Hubs-namnområde och event hub att ansluta till. Om du vill skapa ett namnområde för Event Hubs [skapa ett Event Hubs-namnområde och en event hub med hjälp av Azure portal](../event-hubs/event-hubs-create.md). Återvänd sedan till den här sidan i portalen för att välja Event Hubs-namnområde och principen.
    * **Skicka till Log Analytics**: Om du vill använda det här alternativet måste använda en befintlig arbetsyta eller skapa en ny Log Analytics-arbetsyta genom att följa stegen för att [skapa en ny arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) i portalen. Mer information om hur du visar dina loggar i Azure Monitor-loggar finns i vyn loggar i Azure Monitor-loggar.
    * **Logga DataPlaneRequests**: Välj det här alternativet för att logga backend-begäranden från Azure Cosmos DB underliggande distribuerad plattform för SQL, diagram, MongoDB, Cassandra och tabell-API-konton. Om du arkivering till ett lagringskonto, kan du välja kvarhållningsperioden för diagnostiska loggar. Loggarna är automatiskt bort efter kvarhållningsperioden har gått ut.
    * **Logga MongoRequests**: Välj det här alternativet för att logga användarinitierad begäranden från klientdelen för Azure Cosmos DB för att betjäna Cosmos-konton som konfigurerats med Azure Cosmos DB API för MongoDB. Om du arkivering till ett lagringskonto, kan du välja kvarhållningsperioden för diagnostiska loggar. Loggarna är automatiskt bort efter kvarhållningsperioden har gått ut.
    * **Metrisk begäranden**: Välj det här alternativet för att lagra utförliga data i [Azure-mått](../azure-monitor/platform/metrics-supported.md). Om du arkivering till ett lagringskonto, kan du välja kvarhållningsperioden för diagnostiska loggar. Loggarna är automatiskt bort efter kvarhållningsperioden har gått ut.

3. Välj **Spara**.

    Om du får ett meddelande om att ”det gick inte att uppdatera diagnostik för \<Arbetsytenamn >. Prenumerationen \<prenumerations-id > har inte registrerats för användning av microsoft.insights ”, följa den [felsöka Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) anvisningar för att registrera kontot och försök sedan utföra den här proceduren.

    Om du vill ändra hur dina diagnostikloggar sparas när som helst i framtiden kan du gå tillbaka till den här sidan om du vill ändra inställningarna för diagnostiklogg för ditt konto.

## <a name="turn-on-logging-by-using-azure-cli"></a>Aktivera loggning med hjälp av Azure CLI

Om du vill aktivera mått och diagnostikloggning med hjälp av Azure CLI, använder du följande kommandon:

- Använd följande kommando om du vill aktivera lagring av diagnostiska loggar i ett lagringskonto:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   Den `resource` är namnet på Azure Cosmos DB-kontot. Resursen är i formatet ”/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/ < Azure_Cosmos_account_name >” den `storage-account` är namnet på lagringskontot som du Om du vill skicka loggarna. Du kan logga andra loggar genom att uppdatera kategorin parametervärden till ”MongoRequests” eller ”DataPlaneRequests”. 

- Om du vill aktivera strömning av diagnostiska loggar till en händelsehubb, Använd följande kommando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   Den `resource` är namnet på Azure Cosmos DB-kontot. Den `event-hub-rule` är den event hub-regel-ID 

- Använd följande kommando om du vill aktivera skicka diagnostikloggar till Log Analytics-arbetsyta:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Du kan kombinera dessa parametrar om du vill aktivera flera Utdataalternativ för.

## <a name="turn-on-logging-by-using-powershell"></a>Aktivera loggning med hjälp av PowerShell

Om du vill aktivera Diagnostisk loggning med hjälp av PowerShell behöver du ha Azure Powershell med lägst version 1.0.1.

Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Om du redan har installerat Azure PowerShell och inte vet vilken version från PowerShell-konsolen typen `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```powershell
Connect-AzAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och som standard använder det första.

Om du har mer än en prenumeration kan behöva du ange specifika prenumerationen som används för att skapa din Azure-nyckelvalv. Om du vill visa prenumerationerna för ditt konto, skriver du följande kommando:

```powershell
Get-AzSubscription
```

Om du vill ange den prenumeration som är associerat med Azure Cosmos DB-kontot som du inloggad, skriver du följande kommando:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Om du har mer än en prenumeration som är associerat med ditt konto är det viktigt att ange den prenumeration som du vill använda.
>
>

Mer information om hur du konfigurerar Azure PowerShell finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Skapa ett nytt lagringskonto för dina loggar
Men du kan använda ett befintligt lagringskonto för dina loggar i den här självstudien, skapa vi ett nytt lagringskonto som är dedikerad för Azure Cosmos DB-loggar. För enkelhetens skull Vi lagrar uppgifterna för lagringskontot i variabeln **sa**.

För att ytterligare underlätta hantering i den här självstudien använder vi samma resursgrupp som det som innehåller Azure Cosmos DB-databasen. Ersätt värdena för den **ContosoResourceGroup**, **contosocosmosdblogs**, och **norra centrala USA** parametrar, i tillämpliga fall:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Om du vill använda ett befintligt lagringskonto måste kontot använda samma prenumeration som din Azure Cosmos DB-prenumeration. Kontot måste också använda Resource Manager-distributionsmodellen i stället för den klassiska distributionsmodellen.
>
>

### <a id="identify"></a>Identifiera Azure Cosmos DB-kontot för dina loggar
Ange namnet på Azure Cosmos DB-kontot till en variabel med namnet **konto**, där **ResourceName** är namnet på Azure Cosmos DB-kontot.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Aktivera loggning
Om du vill aktivera loggning för Azure Cosmos DB kan använda den `Set-AzDiagnosticSetting` cmdlet med variabler för det nya lagringskontot, Azure Cosmos DB-konto och kategori för att aktivera för loggning. Kör följande kommando och ange den **-aktiverad** flaggan till **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Utdata för kommandot bör likna följande exempel:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Utdata från kommandot bekräftar att loggning är aktiverat för databasen och informationen sparas i ditt storage-konto.

Du kan också kan du också ange bevarandeprincipen för dina loggar så att äldre loggar tas bort automatiskt. Till exempel ange bevarandeprincip med den **- RetentionEnabled** -flaggan inställd på **$true**. Ange den **- RetentionInDays** parameter **90** så att loggar som är äldre än 90 dagar tas bort automatiskt.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Komma åt loggarna
Azure Cosmos DB-loggarna för den **DataPlaneRequests** kategori lagras i den **insights-logs-data-plan-begäranden** behållare i lagringskontot som du angav. 

Börja med att skapa en variabel för containerns namn. Variabeln används i hela genomgången.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Om du vill visa alla blobar i den här behållaren skriver du:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Utdata för kommandot bör likna följande exempel:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Som du kan se dessa utdata följer blobbarna en namngivningskonvention: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Datum- och tidsvärdena använder UTC.

Eftersom samma lagringskonto kan användas för att samla in loggar för flera resurser, kan du använda fullständigt kvalificerade resurs-ID i blobnamnet att komma åt och ladda ned de specifika blobbar som du behöver. Innan vi gör det beskriver vi hur du laddar ned alla blobar.

Börja med att skapa en mapp som du vill ladda ned blobbarna till. Exempel:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Sedan kan hämta en lista över alla blobar:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Skicka den här listan via den `Get-AzStorageBlobContent` kommando för att ladda ned blobbarna till målmappen:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

När du kör den här andra kommandot skapar den **/** avgränsaren i blobbnamnen skapar en fullständig mappstruktur under målmappen. Den här mappstrukturen används för att ladda ned och spara blobbarna som filer.

Om du vill ladda ned blobbarna selektivt använder du jokertecken. Exempel:

* Om du har flera databaser och vill hämta loggar för en databas med namnet **CONTOSOCOSMOSDB3**, Använd kommandot:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Om du har flera resursgrupper och vill hämta loggar för en resursgrupp kan använda kommandot `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Om du vill hämta alla loggar för månaden för juli 2017 kan använda kommandot `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Du kan också köra följande kommandon:

* Använd kommandot för att fråga efter statusen för nyckelvalvsresursens diagnostikinställningar för `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Du vill inaktivera loggning för den **DataPlaneRequests** kategori för kontot, Använd kommandot `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


BLOB-objekt som returneras i var och en av de här frågorna lagras som text och formaterade som en JSON-blob som visas i följande kod:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Läs om data i varje JSON-blob i [tolka Azure Cosmos DB-loggarna](#interpret).

## <a name="manage-your-logs"></a>Hantera dina loggar

Diagnostikloggar görs tillgängliga i ditt konto för två timmar från den tidpunkt då Azure Cosmos DB-åtgärden gjordes. Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standard Azure åtkomstkontroll metoder för att skydda loggarna och begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.
* Kvarhållningsperioden för begäranden av plan som arkiveras på ett lagringskonto har konfigurerats i portalen när den **Log DataPlaneRequests** har valts. Om du vill ändra den här inställningen, se [aktiverar loggning i Azure-portalen](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Visa loggar i Azure Monitor-loggar

Om du har valt den **skicka till Log Analytics** när du har aktiverat Diagnostisk loggning diagnostiska data från din behållare vidarebefordras till Azure Monitor-loggar inom två timmar. När du tittar på Azure Monitor-loggar omedelbart efter att du aktiverar loggning, visas inte några data. Bara vänta två timmar och försök igen. 

Innan du visa loggarna och se om Log Analytics-arbetsytan har uppgraderats för att använda det nya Kusto-frågespråket. Du kan kontrollera genom att öppna den [Azure-portalen](https://portal.azure.com)väljer **Log Analytics-arbetsytor** längst till vänster, välj sedan namnet på arbetsytan som du ser i nästa bild. Den **Log Analytics-arbetsyta** visas:

![Azure Monitor-loggar i Azure portal](./media/logging/azure-portal.png)

>[!NOTE]
>OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.  

Om du ser följande meddelande på den **Log Analytics-arbetsyta** sidan din arbetsyta inte har uppgraderats för att använda det nya språket. Läs mer om hur du uppgraderar till det nya frågespråket [uppgradera Azure Log Analytics-arbetsytan till ny loggsökning](../log-analytics/log-analytics-log-search-upgrade.md). 

![Azure Monitor-loggar uppgradera meddelande](./media/logging/upgrade-notification.png)

Om du vill visa dina diagnostiska data i Azure Monitor-loggar, öppna den **Loggsökning** sidan menyn till vänster eller **Management** området på sidan som visas i följande bild:

![Loggalternativ för sökning i Azure portal](./media/logging/log-analytics-open-log-search.png)

Nu när du har aktiverat insamling av data, kör du följande loggsökning genom att använda det nya frågespråket kan se de 10 senaste loggarna `AzureDiagnostics | take 10`.

![Exemplet loggsökning för de 10 senaste loggarna](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Frågor

Här följer några ytterligare frågor som du kan ange i den **loggsökning** rutan för att övervaka dina Azure Cosmos DB-behållare. Dessa frågor fungerar med den [nytt språk](../log-analytics/log-analytics-log-search-upgrade.md). 

Läs om betydelsen av de data som returneras av varje loggsökning i [tolka Azure Cosmos DB-loggarna](#interpret).

* Fråga efter alla diagnostiska loggar från Azure Cosmos DB för en angiven tidsperiod:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Om du vill fråga efter 10 mest nyligen loggade händelser:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Fråga efter alla åtgärder, grupperade efter åtgärdstyp:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Att fråga efter alla åtgärder, grupperade efter **Resource**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Fråga efter alla användaraktivitet, grupperade efter resurs:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Det här kommandot är för en aktivitetslogg, inte en diagnostiklogg.

* Fråga som åtgärder tar längre tid än 3 millisekunder:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Fråga för vilken agent kör åtgärder:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Fråga efter när de långvariga åtgärderna utfördes:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Mer information om hur du använder det nya Log Search-språket finns i [förstå loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Tolka dina loggar

Diagnostiska data som lagras i Azure Storage och Azure Monitor-loggar använder ett liknande schema. 

I följande tabell beskrivs innehållet i varje loggpost.

| Azure Storage-fält eller någon egenskap | Azure Monitor loggar egenskapen | Beskrivning |
| --- | --- | --- |
| **tid** | **TimeGenerated** | Datum och tid (UTC) när åtgärden utfördes. |
| **Resurs-ID** | **Resurs** | Azure Cosmos DB-kontot som loggar är aktiverad.|
| **Kategori** | **Kategori** | För Azure Cosmos DB-loggar är **DataPlaneRequests** är endast tillgängligt värde. |
| **OperationName** | **OperationName** | Åtgärdens namn. Det här värdet kan vara något av följande åtgärder: Skapa, uppdatera, läsa, ReadFeed, ta bort, ersätta, köra SQL-fråga, fråga, JSQuery, Head, HeadFeed eller Upsert.   |
| **Egenskaper** | Saknas | Innehållet i det här fältet beskrivs i de rader som följer. |
| **Aktivitets-ID** | **activityId_g** | Unikt GUID för den loggade åtgärden. |
| **UserAgent** | **userAgent_s** | En sträng som anger klientanvändaragent som utför förfrågan. Formatet är {användarnamn för agenten} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Typ av resurs som används. Det här värdet kan vara något av följande resurstyper: Databasen, behållare, dokument, bifogad fil, användare, behörighet, StoredProcedure, utlösare, UserDefinedFunction eller erbjudandet. |
| **statusCode** | **statusCode_s** | Svarsstatus för åtgärden. |
| **requestResourceId** | **Resurs-ID** | ResourceId som gäller för begäran. Värdet kan peka databaseRid, collectionRid eller documentRid beroende på de åtgärder som utförs.|
| **clientIpAddress** | **clientIpAddress_s** | Klientens IP-adress. |
| **requestCharge** | **requestCharge_s** | Antalet enheter för programbegäran som används av åtgärden |
| **collectionRid** | **collectionId_s** | Unikt ID för samlingen.|
| **Varaktighet** | **duration_s** | Varaktighet för åtgärden, i ticken. |
| **requestLength** | **requestLength_s** | Längden på begäran, i byte. |
| **responseLength** | **responseLength_s** | Längden på svaret, i byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Det här värdet är inte tom när [resurstokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) används för autentisering. Värdet som pekar på resurs-ID för användaren. |

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig mer om att aktivera loggning och mått och loggfiler kategorier som stöds av olika Azure-tjänster kan läsa både den [översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) och [översikt över Azure diagnostikloggar ](../azure-monitor/platform/diagnostic-logs-overview.md) artiklar.
- Läs de här artiklarna om du vill veta mer om event hubs:
   - [Vad är Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Läs [hämta mått och diagnostikloggar från Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Läs [förstå loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-search-new.md).
