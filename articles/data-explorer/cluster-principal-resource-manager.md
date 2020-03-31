---
title: Lägga till klusterobjektnamn för Azure Data Explorer med hjälp av en Azure Resource Manager-mall
description: I den här artikeln får du lära dig hur du lägger till klusterhuvudnamn för Azure Data Explorer med hjälp av en Azure Resource Manager-mall.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 22423568ab0b3b55d8d9566df4829eb6070b5f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965052"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Lägga till klusterobjektnamn för Azure Data Explorer med hjälp av en Azure Resource Manager-mall

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-mall](cluster-principal-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. I den här artikeln lägger du till klusterobjektnamn för Azure Data Explorer med hjälp av en Azure Resource Manager-mall.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Skapa ett kluster](create-cluster-database-portal.md).

## <a name="azure-resource-manager-template-for-adding-a-cluster-principal"></a>Azure Resource Manager-mall för att lägga till ett klusterhuvudnamn

I följande exempel visas en Azure Resource Manager-mall för att lägga till ett klusterhuvudnamn.  Du kan [redigera och distribuera mallen i Azure-portalen](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) med hjälp av formuläret.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterPrincipalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "principalIdForCluster": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForClusterPrincipal": {
            "type": "string",
            "defaultValue": "AllDatabasesViewer",
            "metadata": {
                "description": "Specifies the cluster principal role. It can be 'AllDatabasesAdmin', 'AllDatabasesViewer'"
            }
        },
        "tenantIdForClusterPrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the principal"
            }
        },
        "principalTypeForCluster": {
            "type": "string",
            "defaultValue": "User",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('clusterPrincipalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForCluster')]",
                "role": "[parameters('roleForClusterPrincipal')]",
                "tenantId": "[parameters('tenantIdForClusterPrincipal')]",
                "principalType": "[parameters('principalTypeForCluster')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

* [Lägga till databashuvudnamn](database-principal-resource-manager.md)
