---
title: Hur du distribuerar Windows 10 på Azure med Multitenant som är värd för behörighet
description: Lär dig att maximera din Windows Software Assurance-förmåner för att använda lokala licenser för Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: b99ae2abdc4e062588174b6d945b6592ae8e58ed
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707861"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Hur du distribuerar Windows 10 på Azure med Multitenant som är värd för behörighet 
För kunder med Windows 10 Enterprise E3/E5 per användare eller Windows virtuella skrivbord åtkomst per användare (användare prenumerationslicenser eller tillägg användarlicenser prenumeration), Multitenant som är värd för rättigheter för Windows 10 som gör att du kan ta med din Windows 10-licenser till molnet och kör Windows 10-datorer i Azure utan att betala för en annan licens. Mer information finns i [Multitenant som är värd för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> Den här artikeln visar att implementera licensieringsförmånen för Windows 10 Pro Desktop-avbildningar på Azure Marketplace.
> - Windows 7, 8.1, 10 Enterprise (x64)-avbildningar på Azure Marketplace för MSDN-prenumerationer finns i [Windows-klienten i Azure för utveckling och testning](client-images.md)
> - Windows Server-licensiering fördelar, finns i [Azure Hybrid använda förmåner för Windows Server-avbildningar](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuera Windows 10-avbildning från Azure Marketplace 
För Powershell, CLI och Azure Resource Manager-mall-distributioner finns Windows 10-avbildning med följande publishername, erbjudande, sku.

| OS  |      PublisherName      |  Erbjudande | Sku |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Ladda upp Windows 10 virtuell Hårddisk till Azure
Om du överför en generaliserad virtuell Hårddisk 10 Windows, Lägg märke till Windows 10 inte har inbyggda administratörskontot är aktiverat som standard. Inkludera följande kommando som en del av tillägget för anpassat skript för att aktivera det inbyggda administratörskontot.

```powershell
Net user <username> /active:yes
```

Följande powershell-kodavsnitt är att markera alla administratörskonton som aktiv, inklusive det inbyggda administratörskontot. Det här exemplet är användbart om inbyggda administratörens användarnamn är okänd.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Mer information: 
* [Ladda upp VHD till Azure](upload-generalized-managed.md)
* [Så här förbereder du en Windows virtuell Hårddisk för överföring till Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuera Windows 10 med flera innehavare som värd rättigheter
Kontrollera att du har [installerat och konfigurerat den senaste Azure PowerShell](/powershell/azure/overview). När du har skapat en virtuell Hårddisk kan ladda upp den virtuella Hårddisken till ditt Azure Storage-kontot med den `Add-AzVhd` cmdlet enligt följande:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuera med hjälp av Azure Resource Manager-mall för distribution** i Resource Manager-mallar, en extra parameter för `licenseType` kan anges. Du kan läsa mer om [skapa Azure Resource Manager-mallar](../../resource-group-authoring-templates.md). När du har en virtuell Hårddisk som överförts till Azure kan redigera Resource Manager-mall för att inkludera licenstypen som en del av compute-providern och distribuera din mall som vanligt:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Distribuera via PowerShell** när du distribuerar Windows Server-dator via PowerShell måste du ha en extra parameter för `-LicenseType`. När du har en virtuell Hårddisk som överförts till Azure kan du skapa en virtuell dator med `New-AzVM` och ange vilken licensiering enligt följande:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Kontrollera Virtuellt datorn använder licensieringsförmånen
När du har distribuerat den virtuella datorn via antingen distributionsmetoden PowerShell eller Resource Manager, kontrollera licenstypen med `Get-AzVM` på följande sätt:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Utdata liknar följande exempel för Windows 10 med rätt licenstyp:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Detta utdata visas med följande VM distribuerats utan Azure Hybrid Use Benefit licensiering, till exempel en virtuell dator distribueras direkt från Azure-galleriet:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Mer Information om för Azure AD
>[!NOTE]
>Azure etablerar alla Windows-datorer med inbyggda administratörskontot som inte kan användas för att ansluta till AAD. Till exempel *Inställningar > konto > åtkomst till arbets- eller skola > + Connect* fungerar inte. Du måste skapa och logga in som en andra administratörskonto för att ansluta till Azure AD manuellt. Du kan också konfigurera Azure AD med hjälp av ett konfigurationspaket, Använd länken är den *nästa steg* avsnittet om du vill veta mer.
>
>

## <a name="next-steps"></a>Nästa steg
- Läs mer om [konfigurera VDA för Windows 10](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- Läs mer om [Multitenant som är värd för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


