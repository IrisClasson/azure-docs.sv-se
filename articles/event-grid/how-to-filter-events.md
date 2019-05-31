---
title: Så här filtrerar du händelser för Azure Event Grid
description: Visar hur du skapar Azure Event Grid-prenumerationer som Filtrera händelser.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: 5bb95b80e12c818641e2be2b929cdfd01f8f5b5c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304233"
---
# <a name="filter-events-for-event-grid"></a>Filtrera händelser för Event Grid

Den här artikeln visar hur du filtrerar händelser när du skapar en Event Grid-prenumeration. Läs mer om alternativen för händelsefiltrering, i [förstå händelse filtrering för Event Grid-prenumerationer](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtrera efter händelsetyp

När du skapar en Event Grid-prenumeration kan du ange vilka [händelsetyper](event-schema.md) att skicka till slutpunkten. Exemplen i det här avsnittet Skapa prenumerationer på händelser för en resursgrupp, men begränsar de händelser som skickas till `Microsoft.Resources.ResourceWriteFailure` och `Microsoft.Resources.ResourceWriteSuccess`. Om du behöver mer flexibilitet när du filtrerar händelser efter händelsetyper kan se filtrera efter avancerade operatorer och fält.

Du använder PowerShell använder den `-IncludedEventType` parameter när du skapar prenumerationen.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Azure CLI, använder de `--included-event-types` parametern. I följande exempel används Azure CLI i Bash-gränssnittet:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

En Resource Manager-mall, använda den `includedEventTypes` egenskapen.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrera efter ämne

Du kan filtrera händelserna efter ämne i informationen om händelsen. Du kan ange ett värde att matcha för början eller slutet av ämne. Om du behöver mer flexibilitet när du filtrerar händelser efter ämne, läser du filtrera efter avancerade operatorer och fält.

I följande PowerShell-exempel skapar du en händelseprenumeration som filtrerar genom att i början av ämne. Du använder den `-SubjectBeginsWith` parametern för att begränsa händelser till de som för en specifik resurs. Du skickar resurs-ID för en grupp.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

I nästa PowerShell-exempel skapas en prenumeration för ett blob storage. Det begränsar händelser till sådana med ett ämne som slutar med `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

I följande exempel för Azure CLI skapar du en händelseprenumeration som filtrerar genom att i början av ämne. Du använder den `--subject-begins-with` parametern för att begränsa händelser till de som för en specifik resurs. Du skickar resurs-ID för en grupp.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

I nästa Azure CLI-exempel skapas en prenumeration för ett blob storage. Det begränsar händelser till sådana med ett ämne som slutar med `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

I följande exempelmall för Resource Manager kan du skapa en händelseprenumeration som filtrerar genom att i början av ämne. Du använder den `subjectBeginsWith` egenskapen att begränsa händelser till de som för en specifik resurs. Du skickar resurs-ID för en grupp.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

I nästa exempel för Resource Manager-mallen skapar en prenumeration för ett blob storage. Det begränsar händelser till sådana med ett ämne som slutar med `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrera efter operatorer och data

Du kan använda operatorer och egenskaper för data att filtrera händelser för mer flexibilitet vid filtrering.

### <a name="subscribe-with-advanced-filters"></a>Prenumerera med avancerade filter

Läs om operatörer och nycklar som du kan använda för avancerade filter i [avancerad filtrering](event-filtering.md#advanced-filtering).

Följande exempel skapar ett anpassat ämne. De prenumerera på anpassat ämne och filtrera efter ett värde i dataobjektet. Händelser som har egenskapen färg har angetts till blått, rött eller grönt skickas till prenumerationen.

Om du använder Azure CLI använder du:

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Observera att ett [förfallodatum](concepts.md#event-subscription-expiration) har angetts för prenumerationen.

Om du använder PowerShell använder du:

```azurepowershell-interactive
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Test-filter

Testa filtret genom att skicka en händelse med färgfältet inställd grönt. Eftersom grön är ett av värdena i filtret, händelsen skickas till slutpunkten.

Om du använder Azure CLI använder du:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Testa ett scenario där händelsen inte skickas genom att skicka en händelse med färgfältet inställd gult. Gul inte något av värdena som anges i prenumerationen, så att händelsen inte levereras till din prenumeration.

Om du använder Azure CLI använder du:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Om du använder PowerShell använder du:

```azurepowershell-interactive
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelse leveranser finns i [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Läs mer om autentiseringsnyckeln [Event Grid säkerhet och autentisering](security-authentication.md).
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
