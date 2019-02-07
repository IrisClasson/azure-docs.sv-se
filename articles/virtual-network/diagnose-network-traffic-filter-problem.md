---
title: Diagnostisera problem med virtuella nätverk trafik filter | Microsoft Docs
description: Lär dig att diagnostisera problem med virtuella nätverk trafik filter genom att visa de effektiva säkerhetsreglerna för en virtuell dator.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 8b494e3f289d7b3a850a77f7f388cee542c088ed
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821872"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnostisera problem med virtuella nätverk trafik filter

I den här artikeln får lära du att diagnostisera nätverksproblem trafik filter genom att visa Nätverkssäkerhetsregler för nätverkssäkerhetsgrupper (NSG) i säkerhet, som är giltiga för en virtuell dator (VM).

NSG: er kan du styra vilka typer av trafik flödet och från en virtuell dator. Du kan associera en NSG till ett undernät i ett Azure-nätverk, ett nätverksgränssnitt som är kopplat till en virtuell dator, eller båda. Gällande säkerhetsregler tillämpas på ett nätverksgränssnitt är en sammanställning av de regler som finns i Nätverkssäkerhetsgruppen som är kopplad till ett nätverksgränssnitt och undernät som nätverksgränssnittet finns i. Regler i olika NSG: er kan ibland står i konflikt med varandra och påverkar nätverksanslutningen för en virtuell dator. Du kan visa alla gällande säkerhetsregler från NSG: er som tillämpas på den Virtuella datorns nätverksgränssnitt. Om du inte är bekant med virtuella nätverk, nätverksgränssnitt eller NSG-begrepp finns i [översikt över virtuella nätverk](virtual-networks-overview.md), [nätverksgränssnittet](virtual-network-network-interface.md), och [nätverkssäkerhetsöversikt](security-overview.md).

## <a name="scenario"></a>Scenario

Du försöker ansluta till en virtuell dator via port 80 från internet, men om anslutningen misslyckas. För att avgöra varför du inte åtkomst till port 80 från Internet, kan du visa de effektiva säkerhetsreglerna för ett nätverksgränssnitt med Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli).

Stegen nedan förutsätter att du har en befintlig virtuell dator att visa de effektiva säkerhetsreglerna för. Om du inte har en befintlig virtuell dator, först distribuera en [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM du utför uppgifterna i den här artikeln med. Exemplen i den här artikeln gäller för en virtuell dator med namnet *myVM* med ett nätverksgränssnitt med namnet *myVMVMNic*. Det virtuella datorn och ett nätverksgränssnittet finns i en resursgrupp med namnet *myResourceGroup*, och finns i den *USA, östra* region. Ändra värden i steg efter behov för den virtuella datorn som du felsöker problemet för.

## <a name="diagnose-using-azure-portal"></a>Diagnostisera med hjälp av Azure-portalen

1. Logga in på Azure [portal](https://portal.azure.com) med en Azure-konto som har den [behörighet](virtual-network-network-interface.md#permissions).
2. Ange namnet på den virtuella datorn i sökrutan högst upp på Azure-portalen. När namnet på den virtuella datorn visas i sökresultatet väljer du den.
3. Under **inställningar**väljer **nätverk**, enligt följande bild:

   ![Visa säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Reglerna du ser i föregående bild är för ett nätverksgränssnitt med namnet **myVMVMNic**. Du ser att det finns **regler för INKOMMANDE PORTAR** för nätverksgränssnittet från två olika nätverkssäkerhetsgrupper:
   
   - **mySubnetNSG**: Är kopplat till det undernät som nätverksgränssnittet finns i.
   - **myVMNSG**: Som är kopplad till nätverksgränssnittet i den virtuella datorn med namnet **myVMVMNic**.

   Regeln med namnet **DenyAllInBound** är vad förhindrar inkommande kommunikation till den virtuella datorn via port 80, från internet, enligt beskrivningen i den [scenariot](#scenario). Regeln listorna *0.0.0.0/0* för **källa**, som innehåller internet. Ingen annan regel med högre prioritet (lägre nummer) tillåter port 80 inkommande. Att tillåta port 80 inkommande till den virtuella datorn från internet, se [lösa ett problem](#resolve-a-problem). Läs mer om säkerhetsregler och hur Azure tillämpar dem i [Nätverkssäkerhetsgrupper](security-overview.md).

   Längst ned i bilden du också se **regler för utgående PORT**. Under som visas i regler för utgående portar för nätverksgränssnittet. Även om bilden illustrerar bara fyra regler för inkommande trafik för varje NSG, kan dina NSG: er ha många fler än fyra regler. Du ser i bilden **VirtualNetwork** under **källa** och **mål** och **AzureLoadBalancer** under  **KÄLLAN**. **VirtualNetwork** och **AzureLoadBalancer** är [tjänsttaggar](security-overview.md#service-tags). Tjänsttaggar representerar en grupp med IP-adressprefixen för att minska komplexiteten vid skapande av säkerhetsregler.

4. Se till att den virtuella datorn är igång state och välj sedan **gällande säkerhetsregler**, vilket visas i föregående bild för att se vilka gällande säkerhetsregler som visas i följande bild:

   ![Visa effektiva säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Reglerna som visas är samma som du såg i steg 3, även om det finns olika flikarna för Nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet och undernätet. Som du ser i bilden visas endast de första 50 reglerna. Om du vill hämta en CSV-fil som innehåller alla regler, Välj **hämta**.

   Att se vilket prefix för varje tjänsttagg representerar, Välj en regel, till exempel regeln med namnet **AllowAzureLoadBalancerInbound**. Följande bild visar prefixen för den **AzureLoadBalancer** servicetagg:

   ![Visa effektiva säkerhetsregler](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Även om den **AzureLoadBalancer** tjänsttagg representerar bara ett prefix, andra tjänsttaggar representerar flera prefix.

5. De föregående stegen visade säkerhetsreglerna för ett nätverksgränssnitt med namnet **myVMVMNic**, men du har också fått se ett nätverksgränssnitt med namnet **myVMVMNic2** i några av de föregående bilderna. Den virtuella datorn i det här exemplet har två nätverksgränssnitt som är kopplade till den. Gällande säkerhetsregler kan vara olika för varje nätverksgränssnitt.

   I reglerna för den **myVMVMNic2** nätverksgränssnittet, markerar du den. I bilden nedan visas ett nätverksgränssnitt har samma regler som är kopplad till ett undernät som den **myVMVMNic** nätverksgränssnittet eftersom båda nätverksgränssnitt finns i samma undernät. När du kopplar en NSG till ett undernät, tillämpas reglerna på alla nätverksgränssnitt i undernätet.

   ![Visa säkerhetsregler](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Till skillnad från den **myVMVMNic** nätverksgränssnitt, den **myVMVMNic2** nätverksgränssnittet har inte en nätverkssäkerhetsgrupp som är associerade med den. Varje nätverksgränssnitt och undernät kan ha noll eller en NSG som är kopplad till den. NSG: N som är kopplade till varje nätverksgränssnitt eller undernät kan vara samma, eller en annan. Du kan associera samma nätverkssäkerhetsgruppen till så många nätverksgränssnitt och undernät som du väljer.

Även om gällande säkerhetsregler har visas via den virtuella datorn, kan du också visa gällande säkerhetsregler via en enskild:
- **Nätverksgränssnittet**: Lär dig hur du [visa ett nätverksgränssnitt](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Lär dig hur du [visa en NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnostisera med hjälp av PowerShell

Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn, måste den *AzureRM* PowerShell-modulen version 6.0.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` på datorn, hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också behöva köra `Login-AzureRmAccount` att logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions)].

Hämta effektiva säkerhetsregler för ett nätverksgränssnitt med [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). I följande exempel hämtas de effektiva säkerhetsreglerna för ett nätverksgränssnitt med namnet *myVMVMNic*, som i en resursgrupp med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Utdata returneras i json-format. Information om utdata finns i [tolka kommandoutdata](#interpret-command-output).
Utdata returneras bara om en NSG är associerad med nätverksgränssnittet, det undernät som nätverksgränssnittet finns i eller båda. Den virtuella datorn måste vara i körläge. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG: er som tillämpas. När du felsöker, kör du kommandot för varje nätverksgränssnitt.

Om du fortfarande har anslutningsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnerar ID: N för alla nätverksgränssnitt som är kopplade till en virtuell dator i följande kommandon:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Du får utdata som liknar följande exempel:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

I den föregående utdatan, namnet på nätverksgränssnittet som är *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnostisera med hjälp av Azure CLI

Om du utför uppgifterna i den här artikeln med hjälp av Azure-kommandoradsgränssnittet (CLI)-kommandon antingen köra kommandon den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra CLI från datorn. Den här artikeln kräver Azure CLI version 2.0.32 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör Azure CLI lokalt måste du också behöva köra `az login` och logga in på Azure med ett konto som har den [behörighet](virtual-network-network-interface.md#permissions).

Hämta effektiva säkerhetsregler för ett nätverksgränssnitt med [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). I följande exempel hämtas de effektiva säkerhetsreglerna för ett nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Utdata returneras i json-format. Information om utdata finns i [tolka kommandoutdata](#interpret-command-output).
Utdata returneras bara om en NSG är associerad med nätverksgränssnittet, det undernät som nätverksgränssnittet finns i eller båda. Den virtuella datorn måste vara i körläge. En virtuell dator kan ha flera nätverksgränssnitt med olika NSG: er som tillämpas. När du felsöker, kör du kommandot för varje nätverksgränssnitt.

Om du fortfarande har anslutningsproblem, se [ytterligare diagnos](#additional-diagnosis) och [överväganden](#considerations).

Om du inte känner till namnet på ett nätverksgränssnitt, men vet namnet på den virtuella datorn nätverksgränssnittet är ansluten till, returnerar ID: N för alla nätverksgränssnitt som är kopplade till en virtuell dator i följande kommandon:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

I den returnerade utdatan visas information som liknar följande exempel:

```azurecli
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

I den föregående utdatan, namnet på nätverksgränssnittet som är *myVMVMNic gränssnittet*.

## <a name="interpret-command-output"></a>Tolka kommandoutdata

Oavsett om du använde den [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli) för att felsöka problemet visas utdata som innehåller följande information:

- **NetworkSecurityGroup**: ID för nätverkssäkerhetsgruppen.
- **Associationen**: Om nätverkssäkerhetsgruppen är kopplad till en *NetworkInterface* eller *undernät*. Om en NSG är associerad till båda, utdata returneras med **NetworkSecurityGroup**, **Association**, och **EffectiveSecurityRules**, för varje NSG. Om NSG: N som associeras eller avassocieras omedelbart innan du kör kommandot för att visa de effektiva säkerhetsreglerna, kan du behöva vänta några sekunder innan ändringarna återspeglas i kommandoutdata.
- **EffectiveSecurityRules**: En förklaring av varje egenskap beskrivs i [skapa en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Regeln namn som inleds med *defaultSecurityRules /* är standard säkerhetsregler som finns i varje NSG. Regeln namn som inleds med *securityRules /* är regler som du har skapat. Regler som anger en [tjänsttaggen](security-overview.md#service-tags), till exempel **Internet**, **VirtualNetwork**, och **AzureLoadBalancer** för den  **destinationAddressPrefix** eller **sourceAddressPrefix** egenskaperna kan också ha värden för den **expandedDestinationAddressPrefix** egenskapen. Den **expandedDestinationAddressPrefix** egenskapslistor alla adressprefix som representeras av tjänsttaggen.

Om du ser duplicerade regler som visas i utdata, beror det på att en NSG är associerad till både nätverksgränssnittet och undernätet. Både NSG: er har samma standardregler och kan ha ytterligare duplicerade regler, om du har skapat dina egna regler som är samma i båda NSG: er.

Regeln med namnet **defaultSecurityRules/DenyAllInBound** är vad förhindrar inkommande kommunikation till den virtuella datorn via port 80, från internet, enligt beskrivningen i den [scenariot](#scenario). Ingen annan regel med högre prioritet (lägre nummer) tillåter port 80 för inkommande från internet.

## <a name="resolve-a-problem"></a>Lösa ett problem

Om du använder Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), eller [Azure CLI](#diagnose-using-azure-cli) att diagnosticera problemet visas i den [scenariot](#scenario) i det här artikeln lösningen är att skapa en regel för säkerhet med följande egenskaper:

| Egenskap                 | Värde                                                                              |
|---------                |---------                                                                           |
| Källa                  | Alla                                                                                |
| Källportintervall      | Alla                                                                                |
| Mål             | IP-adressen för den virtuella datorn, ett intervall med IP-adresser eller alla adresser i undernätet. |
| Målportintervall | 80                                                                                 |
| Protokoll                | TCP                                                                                |
| Åtgärd                  | Tillåt                                                                              |
| Prioritet                | 100                                                                                |
| Namn                    | Allow-HTTP-All                                                                     |

När du har skapat regeln port 80 tillåts inkommande från internet, eftersom regelns prioritet är högre än standardsäkerhetsregel som heter *DenyAllInBound*, som nekar trafik. Lär dig hur du [skapa en säkerhetsregel](manage-network-security-group.md#create-a-security-rule). Om olika NSG: er är kopplade till både nätverksgränssnittet och undernätet, måste du skapa samma regel i båda NSG: er.

När Azure bearbetar inkommande trafik, den bearbetar regler i NSG: N som är associerade till undernät (om det inte finns någon associerad NSG) och sedan den bearbetar regler i NSG: N som är kopplad till nätverksgränssnittet. Om det finns en NSG som är kopplad till nätverksgränssnittet och undernätet, måste porten vara öppna i båda NSG: er, för trafik till den virtuella datorn. För att underlätta administration och kommunikation problem rekommenderar vi att du kopplar en NSG till ett undernät i stället för enskilda nätverksgränssnitt. Om virtuella datorer i ett undernät behöver olika säkerhetsregler kan du göra nätverket gränssnitt medlemmar i en programsäkerhetsgrupp (ASG) och ange en ASG som källa och mål för en säkerhetsregel. Läs mer om [programsäkerhetsgrupper](security-overview.md#application-security-groups).

Om du fortfarande har problem, se [överväganden](#considerations) och ytterligare diagnos.

## <a name="considerations"></a>Överväganden

Tänk på följande när du felsöker problem med nätverksanslutningen:

* Standardsäkerhetsregler blockera inkommande åtkomst från internet och endast tillåta inkommande trafik från det virtuella nätverket. Lägga till säkerhetsregler med högre prioritet än standardreglerna tillåter inkommande trafik från Internet. Läs mer om [standardsäkerhetsregler](security-overview.md#default-security-rules), eller hur du [lägga till en säkerhetsregel](manage-network-security-group.md#create-a-security-rule).
* Om du har peerkopplade virtuella nätverk som standard den **VIRTUAL_NETWORK** tjänsttagg expanderar automatiskt om du vill inkludera prefix för peer-kopplade virtuella nätverk. För att felsöka eventuella problem som rör det virtuella nätverkets peering kan du visa adressprefix i den **ExpandedAddressPrefix** lista. Läs mer om [virtuell nätverkspeering](virtual-network-peering-overview.md) och [tjänsttaggar](security-overview.md#service-tags).
* Reglerna för effektiva visas endast för ett nätverksgränssnitt om det finns en NSG som associeras med den Virtuella datorns nätverksgränssnitt och, eller undernät, och om den virtuella datorn är i körläge.
* Om det finns inga NSG: er som är associerad med undernätet eller nätverksgränssnittet och du har en [offentliga IP-adressen](virtual-network-public-ip-address.md) tilldelat till en virtuell dator, alla portar är öppna för inkommande åtkomst från och utgående åtkomst till alla platser. Om den virtuella datorn har en offentlig IP-adress, rekommenderar vi tillämpar en NSG till undernätet för nätverksgränssnittet.

## <a name="additional-diagnosis"></a>Ytterligare diagnos

* Kör ett snabbtest för att avgöra om trafik tillåts till eller från en virtuell dator med den [IP-flödesverifieringen](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funktion i Azure Network Watcher. Kontrollera IP-flöde berättar om trafik tillåts eller nekas. Om nekad, kontrollera IP-flöde talar om vilka säkerhetsregel nekas trafiken.
* Om det finns inga säkerhetsregler som orsakar en virtuell dator är ansluten till nätverket misslyckas kan bero det på grund av:
  * Brandväggsprogram som körs i den Virtuella datorns operativsystem
  * Vägar som konfigurerats för virtuella installationer eller lokal trafik. Internet-trafik kan omdirigeras till ditt lokala nätverk via [Tvingad tunneltrafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du tvingar Internettrafik tunnel till en virtuell installation eller lokalt, kan du kanske inte ansluta till den virtuella datorn från internet. Läs hur du felsöker vägen problem som kan störa trafikflödet från den virtuella datorn i [diagnostisera en virtuell dator trafik problem med nätverksroutning](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om alla aktiviteter, egenskaper och inställningar för en [nätverkssäkerhetsgrupp](manage-network-security-group.md#work-with-network-security-groups) och [säkerhetsregler](manage-network-security-group.md#work-with-security-rules).
- Lär dig mer om [standardsäkerhetsregler](security-overview.md#default-security-rules), [tjänsttaggar](security-overview.md#service-tags), och [hur Azure bearbetar säkerhetsreglerna för inkommande och utgående trafik](security-overview.md#network-security-groups) för en virtuell dator.
