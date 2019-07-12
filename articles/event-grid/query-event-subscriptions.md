---
title: Fråga Azure Event Grid-prenumerationer
description: Beskriver hur du listar Azure Event Grid-prenumerationer.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: 08fa9ada97c652ca609a499da1c8e87f7e4487db
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786632"
---
# <a name="query-event-grid-subscriptions"></a>Fråga Event Grid-prenumerationer 

Den här artikeln beskriver hur du listar Event Grid-prenumerationer i Azure-prenumerationen. När du hämtar din befintliga Event Grid-prenumerationer, är det viktigt att förstå de olika typerna av prenumerationer. Du kan ange olika parametrar utifrån vilken typ av prenumeration som du vill hämta.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Resursgrupper och Azure-prenumerationer

Azure-prenumerationer och resursgrupper är du inte Azure-resurser. Därför har event grid-prenumerationer till resursgrupper eller Azure-prenumerationer inte samma egenskaper som event grid-prenumerationer till Azure-resurser. Event grid-prenumerationer till resursgrupper eller Azure-prenumerationer betraktas som global.

För att få event grid-prenumerationer för en Azure-prenumeration och dess resursgrupper, behöver du inte ange några parametrar. Kontrollera att du har valt Azure-prenumeration du vill fråga. I följande exempel få inte event grid-prenumerationer för anpassade ämnen eller Azure-resurser.

Om du använder Azure CLI använder du:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

För att få event grid-prenumerationer för en Azure-prenumeration kan du ange ämnestypen **Microsoft.Resources.Subscriptions**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

För att få event grid-prenumerationer för alla resursgrupper i en Azure-prenumeration kan du ange ämnestypen **Microsoft.Resources.ResourceGroups**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Ange namnet på resursgruppen som en parameter för att få event grid-prenumerationer för en angiven resursgrupp.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Anpassade ämnen och Azure-resurser

Anpassat Event grid-ämnen är Azure-resurser. Därför kan fråga du event grid-prenumerationer för anpassade ämnen och andra resurser, t.ex. Blob storage-konto på samma sätt. För att få event grid-prenumerationer för anpassade ämnen kan ange du parametrar som identifierar resursen eller identifiera platsen för resursen. Det går inte att brett query event grid-prenumerationer för resurser i din Azure-prenumeration.

För att få event grid-prenumerationer för anpassade ämnen och andra resurser på en plats kan du ange namnet på platsen.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Om du vill hämta prenumerationer på anpassade ämnen för en plats, ange platsen och ämnestypen **Microsoft.EventGrid.Topics**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Om du vill hämta prenumerationer till storage-konton för en plats, ange platsen och ämnestypen **Microsoft.Storage.StorageAccounts**.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Ange namnet på det anpassade ämnet och namnet på en resursgrupp för att få event grid-prenumerationer för ett anpassat ämne.

Om du använder Azure CLI använder du:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

För att få event grid-prenumerationer för en viss resurs kan du ange resurs-ID.

Om du använder Azure CLI använder du:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Nästa steg

* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
