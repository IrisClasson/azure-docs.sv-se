---
title: 'Snabb start: skapa en Data Science VM-Resource Manager-mall'
titleSuffix: Azure Data Science Virtual Machine
description: I den här snabb starten använder du en Azure Resource Manager-mall för att snabbt distribuera en Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 675ddf073393afde6ac8d08a65b40da11d90d3ea
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026669"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Snabb start: skapa en Ubuntu-Data Science Virtual Machine med en ARM-mall

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här snabb starten visas hur du skapar en Ubuntu 18,04-Data Science Virtual Machine med en Azure Resource Manager-mall (ARM-mall). Data vetenskap Virtual Machines är molnbaserade virtuella datorer som är förinstallerade med en svit med data vetenskaps-och maskin inlärnings ramverk och verktyg. När du har distribuerat en GPU-baserad beräknings resurs konfigureras alla verktyg och bibliotek för att använda GPU: n. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förutsättningarna och du är van att använda ARM-mallar, väljer du knappen **distribuera till Azure** . Mallen öppnas i Azure Portal.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/services/machine-learning/) innan du börjar.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). Den fullständiga mallen för den här artikeln är för lång för att kunna visas här. Om du vill visa hela mallen går du till [azuredeploy.jspå](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). Den del som definierar de olika DSVM visas här:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

Följande resurser definieras i mallen:

* [Microsoft. Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): skapa en molnbaserad virtuell dator. I den här mallen är den virtuella datorn konfigurerad som en Data Science Virtual Machine som kör Ubuntu 18,04.

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill använda mallen från Azure CLI loggar du in och väljer din prenumeration (se [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)). Kör sedan:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

När du kör kommandot ovan anger du:

1. Namnet på den resurs grupp som du vill skapa som innehåller DSVM och tillhör ande resurser.
1. Den Azure-plats där du vill göra distributionen.
1. Autentiseringstypen som du vill använda (ange strängen `password` eller `sshPublicKey` ).
1. Inloggnings namnet för administratörs kontot (det här värdet får inte vara `admin` ).
1. Värdet för lösen ordet eller den offentliga SSH-nyckeln för kontot.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Så här ser du Data Science Virtual Machine:

1. Gå till https://portal.azure.com.
1. Logga in.
1. Välj den resurs grupp som du nyss skapade.

Du ser resurs gruppens information:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Skärm bild av en grundläggande resurs grupp som innehåller en DSVM":::

Klicka på den virtuella dator resursen för att gå till sidan med information. Här hittar du information om den virtuella datorn, inklusive anslutnings information.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill använda den här virtuella datorn tar du bort den. Eftersom DSVM är kopplat till andra resurser, till exempel ett lagrings konto, vill du förmodligen ta bort hela resurs gruppen som du skapade. Du kan ta bort resurs gruppen med hjälp av portalen genom att klicka på knappen **ta bort** och bekräfta. Du kan också ta bort resurs gruppen från CLI med:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Data Science Virtual Machine från en ARM-mall.

> [!div class="nextstepaction"]
> [Exempel program & ML-genom gångar](dsvm-samples-and-walkthroughs.md)
