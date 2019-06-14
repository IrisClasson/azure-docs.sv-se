---
title: 'Migrera virtuella nätverk från klassisk till Resource Manager - ExpressRoute: Azure: PowerShell | Microsoft Docs'
description: Den här sidan beskriver hur du migrerar ExpressRoute-associerade virtuella nätverk till Resource Manager när du har flyttat din krets.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2e33454ac0ee97385386043706f4b8b73090f57a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363860"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrera ExpressRoute-associerade virtuella nätverk från klassisk till Resource Manager

Den här artikeln förklarar hur du migrerar ExpressRoute-associerade virtuella nätverk från den klassiska distributionsmodellen Azure Resource Manager-distributionsmodellen när du har flyttat din ExpressRoute-krets. 

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Kontrollera att du har den senaste versionen av Azure PowerShell-moduler. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Se till att du har granskat den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Granska informationen som tillhandahålls under [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md). Se till att du förstår gränser och begränsningar.
* Kontrollera att kretsen är fullt fungerande i den klassiska distributionsmodellen.
* Kontrollera att du har en resursgrupp som har skapats i Resource Manager-distributionsmodellen.
* Granska följande resursmigrering dokumentation:

    * [Plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Vanliga frågor och svar: Plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Granska de vanligaste migreringsfelen och åtgärder](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scenarier som stöds och som inte stöds

* En ExpressRoute-krets kan flyttas från klassiskt till Resource Manager-miljön utan någon avbrottstid. Du kan flytta alla ExpressRoute-krets från klassiskt till Resource Manager-miljön utan avbrott. Följ instruktionerna i [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen med hjälp av PowerShell](expressroute-howto-move-arm.md). Det här är en förutsättning för att flytta resurser som är anslutna till det virtuella nätverket.
* Virtuella nätverk, gatewayer och associerade distributioner i det virtuella nätverket som är kopplade till en ExpressRoute-krets i samma prenumeration kan migreras till Resource Manager-miljön utan någon avbrottstid. Du kan följa stegen som beskrivs senare om du vill migrera resurser, till exempel virtuella nätverk, gatewayer och virtuella datorer som distribueras i det virtuella nätverket. Du måste se till att de virtuella nätverken är korrekt konfigurerade innan de migreras. 
* Virtuella nätverk, gatewayer och associerade distributioner i det virtuella nätverket som inte ingår i samma prenumeration som ExpressRoute-kretsen kräver vissa avbrott att slutföra migreringen. Det sista avsnittet av dokumentet beskriver de steg som ska följas för att migrera resurser.
* Ett virtuellt nätverk med både ExpressRoute-gatewayen och VPN-Gateway kan inte migreras.
* ExpressRoute-krets mellan prenumerationer migrering stöds inte. Mer information finns i [tjänster som inte kan flyttas](../azure-resource-manager/resource-group-move-resources.md#services-that-cannot-be-moved).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Flytta en ExpressRoute-krets från klassisk till Resource Manager
Du måste flytta en ExpressRoute-krets från klassiskt till Resource Manager-miljön innan du försöker migrera resurser som är kopplade till ExpressRoute-kretsen. Om du vill utföra den här uppgiften finns i följande artiklar:

* Granska informationen som tillhandahålls under [flytta en ExpressRoute-krets från klassisk till Resource Manager](expressroute-move.md).
* [Flytta en krets från klassisk till Resource Manager med Azure PowerShell](expressroute-howto-move-arm.md).
* Använda Azure Service Management-portalen. Du kan följa arbetsflödet ska [skapa en ny ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och välja importalternativet. 

Den här åtgärden innebär inte någon avbrottstid. Du kan fortsätta att överföra data mellan dina lokaler och Microsoft medan migreringen pågår.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrera virtuella nätverk, gatewayer och associerade distributioner

De steg du följer för att migrera beror på om dina resurser är i samma prenumeration eller den olika prenumerationer.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrera virtuella nätverk, gatewayer och associerade distributioner i samma prenumeration som ExpressRoute-krets
Det här avsnittet beskriver de steg som ska följas för att migrera ett virtuellt nätverk, gatewayen och associerade distributioner i samma prenumeration som ExpressRoute-kretsen. Ingen avbrottstid är associerad med den här migreringen. Du kan fortsätta att använda alla resurser genom migreringsprocessen. Hanteringsplanet är låst medan migreringen pågår. 

1. Se till att ExpressRoute-krets har flyttats från klassiskt till Resource Manager-miljön.
2. Se till att det virtuella nätverket har förberetts på rätt sätt för migreringen.
3. Registrera din prenumeration för resursmigrering. Om du vill registrera din prenumeration för resursmigrering, använder du följande PowerShell-kodavsnitt:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Validera, förbereda och migrera. Om du vill flytta det virtuella nätverket, använder du följande PowerShell-kodavsnitt:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Du kan också avbryta migreringen genom att köra följande PowerShell-cmdlet:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Nästa steg
* [Plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Vanliga frågor och svar: Plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Granska de vanligaste migreringsfelen och åtgärder](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
