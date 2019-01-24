---
title: Självstudiekurs – Installera appar på en Windows-dator i Azure | Microsoft Docs
description: I kursen får du lära dig hur du använder tillägget för anpassat skript för att köra skript och distribuera appar till Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 605649992cad988b2630034c7fdb8df1dc0fd5f5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435405"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Självstudiekurs – Distribuera appar till en Windows-dator i Azure med tillägget för anpassat skript

Om du vill konfigurera virtuella datorer (VM) på ett snabbt och konsekvent sätt, kan det vara användbart med någon form av automatisering. Ett vanligt sätt för att anpassa en virtuell Windows-dator är att använda [Anpassat skripttillägg för Windows](extensions-customscript.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Använd det anpassade skripttillägget till att installera IIS
> * Skapa en virtuell dator som använder det anpassade skripttillägget
> * Visa en IIS-webbplats efter att tillägget har tillämpats

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs Azure PowerShell-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.


## <a name="custom-script-extension-overview"></a>Översikt över Anpassat skripttillägg
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning.

Det anpassade skripttillägget är integrerat med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller REST API:n för virtuella Azure-datorer.

Du kan använda det anpassade skripttillägget med både Windows och Linux virtuella datorer.


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu kan du skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVM* på platsen *EastUS* (Östra USA). Resursgruppen *myResourceGroupAutomate* och stödjande nätverksresurser skapas om de inte redan finns. För att tillåta webbtrafik öppnar cmdleten även port *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Det tar några minuter att skapa resurserna och den virtuella datorn.


## <a name="automate-iis-install"></a>Automatisera installationen av IIS
Använd [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) till att installera det anpassade skripttillägget. Tillägget kör `powershell Add-WindowsFeature Web-Server` för att installera IIS-webbservern och uppdaterar sedan sidan *Default.htm* till att visa värddatornamnet för den virtuella datorn:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testwebbplats
Hämta den offentliga IP-adressen för lastbalanseraren med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIPAddress* som skapades tidigare:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Köra IIS-webbplatsen](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien kommer du att automatisera installationen av IIS på en virtuell dator. Du har lärt dig att:

> [!div class="checklist"]
> * Använd det anpassade skripttillägget till att installera IIS
> * Skapa en virtuell dator som använder det anpassade skripttillägget
> * Visa en IIS-webbplats efter att tillägget har tillämpats

Gå vidare till nästa självstudie där du får lära dig att skapa anpassade avbildningar för virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa anpassade avbildningar av en virtuell dator](./tutorial-custom-images.md)
