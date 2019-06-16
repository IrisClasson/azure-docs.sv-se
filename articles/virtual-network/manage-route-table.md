---
title: Skapa, ändra eller ta bort en Azure routningstabellen
titlesuffix: Azure Virtual Network
description: Lär dig mer om att skapa, ändra eller ta bort en routningstabell.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849806"
---
# <a name="create-change-or-delete-a-route-table"></a>Skapa, ändra eller ta bort en routningstabell

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures routning kan göra du det genom att skapa en routningstabell. Om du inte har använt routning i virtuella nätverk kan du läsa mer om den i den [routning översikt](virtual-networks-udr-overview.md) eller genom att slutföra en [självstudien](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utför följande uppgifter innan du slutför stegen i ett avsnitt i den här artikeln:

* Om du inte redan har ett Azure-konto, registrera dig för en [kostnadsfritt utvärderingskonto](https://azure.microsoft.com/free).<br>
* Om du använder portalen, öppnar du https://portal.azure.com, och logga in med ditt Azure-konto.<br>
* Om du utför uppgifterna i den här artikeln med hjälp av PowerShell-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Vanliga Azure-verktyg finns förinstallerade och har konfigurerats för användning med ditt konto. Den här självstudien kräver Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.<br>
* Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här självstudien krävs Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

Kontot du loggar in på eller ansluta till Azure med, måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas de åtgärder som anges i [behörigheter ](#permissions).

## <a name="create-a-route-table"></a>Skapa en routningstabell

Det finns en gräns för hur många routningstabeller du kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. I det övre vänstra hörnet i portalen väljer du **+ skapa en resurs**.
1. Välj **nätverk**och välj sedan **routningstabellen**.
1. Ange en **namn** routningstabellen, Välj din **prenumeration**, skapa en ny **resursgrupp**, eller välj en befintlig resursgrupp, Välj en **plats** och välj sedan **skapa**. Om du planerar att associera routningstabellen till ett undernät i ett virtuellt nätverk som är ansluten till ditt lokala nätverk via en VPN-gateway och du inaktiverar **Virtual network gateway-spridning**, lokala vägar är inte sprids till nätverksgränssnitt i undernätet.

### <a name="create-route-table---commands"></a>Skapa routningstabell - kommandon

* Azure CLI: [skapa az network route-table](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Visa routningstabeller

Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den. Routningstabeller som finns i din prenumeration visas.

### <a name="view-route-table---commands"></a>Visa routningstabellen - kommandon

* Azure CLI: [az network route-table list](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Visa information om en routningstabell

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj routningstabellen i listan som du vill visa information om. Under **inställningar**, du kan visa den **vägar** i routningstabellen och **undernät** routningstabellen är kopplad till.
1. Mer information om gemensamma inställningar för Azure finns följande information:

    * [Aktivitetslogg](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [Åtkomstkontroll (IAM)](../role-based-access-control/overview.md)<br>
    * [Taggar](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Lås](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Automationsskript](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>Visa information om routningstabellen - kommandon

* Azure CLI: [az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Ändra en routningstabell

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj routningstabellen som du vill ändra. De vanligaste förändringar är [att lägga till](#create-a-route) eller [tar bort](#delete-a-route) vägar och [associera](#associate-a-route-table-to-a-subnet) routningstabeller till, eller [kopplas bort](#dissociate-a-route-table-from-a-subnet) routningstabeller från undernät.

### <a name="change-a-route-table---commands"></a>Ändra en routningstabell - kommandon

* Azure CLI: [az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Ett undernät kan ha ingen eller en associerad routningstabell. En routningstabell kan kopplas till inget eller flera undernät. Eftersom routningstabeller inte är kopplade till virtuella nätverk måste du associera en routningstabell till varje undernät som du vill associera routningstabellen till. All trafik som lämnar undernätet dirigeras baserat på vägar som du har skapat i routningstabeller [systemstandardvägar](virtual-networks-udr-overview.md#default), och vägar sprids från ett lokalt nätverk, om det virtuella nätverket är anslutet till en gateway () Azure-nätverk ExpressRoute eller VPN). Du kan bara associera en routningstabell till undernät i virtuella nätverk som finns på samma Azure-plats och i samma prenumeration som routningstabellen.

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
1. Välj det virtuella nätverket i den lista som innehåller det undernät som du vill associera en routningstabell till.
1. Välj **undernät** under **inställningar**.
1. Välj det undernät som du vill associera routningstabellen till.
1. Välj **routningstabellen**, Välj routningstabellen som du vill associera med undernätet och välj **spara**.

Om ditt virtuella nätverk är anslutet till en Azure VPN-gateway, associerar du inte en routingtabell till det [gatewayundernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med målet 0.0.0.0/0. Om du gör det så förhindrar du gatewayen från att fungera korrekt. Läs mer om hur du använder 0.0.0.0/0 i ett flöde, [trafikdirigering i virtuella nätverk](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associera en routningstabell - kommandon

* Azure CLI: [az network vnet-undernät update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Avassociera en routningstabell från ett undernät

När du koppla bort en routningstabell från ett undernät Azure dirigerar trafik baserat på dess [systemstandardvägar](virtual-networks-udr-overview.md#default).

1. Ange i sökrutan överst på portalen *virtuella nätverk* i sökrutan. När **virtuella nätverk** visas i sökresultaten, markerar du den.
1. Välj det virtuella nätverket som innehåller det undernät du vill koppla bort en routningstabell från.
1. Välj **undernät** under **inställningar**.
1. Välj det undernät som du vill koppla bort från routningstabellen.
1. Välj **routningstabellen**väljer **ingen**och välj sedan **spara**.

### <a name="dissociate-a-route-table---commands"></a>Koppla bort en routningstabell - kommandon

* Azure CLI: [az network vnet-undernät update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Ta bort en routningstabell

Om en routingtabell är kopplad till undernät kan den inte tas bort. [Koppla bort](#dissociate-a-route-table-from-a-subnet) en routningstabell från alla undernät innan du försöker ta bort den.

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj **...**  till höger i routningstabellen som du vill ta bort.
1. Välj **ta bort**, och välj sedan **Ja**.

### <a name="delete-a-route-table---commands"></a>Ta bort en routningstabell - kommandon

* Azure CLI: [ta bort az network route-table](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Skapa en väg

Det finns en gräns för hur många vägar vägtabell kan skapa per Azure-plats och prenumeration. Läs mer i informationen om [begränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj i listan som du vill lägga till en väg till routningstabellen.
1. Välj **vägar**under **inställningar**.
1. Välj **+ Lägg till**.
1. Ange ett unikt **namn** för vägen i routningstabellen.
1. Ange den **adressprefix**, i CIDR-notation, som du vill dirigera trafik till. Prefixet kan inte dupliceras i mer än en väg i routningstabellen, även om prefixet som kan finnas i ett annat prefix. Om du har definierat 10.0.0.0/16 som ett prefix i ett flöde, kan du fortfarande definierar en annan väg med adressprefixet 10.0.0.0/24. Azure väljer en väg för trafik baserat på längsta prefix-matchningen. Läs mer om hur Azure väljer vägar i [routningsöversikten](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Välj en **nästa hopptyp**. En detaljerad beskrivning av alla nästa hopptyper Se [routningsöversikten](virtual-networks-udr-overview.md).
1. Ange en IP-adress för **nexthop-adress**. Du kan bara ange en adress om du har valt *virtuell installation* för **nästa hopptyp**.
1. Välj **OK**.

### <a name="create-a-route---commands"></a>Skapa en väg - kommandon

* Azure CLI: [az network route-table route skapa](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Visa flöden

Noll eller flera vägar innehåller en routningstabell. Läs mer om vilken information som visas när du visar vägar i [routningsöversikten](virtual-networks-udr-overview.md).

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj i listan som du vill visa vägar för routningstabellen.
1. Välj **vägar** under **inställningar**.

### <a name="view-routes---commands"></a>Visa flöden - kommandon

* Azure CLI: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Visa information om en väg

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj routningstabellen som du vill visa information om en väg för.
1. Välj **vägar**.
1. Välj den väg som du vill visa information om.

### <a name="view-details-of-a-route---commands"></a>Visa information om en väg - kommandon

* Azure CLI: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Ändra en väg

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj routningstabellen som du vill ändra en väg för.
1. Välj **vägar**.
1. Välj den väg som du vill ändra.
1. Ändra befintliga inställningar till sina nya inställningar och välj sedan **spara**.

### <a name="change-a-route---commands"></a>Ändra en väg - kommandon

* Azure CLI: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Ta bort en väg

1. Ange i sökrutan överst på portalen *routningstabeller* i sökrutan. När **routningstabeller** visas i sökresultaten, markerar du den.
1. Välj routningstabellen som du vill ta bort en väg för.
1. Välj **vägar**.
1. I listan över flöden väljer **...**  till höger i flödet som du vill ta bort.
1. Välj **ta bort**och välj sedan **Ja**.

### <a name="delete-a-route---commands"></a>Ta bort en väg - kommandon

* Azure CLI: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Visa effektiva vägar

Gällande routningar för varje nätverksgränssnitt som är kopplat till en virtuell dator är en kombination av routningstabeller som du har skapat och Azures standardvägar dirigerar alla vägar som sprids från lokala nätverk via BGP via en gateway för virtuellt Azure-nätverk. Förstå de effektiva vägarna för ett nätverksgränssnitt är användbart när du felsöker problem med routning. Du kan visa de effektiva vägarna för alla nätverksgränssnitt som är kopplad till en aktiv virtuell dator.

1. Ange namnet på en virtuell dator som du vill visa effektiva vägar för i sökrutan överst på portalen. Om du inte vet namnet på en virtuell dator, ange *virtuella datorer* i sökrutan. När **virtuella datorer** visas i sökresultaten markerar du den och välj en virtuell dator i listan.
1. Välj **nätverk** under **inställningar**.
1. Välj namnet på ett nätverksgränssnitt.
1. Välj **gällande routningar** under **stöd + felsökning**.
1. Granska listan över gällande routningar för att avgöra om det finns rätt väg för där du vill dirigera trafik till. Mer information om nästa hopptyper som visas i den här listan i [routningsöversikten](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Visa effektiva vägar - kommandon

* Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Verifiera routning mellan två slutpunkter

Du kan fastställa nästa hopptyp mellan en virtuell dator och IP-adressen för en annan Azure-resurs, en lokal resurs eller en resurs på Internet. Överordnad Azure routning är användbart när du felsöker problem med routning. Du måste ha en befintlig Network Watcher för att slutföra den här uppgiften. Om du inte har en befintlig Network Watcher kan du skapa en genom att följa stegen i [skapa en Network Watcher-instans](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Ange i sökrutan överst på portalen *nätverksbevakaren* i sökrutan. När **Network Watcher** visas i sökresultatet väljer du posten.
1. Välj **nästa hopp** under **diagnostiska verktyg för nätverk**.
1. Välj din **prenumeration** och **resursgrupp** av den virtuella källdatorn som du vill validera routning från.
1. Välj den **VM**, **nätverksgränssnittet** kopplade till den virtuella datorn, och **källans IP-adress** tilldelats det nätverksgränssnitt som du vill validera Routning från.
1. Ange den **målets IP-adress** som du vill validera routning till.
1. Välj **nästa hopp**.
1. Efter en kort stund returneras information som talar om nästa hopptyp och ID för den väg som dirigeras trafiken. Mer information om nästa hopptyper som visas som returnerades i [routningsöversikten](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Verifiera routning mellan två slutpunkter - kommandon

* Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Behörigheter

Om du vill genomföra åtgärder på routningstabeller och vägar, måste ditt konto tilldelas till den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som visas i följande tabell:

| Åtgärd                                                          |   Namn                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Läsa en routningstabell                                    |
| Microsoft.Network/routeTables/write                             |   Skapa eller uppdatera en routningstabell                        |
| Microsoft.Network/routeTables/delete                            |   Ta bort en routningstabell                                  |
| Microsoft.Network/routeTables/join/action                       |   Associera en routningstabell till ett undernät                   |
| Microsoft.Network/routeTables/routes/read                       |   Läsa en väg                                          |
| Microsoft.Network/routeTables/routes/write                      |   Skapa eller uppdatera en väg                              |
| Microsoft.Network/routeTables/routes/delete                     |   Ta bort en väg                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Hämta den effektiva routningstabellen för ett nätverksgränssnitt |
| Microsoft.Network/networkWatchers/nextHop/action                |   Hämtar nästa hopp från en virtuell dator                           |

## <a name="next-steps"></a>Nästa steg

* Skapa en väg tabell med [PowerShell](powershell-samples.md) eller [Azure CLI](cli-samples.md) exempel på skript eller genom att använda Azure [Resource Manager-mallar](template-samples.md)<br>
* Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk
