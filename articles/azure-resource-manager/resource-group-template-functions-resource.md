---
title: Azure Resource Manager-Mallfunktioner - resurser | Microsoft Docs
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att hämta värden om resurser.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: tomfitz
ms.openlocfilehash: 43369131700681de5523043f414129a2e4169f44
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306934"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Resursfunktioner för Azure Resource Manager-mallar

Resource Manager tillhandahåller följande funktioner för att hämta resurs-värden:

* [lista *](#list)
* [Providers](#providers)
* [Referens](#reference)
* [ResourceGroup](#resourcegroup)
* [Resurs-ID](#resourceid)
* [prenumeration](#subscription)

Om du vill hämta värden från parametrar, variabler eller den aktuella distributionen, se [värdet distributionsfunktioner](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>lista

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Syntaxen för den här funktionen varierar beroende på namnet på list åtgärderna. Varje implementering returnerar värden för den resurs typ som har stöd för en List åtgärd. Åtgärds namnet måste börja med `list`. Några vanliga användnings områden `listKeys` är `listSecrets`och. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Unik identifierare för resursen. |
| apiVersion |Ja |sträng |API-versionen av resursen runtime-tillståndet. Normalt i format, **åååå-mm-dd**. |
| functionValues |Nej |objekt | Ett objekt som har värden för funktionen. Endast ger det här objektet för funktioner som stöder tar emot ett objekt med parametervärden, exempelvis **listAccountSas** på ett lagringskonto. Ett exempel på att skicka funktions värden visas i den här artikeln. | 

### <a name="implementations"></a>Implementeringar

Den möjliga användningen av List * visas i följande tabell.

| Resurstyp | Funktionsnamn |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. AppConfiguration/configurationStores | Listnycklar |
| Microsoft. Automation/automationAccounts | [Listnycklar](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listnycklar](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. BatchAI/arbets ytor/experiment/jobb | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/Channels | listChannelWithKeys |
| Microsoft.Cache/redis | [Listnycklar](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [Listnycklar](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/register | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/register | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/register | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/register/kör | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/register/uppgifter | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. container service/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. container service/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. data-och-jobb | listCredentials |
| Microsoft. DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/Accounts/storageAccounts/containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listnycklar](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft. DocumentDB/databaseAccounts | [Listnycklar](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/Domains | [Listnycklar](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/ämnen | [Listnycklar](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listnycklar](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listnycklar](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. LabServices/användare | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/användare | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/arbets flöden/utlösare | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listnycklar](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft. MachineLearningServices/arbets ytor/beräkningar | Listnycklar |
| Microsoft.MachineLearningServices/workspaces | Listnycklar |
| Microsoft. Maps/konton | [Listnycklar](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/Media Services/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/Media Services/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/Media Services/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/Media Services/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listnycklar](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listnycklar](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/arbets ytor | [Listnycklar](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.Relay/namespaces/authorizationRules | [listnycklar](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listnycklar |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listnycklar](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listnycklar](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listnycklar](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listnycklar](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listnycklar](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listnycklar](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listnycklar](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/chefer/Devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/chefer/Devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| Microsoft. Web/Sites/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/relays | [listnycklar](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/lotss/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

För att avgöra vilka resurstyper som har en liståtgärden, har du följande alternativ:

* Visa den [REST API-åtgärder](/rest/api/) för en provider för nätverksresurser och leta efter åtgärder i listan. Till exempel lagringskonton har den [Listnycklar åtgärden](/rest/api/storagerp/storageaccounts).
* Använd cmdleten [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. I följande exempel hämtas alla åtgärder i listan för storage-konton:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Använd följande Azure CLI-kommando för att filtrera endast Liståtgärder:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Returvärde

Det returnerade objektet varierar beroende på vilken List funktion du använder. Till exempel returnerar Listnycklar för ett lagrings konto följande format:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andra listfunktioner har olika returnerade format. Om du vill se formatet för en funktion, inkludera den i outputs-avsnittet som visas i exemplet mallen.

### <a name="remarks"></a>Kommentarer

Ange resurs med samma resurs eller [resourceId funktionen](#resourceid). Använd resurs namnet när du använder en List funktion i samma mall som distribuerar den refererade resursen.

Om du använder en **list** funktion i en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats. Du får ett fel meddelande om **list** funktionen hänvisar till en resurs som inte finns. Använd funktionen **IF** för att se till att funktionen endast utvärderas när resursen distribueras. Se [funktionen IF](resource-group-template-functions-logical.md#if) för en exempel mall som använder IF och list med en villkorligt distribuerad resurs.

### <a name="list-example"></a>Lista exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) visar hur du returnerar de primära och sekundära nycklarna från ett lagringskonto i outputs-avsnittet. Den returnerar också en SAS-token för storage-kontot. 

Om du vill hämta SAS-token skickar du ett objekt för förfallo tiden. Förfallo tiden måste ligga i framtiden. Det här exemplet är avsedd att visa hur du använder funktionerna lista. Normalt du skulle använda SAS-token i ett resursvärde i stället returneras som ett utdatavärde. Utdatavärden lagras i distributionshistoriken och är inte säker.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>Providers

`providers(providerNamespace, [resourceType])`

Returnerar information om en resursprovider och dess resurstyper som stöds. Om du inte anger en resurstyp, returnerar funktionen typerna som stöds för resursprovidern.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |sträng |Namespace av providern |
| ResourceType |Nej |sträng |Typ av resurs i det angivna namnområdet. |

### <a name="return-value"></a>Returvärde

Varje typ som stöds returneras i följande format: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Matris sorteringen av de returnerade värdena är inte garanterad.

### <a name="providers-example"></a>Exempel på leverantörer

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) visar hur du använder funktionen provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

För den **Microsoft.Web** resursprovidern och **platser** resurstyp i föregående exempel returnerar ett objekt i följande format:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>Referens

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Returnerar ett objekt som representerar en resurs runtime-tillståndet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Namn eller unik identifierare för en resurs. När du refererar till en resurs i den aktuella mallen, anger du bara resursnamn som en parameter. Ange resurs-ID när du refererar till en tidigare distribuerad resurs. |
| apiVersion |Nej |sträng |API-versionen av den angivna resursen. Inkludera den här parametern när resursen inte är tillhandahållits i samma mall. Normalt i format, **åååå-mm-dd**. Giltiga API-versioner för din resurs finns i [referens för mallar](/azure/templates/). |
| ”Fullständig” |Nej |sträng |Värde som anger om du vill returnera fullständiga resurs-objekt. Om du inte anger `'Full'`, egenskaper för objekt av resursen returneras. Fullständig objektet innehåller värden som resurs-ID och plats. |

### <a name="return-value"></a>Returvärde

Alla resurstyper returnerar olika egenskaper för funktionen referens. Funktionen returnerar inte ett enda, fördefinierade format. Dessutom varierar det returnerade värdet beroende på om du har angett det fullständiga objektet. Om du vill visa egenskaperna för en resurstyp, returnerar du objektet i outputs-avsnittet som visas i exemplet.

### <a name="remarks"></a>Kommentarer

Funktionen referens hämtar körtiden för en tidigare distribuerad resurs eller en resurs som distribuerats i den aktuella mallen. Den här artikeln visar exempel på båda scenarierna.

Normalt använder du den **referens** funktionen för att returnera ett visst värde från ett objekt, till exempel blob-slutpunkt URI eller fullständigt domännamn.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Använd `'Full'` när du behöver resurs-värden som inte ingår i Egenskaper för schemat. Till exempel om du vill ställa in åtkomstprinciper för nyckelvalvet, få identitetsegenskaperna för en virtuell dator.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Giltig användning

Referens-funktionen kan endast användas i egenskaperna för en resursdefinition och outputs-avsnittet av en mall eller distribution. När det används med [egenskapen iteration](resource-group-create-multiple.md#property-iteration)kan du använda funktionen Reference för `input` eftersom uttrycket har tilldelats till resurs egenskapen. Du kan inte använda den `count` med eftersom antalet måste bestämmas innan referens funktionen har åtgärd ATS.

Du kan inte använda funktionen reference i utdata för en [kapslad mall](resource-group-linked-templates.md#nested-template) för att returnera en resurs som du har distribuerat i den kapslade mallen. Använd i stället en [länkad mall](resource-group-linked-templates.md#external-template-and-external-parameters).

Om du använder funktionen **Reference** i en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats.  Du får ett fel meddelande om **referens** funktionen hänvisar till en resurs som inte finns. Använd funktionen **IF** för att se till att funktionen endast utvärderas när resursen distribueras. Se [funktionen IF](resource-group-template-functions-logical.md#if) för en exempel mall som använder IF och Reference med en villkorligt distribuerad resurs.

### <a name="implicit-dependency"></a>Implicit beroende

Med hjälp av funktionen referens deklarera du implicit att en resurs beror på en annan resurs om refererade resursen har tillhandahållits i samma mall och du referera till resursen med sitt namn (inte resurs-ID). Du behöver inte också använda egenskapen dependsOn. Funktionen utvärderas inte förrän den refererade resursen har slutfört distributionen.

### <a name="resource-name-or-identifier"></a>Resurs namn eller identifierare

Ange namnet på resursen när du refererar till en resurs som har distribuerats i samma mall.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ange resurs-ID när du refererar till en resurs som inte har distribuerats i samma mall.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

För att undvika tvetydighet om vilken resurs du refererar till, kan du ange ett fullständigt resurs namn.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

När du skapar en fullständigt kvalificerad referens till en resurs, är ordningen för att kombinera segment från typ och namn inte bara en sammanfogning av de två. Använd i stället en sekvens av *typnamn/namn* -par från minst för de mest aktuella för namn området:

**{Resource-Provider-namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt``Microsoft.Compute/virtualMachines/extensions/myVM/myExt` stämmer inte korrekt

### <a name="reference-example"></a>Referens exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) distribuerar en resurs och refererar till den här resursen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Föregående exempel returnerar de två objekten. För egenskapsobjektet är i följande format:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Fullständig objektet är i följande format:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) refererar till ett lagringskonto som inte är distribuerat i den här mallen. Lagringskontot finns redan i samma prenumeration.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Returnerar ett objekt som representerar den aktuella resursgruppen. 

### <a name="return-value"></a>Returvärde

Det returnerade objektet är i följande format:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Egenskapen **managedBy** returneras bara för resurs grupper som innehåller resurser som hanteras av en annan tjänst. För hanterade program, Databricks och AKS är egenskapens värde resurs-ID för hanterings resursen.

### <a name="remarks"></a>Kommentarer

Funktionen kan inte användas i en mall som har [distribuerats på prenumerations nivån.](deploy-to-subscription.md) `resourceGroup()` Den kan bara användas i mallar som har distribuerats till en resurs grupp.

Ett vanligt användningsområde för resourceGroup-funktionen är att skapa resurser på samma plats som resursgruppen. I följande exempel använder resursgruppens plats för att tilldela en plats för en webbplats.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

Du kan också använda funktionen resourceGroup för att lägga till taggar från resurs gruppen till en resurs. Mer information finns i [använda taggar från resurs gruppen](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="resource-group-example"></a>Exempel på resurs grupp

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) returnerar egenskaperna för resursgruppen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Föregående exempel returnerar ett objekt i följande format:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Returnerar den unika identifieraren för en resurs. Du använder den här funktionen när resursnamnet är tvetydigt eller ej etablerad inom samma mall. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nej |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. |
| resourceGroupName |Nej |sträng |Standardvärdet är aktuella resursgruppen. Ange det här värdet när du behöver hämta en resurs i en annan resursgrupp. |
| ResourceType |Ja |sträng |Typ av resurs, inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namnet på resursen. |
| resourceName2 |Nej |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Identifieraren returneras i följande format:

**/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}**


### <a name="remarks"></a>Kommentarer

Antalet parametrar som du anger varierar beroende på om resursen är en överordnad eller underordnad resurs och om resursen finns i samma prenumeration eller resurs grupp.

Om du vill hämta resurs-ID för en överordnad resurs i samma prenumeration och resurs grupp anger du resursens typ och namn.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Om du vill hämta resurs-ID för en underordnad resurs, bör du tänka på antalet segment i resurs typen. Ange ett resurs namn för varje segment av resurs typen. Namnet på segmentet motsvarar den resurs som finns för den delen av hierarkin.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Om du vill hämta resurs-ID för en resurs i samma prenumeration, men i en annan resurs grupp, anger du resurs gruppens namn.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Om du vill hämta resurs-ID för en resurs i en annan prenumeration och resurs grupp anger du prenumerations-ID och resurs gruppens namn.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

När den `resourceId()` används med en [distribution på prenumerations nivå](deploy-to-subscription.md)kan funktionen bara hämta ID för resurser som har distribuerats på den nivån. Du kan till exempel hämta ID: t för en princip definition eller roll definition, men inte ID: t för ett lagrings konto. Vid distributioner till en resurs grupp är motsatsen sant. Du kan inte hämta resurs-ID för resurser som har distribuerats på prenumerations nivå.

Om du vill hämta resurs-ID för en resurs på en prenumerations nivå när du distribuerar i prenumerations omfånget använder du:

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Du behöver ofta, Använd den här funktionen när du använder ett lagringskonto eller ett virtuellt nätverk i en annan resursgrupp. I följande exempel visas hur en resurs från en extern resursgrupp enkelt kan användas:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Exempel på resurs-ID

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) returnerar resurs-ID för ett lagringskonto i resursgruppen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| sameRGOutput | Sträng | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Sträng | /subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Sträng | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Sträng | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.SQL/Servers/ServerName/Databases/databaseName |

## <a name="subscription"></a>subscription

`subscription()`

Returnerar information om prenumerationen för den aktuella distributionen. 

### <a name="return-value"></a>Returvärde

Funktionen returnerar följande format:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Exempel på prenumeration

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) visar anropa prenumeration-funktionen i outputs-avsnittet. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill slå samman flera mallar, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

