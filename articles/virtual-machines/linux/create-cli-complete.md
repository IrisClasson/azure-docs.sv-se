---
title: Skapa en Linux-miljö med Azure CLI | Microsoft Docs
description: Skapa storage, en Linux VM, ett virtuellt nätverk och undernät, en belastningsutjämnare, ett nätverkskort, en offentlig IP-adress och en nätverkssäkerhetsgrupp, allt från grunden med hjälp av Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 6b3f862acd5aba39a7ad6eb0ce2f0a9b4a9e5307
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973669"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Skapa en fullständig Linux-dator med Azure CLI
För att snabbt skapa en virtuell dator (VM) i Azure, kan du använda en enda Azure CLI-kommando som använder standardvärden för att skapa alla nödvändiga resurser. Resurser, till exempel ett virtuellt nätverk, offentlig IP-adress och reglerna för nätverkssäkerhetsgruppen skapas automatiskt. Använd för mer kontroll över din miljö i produktion, du kan skapa de här resurserna förbereds i förväg och sedan lägga till de virtuella datorerna i dem. Den här artikeln vägleder dig genom hur du skapar en virtuell dator och var och en av de stödjande resurserna i taget.

Se till att du har installerat senast [Azure CLI](/cli/azure/install-az-cli2) och inloggad på en Azure-konto med [az-inloggning](/cli/azure/reference-index#az_login).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar *myResourceGroup*, *myVnet*, och *myVM*.

## <a name="create-resource-group"></a>Skapa resursgrupp
En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas innan en virtuell dator och virtuella nätverksresurser. Skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Som standard är utdata från Azure CLI-kommandon i JSON (JavaScript Object Notation). Om du vill ändra standardvärdet utdata till en lista eller tabell, till exempel använda [konfigurera az--utdata](/cli/azure/reference-index#az_configure). Du kan också lägga till `--output` med valfritt kommando under en tid ändras i utdataformat. I följande exempel visas JSON-utdata från den `az group create` kommando:

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät
Nästa du skapar ett virtuellt nätverk i Azure och ett undernät i som du kan skapa dina virtuella datorer. Använd [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create) att skapa ett virtuellt nätverk med namnet *myVnet* med den *192.168.0.0/16* adressprefix. Du också lägga till ett undernät med namnet *mySubnet* med adressprefix *192.168.1.0/24*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

Utdata visar undernätet skapas logiskt i det virtuella nätverket:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
Nu ska vi skapa en offentlig IP-adress med [az nätverket offentliga ip-skapa](/cli/azure/network/public-ip#az_network_public_ip_create). Den här offentliga IP-adressen kan du ansluta till dina virtuella datorer från Internet. Eftersom den är dynamisk, skapa en namngiven DNS-post med den `--domain-name-label` parametern. I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* med DNS-namnet på *mypublicdns*. Eftersom DNS-namnet måste vara unikt, ange ditt eget unika DNS-namn:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Utdata:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Om du vill styra flödet av trafik in och ut ur dina virtuella datorer använda du en nätverkssäkerhetsgrupp för ett virtuellt nätverkskort eller undernät. I följande exempel används [az network nsg skapa](/cli/azure/network/nsg#az_network_nsg_create) att skapa en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Du kan definiera regler som tillåter eller nekar viss trafik. För att tillåta inkommande anslutningar på port 22 (för att aktivera SSH-åtkomst), skapar du en inkommande regel med [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRuleSSH*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Lägg till en annan regel för nätverkssäkerhetsgrupp som tillåter inkommande anslutningar på port 80 (för Internet-trafik). I följande exempel skapas en regel med namnet *myNetworkSecurityGroupRuleHTTP*:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Granska nätverkssäkerhetsgruppen och regler med [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show):

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Utdata:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Skapa ett virtuellt nätverkskort
Virtuella nätverkskort (NIC) är tillgängliga programmässigt eftersom du kan använda regler för deras användning. Beroende på den [VM-storlek](sizes.md), kan du ansluta flera virtuella nätverkskort till en virtuell dator. I följande [az network nic skapa](/cli/azure/network/nic#az_network_nic_create) kommandot skapar du ett nätverkskort med namnet *myNic* och associera den med nätverkssäkerhetsgruppen. Offentliga IP-adress *myPublicIP* är även associerat med det virtuella nätverkskortet.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Utdata:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Tillgänglighetsuppsättningar hjälp spridning dina virtuella datorer över feldomäner och uppdateringsdomäner. Även om du bara skapa en virtuell dator just nu, är det bäst att använda tillgänglighetsuppsättningar för att göra det enklare att expandera i framtiden. 

Feldomäner definierar en gruppering av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard indelade de virtuella datorer som har konfigurerats i din tillgänglighetsuppsättning upp till tre feldomäner. Varje virtuell dator som kör din app påverkas inte av maskinvaruproblem på något av dessa feldomäner.

Uppdateringsdomäner ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas på samma gång. Den ordning i vilken uppdatering domäner startas om kan inte vara i följd under planerat underhåll, men endast en uppdateringsdomän startas om samtidigt.

Azure distribuerar automatiskt virtuella datorer mellan fel- och uppdateringsdomäner när de placeras i en tillgänglighetsuppsättning. Mer information finns i [hantera tillgängligheten för virtuella datorer](manage-availability.md).

Skapa en tillgänglighetsuppsättning för den virtuella datorn med [az vm availability-set skapa](/cli/azure/vm/availability-set#az_vm_availability_set_create). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

Utdata anteckningar feldomäner och uppdateringsdomäner:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Skapa en virtuell dator
Du har skapat till nätverksresurser för Internet-åtkomlig virtuella datorer. Skapa en virtuell dator nu och skydda den med en SSH-nyckel. I det här exemplet ska vi skapa en Ubuntu virtuell dator baserat på senaste LTS. Du kan hitta fler bilder med [az vm bildlista](/cli/azure/vm/image#az_vm_image_list), enligt beskrivningen i [att söka efter Azure VM-avbildningarna](cli-ps-findimage.md).

Ange en SSH-nyckel ska användas för autentisering. Om du inte har ett SSH-offentligt nyckelpar, kan du [skapa dem](mac-create-ssh-keys.md) eller Använd den `--generate-ssh-keys` parametern för att skapa dem åt dig. Om du redan har ett nyckelpar, den här parametern använder befintliga nycklar i `~/.ssh`.

Skapa den virtuella datorn genom att föra alla resurser och information tillsammans med den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. I följande exempel skapas en virtuell dator med namnet *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med DNS-posten som du angav när du skapade den offentliga IP-adressen. Detta `fqdn` visas i utdata när du skapar den virtuella datorn:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Utdata:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Du kan installera NGINX och se att trafiken flödar till den virtuella datorn. Installera NGINX på följande sätt:

```bash
sudo apt-get install -y nginx
```

Om du vill se NGINX-standardplatsen i praktiken, öppna webbläsaren och ange fullständigt domännamn:

![NGINX-standardplatsen på den virtuella datorn](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportera som mall
Vad händer om du vill nu skapa en ytterligare utvecklingsmiljö med samma parametrar eller en produktionsmiljö som matchar det? Resource Manager använder JSON-mallar som definierar parametrarna som för din miljö. Du bygga ut hela miljöer genom att referera till den här JSON-mallen. Du kan [bygga JSON-mallar manuellt](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller exportera en befintlig miljö för att skapa JSON-mallen för dig. Använd [az exportera](/cli/azure/group#az_group_export) att exportera din resursgrupp på följande sätt:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Det här kommandot skapar den `myResourceGroup.json` fil i din aktuella arbetskatalog. När du skapar en miljö från den här mallen kan uppmanas du för alla resursnamn. Du kan fylla i de här namnen i mallfilen genom att lägga till den `--include-parameter-default-value` parametern till den `az group export` kommando. Redigera din JSON-mall för att ange resursnamn, eller [skapar du en parameters.json fil](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) som anger resursnamnen.

Du kan skapa en miljö från din mall med [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create) på följande sätt:

```azurecli
az group deployment create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Du kanske vill läsa [mer om hur du distribuerar från mallar](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Läs mer om hur du inkrementellt uppdatera miljöer, använder du filen parametrar och komma åt mallar från en enda lagringsplats.

## <a name="next-steps"></a>Nästa steg
Nu är du redo att börja arbeta med flera nätverkskomponenter och virtuella datorer. Du kan använda det här exemplet miljö för att bygga ut ditt program med hjälp av kärnkomponenter som introduceras här.
