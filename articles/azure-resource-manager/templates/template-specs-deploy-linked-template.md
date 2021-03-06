---
title: Distribuera en mall specifikation som en länkad mall
description: Lär dig hur du distribuerar en befintlig mall-specifikation i en länkad distribution.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 5d4824ea432d804418fda2cdc90d49154d496722
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099740"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Självstudie: Distribuera en mall specifikation som en länkad mall (förhands granskning)

Lär dig hur du distribuerar en befintlig [mall-specifikation](template-specs.md) med hjälp av en [länkad distribution](linked-templates.md#linked-template). Du använder mall-specifikationer för att dela ARM-mallar med andra användare i din organisation. När du har skapat en mall kan du distribuera mallens specifikation med hjälp av Azure PowerShell. Du kan också distribuera mal Lav specifikationen som en del av din lösning med hjälp av en länkad mall.

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Du måste [Registrera dig för för hands versionen för](https://aka.ms/templateSpecOnboarding)att kunna använda den.

## <a name="create-a-template-spec"></a>Skapa en mall specifikation

Följ [snabb start: skapa och distribuera mall-specifikation](quickstart-create-template-specs.md) för att skapa en mall-specifikation för att distribuera ett lagrings konto. Du behöver resurs grupp namnet för mallens specifikation, mallens Specifikations namn och versions specifikationen för mallen i nästa avsnitt.

## <a name="create-the-main-template"></a>Skapa huvud mal len

Om du vill distribuera en malls specifikation i en ARM-mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I `templateLink` egenskapen anger du resurs-ID för en mall specifikation. skapa en mall med följande JSON som kallas **azuredeploy.jspå**. Den här självstudien förutsätter att du har sparat till en sökväg **c:\Templates\deployTS\azuredeploy.js** men du kan använda valfri sökväg.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

Mallens Specifikations-ID genereras med hjälp av [`resourceID()`](template-functions-resource.md#resourceid) funktionen. Resurs grupps argumentet i funktionen resourceID () är valfritt om templateSpec finns i samma resurs grupp för den aktuella distributionen.  Du kan också direkt skicka in resurs-ID som en parameter. Hämta ID: t genom att använda:

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Version.Id
```

Syntaxen för att skicka parametrar till specifikationen för mallen är:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> API version `Microsoft.Resources/deployments` måste vara 2020-06-01 eller senare.

## <a name="deploy-the-template"></a>Distribuera mallen

När du distribuerar den länkade mallen distribuerar den både webb programmet och lagrings kontot. Distributionen är samma som att distribuera andra ARM-mallar.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json"
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en mall-specifikation som innehåller länkade mallar finns i [skapa en mall specifikation av en länkad mall](template-specs-create-linked.md).
