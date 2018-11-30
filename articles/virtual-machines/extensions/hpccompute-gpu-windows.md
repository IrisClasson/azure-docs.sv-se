---
title: NVIDIA GPU-drivrutinen tillägg – virtuella Azure Windows-datorer | Microsoft Docs
description: Microsoft Azure-tillägget för att installera NVIDIA GPU-drivrutiner på N-serien compute virtuella datorer som kör Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: roiyz
ms.openlocfilehash: ee74d4520e867604f50c70f2b6449f12ff3bd8b9
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495967"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA GPU-drivrutinen-tillägg för Windows

## <a name="overview"></a>Översikt

Det här tillägget installerar NVIDIA GPU-drivrutiner på Windows virtuella datorer i N-serien. Beroende på VM-familjen installerar tillägget CUDA- eller NÄTVERKSBASERADE drivrutiner. När du installerar NVIDIA drivrutinerna med hjälp av det här tillägget du accepterar och samtycker till villkoren i den [NVIDIA licensavtalet](https://go.microsoft.com/fwlink/?linkid=874330). Under installationen, kan den virtuella datorn startas om för att slutföra installationen för drivrutinen.

Ett tillägg kan även installera NVIDIA GPU-drivrutiner på [Linux N-serien virtuella datorer](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Det här tillägget har stöd för följande operativsystem:

| Distribution | Version |
|---|---|
| Windows 10 | Kärna |
| Windows Server 2016 | Kärna |
| Windows Server 2012R2 | Kärna |

### <a name="internet-connectivity"></a>Internetanslutning

Microsoft Azure-tillägget för NVIDIA GPU-drivrutiner kräver att den Virtuella måldatorn är ansluten till internet och har åtkomst.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Egenskaper

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| utgivare | Microsoft.HpcCompute | sträng |
| typ | NvidiaGpuDriverWindows | sträng |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Inställningar

Alla inställningar är valfria. Standardbeteendet är installera den senaste stödda drivrutinen som är tillämpligt.

| Namn | Beskrivning | Standardvärde | Giltiga värden | Datatyp |
| ---- | ---- | ---- | ---- | ---- |
| driverVersion | NV: GRID drivrutinsversion<br> NC/ND: CUDA drivrutinsversion | senaste | GRID: ”411.81”, ”391.81”, ”391.58”, ”391.03”<br> CUDA: ”398.75”, ”397.44”, ”390.85” | sträng |
| installGridND | Installera GRID drivrutinen på ND-serien virtuella datorer | false | SANT, FALSKT | boolesk |

## <a name="deployment"></a>Distribution


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall 

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution.

JSON-konfiguration för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md). 

I följande exempel förutsätter att tillägget är kapslade i den virtuella datorresursen. När kapsla tillägget resursen JSON placeras i den `"resources": []` objekt av den virtuella datorn.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

I följande exempel speglar ARM och PowerShell-exemplet ovan och lägger också till anpassade inställningar som ett exempel på icke-standard drivrutinsinstallation. Mer specifikt installerar en specifik drivrutin rutnät, även om en ND-serien virtuella datorn etableras.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "driverVersion": "391.03",
    "installGridND": true
  }'
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure PowerShell och Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Felkoder

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Åtgärden har slutförts. Omstart krävs. |
| 100 | Åtgärden stöds inte eller kunde inte slutföras. | Möjliga orsaker: PowerShell-version inte stöds, VM-storleken är inte en N-serien virtuell dator, fel laddades ned. Kontrollera loggfilerna om du vill ta reda på orsaken till felet. |
| 240, 840 | Tidsgränsen för åtgärden. | Försök igen. |
| -1 | Undantag inträffade. | Kontrollera loggfilerna om du vill ta reda på orsaken till undantaget. |
| – 5 gånger | Åtgärden avbröts på grund av en väntande omstart. | Starta om virtuell dator. Installationen fortsätter efter omstart. Avinstallera ska anropas manuellt. |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [VM-tillägg och funktioner i Windows](features-windows.md).

Läs mer om virtuella datorer i N-serien, [GPU-optimerad storlekar för virtuella datorer](../windows/sizes-gpu.md).
