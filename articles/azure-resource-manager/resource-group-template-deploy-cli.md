---
title: Distribuera resurser med Azure CLI och en mall | Microsoft Docs
description: Använda Azure Resource Manager och Azure CLI för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 6cccae343e0a06af88c2e996c37910de72138c60
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475053"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuera resurser med Resource Manager-mallar och Azure CLI

Den här artikeln förklarar hur du använder Azure CLI med Resource Manager-mallar för att distribuera dina resurser till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar finns i [översikt över Azure Resource Manager](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Om du inte har Azure CLI installerat kan du använda den [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Distributionsomfattningen

Du kan begränsa distributionen till en Azure-prenumeration eller resursgrupp inom en prenumeration. I de flesta fall kommer du rikta distribution till en resursgrupp. Använda prenumerationsdistributioner för att tillämpa principer och rolltilldelningar i prenumerationen. Du kan också använda prenumerationsdistributioner för att skapa en resursgrupp och distribuera resurser till den. Beroende på omfattningen av distributionen, kan du använda olika kommandon.

Distribuera till en **resursgrupp**, använda [az group deployment skapa](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Distribuera till en **prenumeration**, använda [az distributionen skapa](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Distributioner av hanteringsgrupper stöds för närvarande endast via REST API. Se [distribuera resurser med Resource Manager-mallar och Resource Manager REST API](resource-group-template-deploy-rest.md).

I exemplen i den här artikeln används distribution av resursgrupper. Mer information om prenumerationsdistributioner finns i [skapa resursgrupper och resurser på prenumerationsnivå](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Distribuera lokala mall

När du distribuerar resurser till Azure måste du:

1. Logga in på ditt Azure-konto
2. Skapa en resursgrupp som fungerar som behållare för distribuerade resurser. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte avslutas med en punkt.
3. Distribuera till resursgrupp mallen som definierar resurser för att skapa

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Du kan till exempel ange värden som är skräddarsydda för en viss miljö (till exempel utveckling, testning och produktion). Exempelmallen definierar en parameter för SKU för lagringskontot. 

I följande exempel skapas en resursgrupp och distribuerar en mall från den lokala datorn:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När den är klar visas ett meddelande som innehåller resultatet:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuera fjärråtkomst mall

Istället för att lagra Resource Manager-mallar på den lokala datorn, kanske du föredrar att lagra dem i en extern plats. Du kan lagra mallar i ett källkontrollscentrallager (till exempel GitHub). Eller du kan lagra dem i ett Azure storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en mall för externa, använda den **mall-uri** parametern. Använd URI: N i det här exemplet för att distribuera exempelmallen från GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

I föregående exempel kräver en offentligt tillgänglig URI för den mall som passar de flesta fall eftersom mallen inte ska innehålla känsliga data. Om du vill ange känsliga data (till exempel ett administratörslösenord) kan du skicka det värdet som en säker parameter. Om du inte vill att mallen för att vara allmänt tillgänglig, kan du dock skydda den genom att lagra det i en privat lagringsbehållare. Information om hur du distribuerar en mall som kräver en signatur (SAS) token för delad åtkomst finns i [distribuera privat mall med SAS-token](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

I Cloud Shell använder du följande kommandon:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *återställning vid fel*. När en distribution misslyckas, kan du automatiskt distribuera om en tidigare lyckad distribution från distributionshistoriken. Om du vill ange omdistribution, använda den `--rollback-on-error` parameter i distributionskommandot. Den här funktionen är användbart om du har ett fungerande tillstånd för din distribution av infrastruktur och vill återgå till det här tillståndet. Det finns ett antal varningar och begränsningar:

- Omdistributionen körs exakt som den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Föregående distributionen körs med hjälp av den [fullständig läge](./deployment-modes.md#complete-mode). Alla resurser som inte ingår i den föregående distributionen tas bort och alla resurskonfigurationer ställs till sina tidigare tillstånd. Kontrollera att du förstår de [distributionslägen](./deployment-modes.md).
- Omdistributionen påverkar endast resurserna, dataändringar påverkas inte.
- Den här funktionen stöds bara på resursgrupp distributioner, inte prenumerationen på distributioner. Mer information om prenumerationen på distribution finns i [skapa resursgrupper och resurser på prenumerationsnivå](./deploy-to-subscription.md).

Om du vill använda det här alternativet för måste dina distributioner ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn, kan den aktuella misslyckad distributionen skriva över tidigare distributionen i historiken. Du kan bara använda det här alternativet med rot på distributioner. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill distribuera om den senaste distributionen, lägger du till den `--rollback-on-error` parametern som en flagga.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Om du vill distribuera om en specifik distribution, använda den `--rollback-on-error` parametern och ange namnet på distributionen.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

Den angivna distributionen måste ha lyckats.

## <a name="parameters"></a>Parametrar

Du kan använda infogade parametrar eller en parameterfil för att ange parametervärden. I föregående exempel i den här artikeln visas infogade parametrar.

### <a name="inline-parameters"></a>Infogad parametrar

Om du vill skicka infogade parametrar, anger du värden i `parameters`. Till exempel är ett Bash-gränssnitt för att skicka en sträng och en matris till en mall, använda:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Du kan också hämta innehållet i filen och ge innehållet som en infogad-parameter.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Hämta ett parametervärde från en fil är användbart när du behöver ange konfigurationsvärden. Du kan till exempel ange [cloud-init värden för en Linux-dator](../virtual-machines/linux/using-cloud-init.md).

ArrayContent.json formatet är:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Parameterfiler

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara enklare att använda en JSON-fil som innehåller parametervärdena. Parameterfilen måste vara en lokal fil. Externa parameterfiler stöds inte med Azure CLI.

Parameterfilen måste vara i följande format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observera att parameters-avsnittet innehåller ett parameternamn som matchar den parameter som definierats i mallen (storageAccountType). Parameterfilen innehåller ett värde för parametern. Det här värdet skickas automatiskt till mallen under distributionen. Du kan skapa fler än en parameterfil och sedan skicka in lämpliga parameterfilen för scenariot. 

Kopiera i föregående exempel och spara det som en fil med namnet `storage.parameters.json`.

Om du vill skicka en lokal parameterfil använda `@` att ange en lokal fil med namnet storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Parametern prioritet

Du kan använda infogade parametrar och en lokal parameterfil på samma gång för distribution. Du kan till exempel ange vissa värden i den lokala parameterfilen och lägga till andra värden infogad under distributionen. Om du anger värden för en parameter i både lokala parameterfilen och infogade måste företräde det infogade värdet.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>Testa en för malldistribution

Testa din mall- och parameterfilerna värden utan att faktiskt distribuera resurser med [az group deployment Validera](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Om inga fel identifieras måste returnerar kommandot information om test-distribution. Observera att särskilt den **fel** värdet är null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Om ett fel upptäcks kan returnerar kommandot ett felmeddelande. Skicka ett felaktigt värde för lagringskontots SKU, returnerar exempelvis följande fel:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Om mallen innehåller ett syntaxfel, returnerar kommandot ett felmeddelande om att det gick inte att parsa mallen. Meddelandet Anger radnumret och positionen för parsningsfel.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Nästa steg

- Exemplen i den här artikeln distribuera resurser till en resursgrupp i din Standardprenumeration. Om du vill använda en annan prenumeration, se [hantera flera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli).
- Om du vill ange hur du hanterar resurser som finns i resursgruppen men inte har definierats i mallen, se [distributionslägen i Azure Resource Manager](deployment-modes.md).
- Information om hur du definierar parametrar i mallen finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
- Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](resource-manager-cli-sas-token.md).
- Om du vill distribuera på ett säkert sätt din tjänst till flera regioner, se [Azure Deployment Manager](deployment-manager-overview.md).
