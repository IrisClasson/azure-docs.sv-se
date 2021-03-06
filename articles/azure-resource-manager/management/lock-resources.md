---
title: Lås resurser för att förhindra ändringar
description: Förhindra att användare uppdaterar eller tar bort kritiska Azure-resurser genom att använda ett lås för alla användare och roller.
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e76287c4524831a84a22fb23ddf8a5fdee8bc12b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827290"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Låsa resurser för att förhindra oväntade ändringar

Som administratör kan du behöva låsa en prenumeration, resursgrupp eller resurs för att förhindra att andra användare i organisationen av misstag tar bort eller ändrar viktiga resurser. Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen kallas låsen **Delete** och **Read Only** .

* **CanNotDelete** innebär att behöriga användare fortfarande kan läsa och ändra en resurs, men de kan inte ta bort resursen.
* **ReadOnly** innebär att auktoriserade användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Att använda det här låset liknar att begränsa alla behöriga användare till de behörigheter som har beviljats av rollen **läsare** .

## <a name="how-locks-are-applied"></a>Hur låsen används

När du använder ett lås vid en överordnad omfattning ärver alla resurser inom den omfattningen samma lås. Även resurser som du lägger till senare ärver låset från det överordnade objektet. Det mest restriktiva låset i arv prioriteras.

Till skillnad från rollbaserad åtkomstkontroll använder du hanteringslås för att tillämpa en begränsning för alla användare och roller. Information om hur du anger behörigheter för användare och roller finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

Resource Manager-lås gäller endast för åtgärder som sker i hanteringsplanet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsar inte hur resurser utför sina egna funktioner. Resursändringar är begränsade, men resursåtgärder är inte begränsade. Ett skrivskyddat lås på en SQL Database hindrar dig till exempel från att ta bort eller ändra databasen. Det hindrar dig inte från att skapa, uppdatera eller ta bort data i databasen. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Att tänka på innan du använder lås

Att använda Lås kan leda till oväntade resultat eftersom vissa åtgärder som inte verkar ändra resursen verkligen kräver åtgärder som blockeras av låset. Några vanliga exempel på åtgärder som blockeras av lås är:

* Ett skrivskyddat lås på ett **lagrings konto** förhindrar att alla användare visar nycklarna. Åtgärden för att visa nycklar hanteras via en POST-begäran eftersom nycklarna som returneras är tillgängliga för skrivåtgärder.

* Ett skrivskyddat lås på en **App Service** resurs förhindrar att Visual Studio-Server Explorer visar filer för resursen, eftersom denna interaktion kräver skriv åtkomst.

* Ett skrivskyddat lås på en **resurs grupp** som innehåller en **virtuell dator** hindrar alla användare från att starta eller starta om den virtuella datorn. De här åtgärderna kräver en POST-begäran.

* Ett borttagnings lås för en **resurs grupp** förhindrar Azure Resource Manager från att [automatiskt ta bort distributioner](../templates/deployment-history-deletions.md) i historiken. Om du når 800-distributioner i historiken går det inte att distribuera.

* Ett borttagnings lås på **resurs gruppen** som skapats av **Azure Backup tjänsten** medför att säkerhets kopieringen Miss lyckas. Tjänsten har stöd för högst 18 återställnings punkter. När det är låst kan säkerhets kopierings tjänsten inte rensa återställnings punkter. Mer information finns i vanliga frågor och svar om hur du [säkerhetskopierar virtuella Azure-datorer](../../backup/backup-azure-vm-backup-faq.md).

* Ett skrivskyddat lås på en **prenumeration** förhindrar att **Azure Advisor** fungerar korrekt. Advisor kan inte lagra resultatet av sina frågor.

## <a name="who-can-create-or-delete-locks"></a>Vem kan skapa eller ta bort lås

Om du vill skapa eller ta bort hanterings lås måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

## <a name="managed-applications-and-locks"></a>Hanterade program och lås

Vissa Azure-tjänster, till exempel Azure Databricks, använder [hanterade program](../managed-applications/overview.md) för att implementera tjänsten. I så fall skapar tjänsten två resurs grupper. En resurs grupp innehåller en översikt över tjänsten och är inte låst. Den andra resurs gruppen innehåller infrastrukturen för tjänsten och är låst.

Om du försöker ta bort infrastruktur resurs gruppen får du ett fel meddelande om att resurs gruppen är låst. Om du försöker ta bort låset för infrastruktur resurs gruppen får du ett fel meddelande om att låset inte kan tas bort eftersom det ägs av ett system program.

Ta i stället bort tjänsten, som också tar bort infrastruktur resurs gruppen.

För hanterade program väljer du den tjänst som du har distribuerat.

![Välj tjänst](./media/lock-resources/select-service.png)

Observera att tjänsten innehåller en länk till en **hanterad resurs grupp**. Resurs gruppen innehåller infrastrukturen och är låst. Den kan inte tas bort direkt.

![Visa hanterad grupp](./media/lock-resources/show-managed-group.png)

Om du vill ta bort allt för tjänsten, inklusive resurs gruppen låst infrastruktur, väljer du **ta bort** för tjänsten.

![Ta bort tjänst](./media/lock-resources/delete-service.png)

## <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Mall

När du använder en Resource Manager-mall för att distribuera ett lås, använder du olika värden för namn och typ beroende på låsets omfång.

Använd följande format när du använder ett lås på en **resurs**:

* ändrat`{resourceName}/Microsoft.Authorization/{lockName}`
* bastyp`{resourceProviderNamespace}/{resourceType}/providers/locks`

Använd följande format när du använder ett lås till en **resurs grupp** eller **prenumeration**:

* ändrat`{lockName}`
* bastyp`Microsoft.Authorization/locks`

I följande exempel visas en mall som skapar en app service-plan, en webbplats och ett lås på webbplatsen. Resurs typen för låset är resurs typen för den resurs som ska låsas och **/providers/Locks**. Namnet på låset skapas genom att resurs namnet kombineras med **/Microsoft.Authorization/** och namnet på låset.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2019-08-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2019-08-01",
      "name": "[variables('siteName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Web/sites/providers/locks",
      "apiVersion": "2016-09-01",
      "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

Ett exempel på att ställa in ett lås på en resurs grupp finns i [skapa en resurs grupp och låsa den](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell

Du låser distribuerade resurser med Azure PowerShell med kommandot [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Om du vill låsa en resurs anger du namnet på resursen, resurs typ och dess resurs grupp namn.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Ange namnet på resurs gruppen om du vill låsa en resurs grupp.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Om du vill ha information om ett lås använder du [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Använd följande för att hämta alla Lås i din prenumeration:

```azurepowershell-interactive
Get-AzResourceLock
```

Om du vill hämta alla Lås för en resurs använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill hämta alla Lås för en resurs grupp använder du:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Du låser distribuerade resurser med Azure CLI med hjälp av kommandot [AZ lock Create](/cli/azure/lock#az-lock-create) .

Om du vill låsa en resurs anger du namnet på resursen, resurs typ och dess resurs grupp namn.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Ange namnet på resurs gruppen om du vill låsa en resurs grupp.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Om du vill ha information om ett lås använder du [AZ lock List](/cli/azure/lock#az-lock-list). Använd följande för att hämta alla Lås i din prenumeration:

```azurecli
az lock list
```

Om du vill hämta alla Lås för en resurs använder du:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Om du vill hämta alla Lås för en resurs grupp använder du:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST-API

Du kan låsa distribuerade resurser med [REST API för hanterings lås](/rest/api/resources/managementlocks). Med REST API kan du skapa och ta bort lås och hämta information om befintliga lås.

Skapa ett lås genom att köra:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Omfånget kan vara en prenumeration, en resurs grupp eller en resurs. Namnet på låset är vad du vill för att anropa låset. Använd **2016-09-01**för API-version.

I begäran inkluderar du ett JSON-objekt som anger egenskaperna för låset.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du strukturerar resurserna logiskt finns i [använda taggar för att organisera dina resurser](tag-resources.md).
* Du kan tillämpa begränsningar och konventioner i din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
