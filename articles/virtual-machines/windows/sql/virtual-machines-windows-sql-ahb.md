---
title: Ändra så att licensieringsmodellen för en SQL Server-VM i Azure | Microsoft Docs
description: Lär dig mer om att växla licenser för en SQL-dator i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1fb67600ea01629e7bf3ab4c7c470e4727b0e923
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393178"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Så här ändrar du så att licensieringsmodellen för en SQL Server-dator i Azure
Den här artikeln beskriver hur du ändrar så att licensieringsmodellen för en SQL Server-dator i Azure med hjälp av den nya SQL-VM-resursprovidern - **Microsoft.SqlVirtualMachine**. Det finns två licensiering modeller för en virtuell dator (VM) som är värd för SQL Server – betala per användning, och Använd din egen licens (BYOL). Och nu, med hjälp av Azure portal, Azure CLI eller PowerShell kan du ändra vilken licensieringsmodell som använder SQL Server-dator. 

Den **användningsbaserad** (PAYG) modellen innebär att kostnaden per sekund för att köra Azure VM omfattar kostnaden för SQL Server-licens.

Den **bring-your-own-license** (BYOL) modellen är även känd som den [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), och du kan använda din egen SQL Server-licens med en virtuell dator som kör SQL Server. Mer information om priser finns i [prissättning guide för SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Växla mellan de två modellerna licens medför **utan avbrott**, startar inte den virtuella datorn, lägger till **utan extra kostnad** (i själva verket aktivera AHB *minskar* kostnaden) och är **från och med nu**. 

## <a name="remarks"></a>Kommentarer


 - Azure Cloud Solution Partner (CSP)-kunder kan använda Azure Hybrid-förmånen genom att först distribuera en betala per virtuell dator och sedan konvertera den till bring-your-own-license. 
 - När du registrerar en anpassad SQL Server-VM-avbildning med resursprovidern, ange licenstypen som = 'AHUB'. Lämna licensen ange tomma eller att ange 'PAYG' kommer registreringen att misslyckas. 
 - Om du släpper din SQL Server-VM-resurs ska du gå tillbaka till inställningen hårdkodade licens för avbildningen. 
 - Lägga till en SQL Server VM till en tillgänglighetsuppsättning måste återskapa den virtuella datorn. Som sådana, alla virtuella datorer som lagts till i en tillgänglighet set går tillbaka till typ av licens för betala per användning och AHB måste aktiveras igen. 
 - Ändra så att licensieringsmodellen är en funktion i SQL VM-resursprovidern. Distribuera en marketplace-avbildning via Azure portal automatiskt registrerar en SQL Server VM med resursprovidern. Men kunder som lokal installation av SQL Server måste du manuellt [registrera sina SQL Server-VM](#register-sql-server-vm-with-the-sql-vm-resource-provider). 
 

 
## <a name="limitations"></a>Begränsningar

 - Möjligheten att konvertera licensieringsmodellen är för närvarande bara tillgänglig när du startar en SQL Server VM-avbildning med modellen Betala per användning. Om du startar med en Bring your own license-avbildning från portalen kan du inte konvertera avbildningen till Betala per användning.
 - Ändra så att licensieringsmodellen stöds bara för virtuella datorer som distribueras med hjälp av Resource Manager-modellen. Virtuella datorer som distribueras med den klassiska modellen stöds inte. 
 - Ändra så att licensieringsmodellen aktiveras endast för offentliga moln-installationer.
 - Ändra så att licensieringsmodellen stöds bara på virtuella datorer som har ett enda nätverkskort (nätverksgränssnitt). På virtuella datorer som har mer än ett NIC, du bör först ta bort ett nätverkskort (med hjälp av Azure-portalen) innan du försöker proceduren. I annat fall körs i ett fel som liknar följande: `The virtual machine '\<vmname\>' has more than one NIC associated.` Även om du kan lägga till nätverkskortet tillbaka till den virtuella datorn när du har ändrat licensieringsalternativ, åtgärder via bladet SQL-konfiguration som automatisk uppdatering och säkerhetskopiering, inte längre betraktas som stöds.

## <a name="prerequisites"></a>Nödvändiga komponenter

Användningen av SQL VM-resursprovidern kräver SQL IaaS-tillägget. Det innebär för att fortsätta använda SQL VM-resursprovidern, behöver du följande:
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- [Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- En *användningsbaserad* [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) med den [SQL IaaS-tillägget](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installerad. 

## <a name="with-the-azure-portal"></a>Med Azure Portal

Du kan ändra så att licensieringsmodellen direkt från portalen. 

1. Gå till din SQL Server-VM i den [Azure-portalen](https://portal.azure.com). 
1. Välj **konfiguration av SQL Server** i den **inställningar** fönstret. 
1. Välj **redigera** i den **SQL Server-licens** fönstret för att ändra licensen. 

![AHB i portalen](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Det här alternativet är inte tillgängligt för bring-your-own-license-avbildningar. 

## <a name="with-azure-cli"></a>Med Azure CLI

Du kan använda Azure CLI för att ändra licensieringsmodellen.  

Följande kodavsnitt växlar modellen betala per licens till BYOL (eller med hjälp av Azure Hybrid-förmånen):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Följande kodavsnitt växlar bring-your-own-license-modell till betala per användning: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>Med PowerShell
Du kan använda PowerShell för att ändra licensieringsmodellen.

Följande kodavsnitt växlar modellen betala per licens till BYOL (eller med hjälp av Azure Hybrid-förmånen):

```powershell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Följande kodavsnitt växlar BYOL-modell till betala per användning:

```powershell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Registrera dig för SQL Server-dator med SQL VM-resursprovidern
I vissa situationer kan du behöva registrera manuellt SQL Server-dator med SQL VM-resursprovidern. För att göra det, kan du också behöva manuellt registrera resursprovidern med din prenumeration. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrera resursprovidern för SQL VM med prenumeration 

Om du vill registrera din SQL Server-dator med SQL-resursprovider, måste du registrera resursprovidern i din prenumeration. Du kan göra det med Azure portal eller Azure CLI. 

#### <a name="with-the-azure-portal"></a>Med Azure Portal
Följande steg ska registrera SQL-resursprovidern på Azure-prenumerationen med hjälp av Azure portal. 

1. Öppna Azure portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj prenumerationen av intresse.  
1. I den **prenumerationer** bladet går du till **resursprovidrar**. 
1. Typ `sql` i filtret för att få fram de SQL-relaterade resursprovidrar. 
1. Välj antingen *registrera*, *Omregistrera*, eller *avregistrera* för den **Microsoft.SqlVirtualMachine** providern beroende på din önskad åtgärd. 

   ![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Med Azure CLI
Följande kodavsnitt kommer registrera SQL VM-resursprovidern på Azure-prenumerationen. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Med PowerShell

Följande kodavsnitt kommer registrera SQL-resursprovidern på Azure-prenumerationen.

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registrera SQL Server-dator med SQL-resursprovider
När SQL VM-resursprovidern har registrerats i prenumerationen kan registrera du sedan din SQL Server-VM med resursprovidern med Azure CLI. 

#### <a name="with-azure-cli"></a>Med Azure CLI
Registrera SQL Server-dator med Azure CLI med följande kodavsnitt: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>Med PowerShell
Registrera SQL Server-dator med hjälp av PowerShell med följande kodavsnitt:

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>Kända fel

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>SQL IaaS-tillägget har inte installerats på den virtuella datorn
SQL IaaS-tillägget är ett nödvändigt krav för att registrera din SQL Server-dator med SQL VM-resursprovidern. Om du försöker registrera din SQL Server-VM innan du installerar tillägget SQL IaaS uppstår följande fel:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Lös problemet genom att installera SQL IaaS-tillägget innan du försöker registrera din SQL Server-VM. 

  > [!NOTE]
  > Installera SQL IaaS tillägget startar om SQL Server-tjänsten och bör endast göras under ett underhållsfönster. Mer information finns i [SQL IaaS tilläggsinstallationen](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Resursen ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resursgrupp > ”i resursgruppen”\<resursgrupp >' hittades inte. Egenskapen 'sqlServerLicenseType' kan inte hittas på det här objektet. Kontrollera att egenskapen finns och kan ställas in.
Det här felet uppstår vid försök att ändra licensieringsmodell på en SQL Server VM som inte har registrerats med SQL-resursprovider. Du måste registrera resursprovidern till din [prenumeration](#register-sql-vm-resource-provider-with-subscription), och sedan registrera SQL Server-dator med SQL [resursprovidern](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Det går inte att verifiera argumentet i parametern ”Sku”
Det här felet kan uppstå när du försöker ändra licensieringsmodellen för SQL Server-dator när du använder Azure PowerShell > 4.0: Set-AzResource: Det går inte att verifiera argumentet i parametern ”Sku”. Argumentet är null eller tomt. Ange ett argument som inte är null eller tomt och kör sedan kommandot igen.
Lös felet genom att ta bort kommentarerna raderna i kodfragmentet tidigare nämnda PowerShell när du växlar licensieringsmodellen:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Använd följande kod för att verifiera Azure PowerShell-version:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer på en Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer på en Windows VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server på en Windows-VM priser vägledning](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en Windows VM viktig information](virtual-machines-windows-sql-server-iaas-release-notes.md)


