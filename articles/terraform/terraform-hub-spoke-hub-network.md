---
title: Självstudie – Skapa ett virtuellt Hubbs nätverk i Azure med terraform
description: Självstudie som illustrerar hur du skapar ett hubb-VNet i Azure som fungerar som en gemensam kopplings punkt mellan andra nätverk
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 231f8c6b98db785f3ef155af271be7e354998d54
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969426"
---
# <a name="tutorial-create-a-hub-virtual-network-in-azure-using-terraform"></a>Självstudie: skapa ett virtuellt hubb nätverk i Azure med terraform

Det virtuella hubb-nätverket (VNet) fungerar som central punkt för anslutning till det lokala nätverket. Det virtuella nätverket är värd för delade tjänster som förbrukas av arbets belastningar som finns i eker-virtuella nätverk. I demonstrations syfte är inga delade tjänster implementerade i den här självstudien.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att implementera hubbens VNet i hubben för nav-eker
> * Använd terraform för att skapa en Hubbs hopp ruta för virtuell dator
> * Använd terraform för att skapa hubb för virtuella privata nätverk Gateway
> * Använd terraform för att skapa hubb och lokala gateway-anslutningar

## <a name="prerequisites"></a>Krav

1. [Skapa en nav-och eker hybrid nätverkstopologi med terraform i Azure](./terraform-hub-spoke-introduction.md).
1. [Skapa lokalt virtuellt nätverk med terraform i Azure](./terraform-hub-spoke-on-prem.md).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Hubbs nätverket består av följande komponenter:

- Hubb virtuellt nätverk
- Hubb för virtuell nätverksgateway
- Hubb Gateway-anslutningar 

Följande konfigurations fil för terraform definierar resurserna:

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-vnet"></a>Deklarera hubbens VNet

Skapa konfigurations filen terraform som deklarerar det virtuella hubb nätverket.

1. I Cloud Shell skapar du en fil som heter `hub-vnet.tf`.

    ```bash
    code hub-vnet.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = local.hub-resource-group
      location = local.hub-location
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub
        subnet_id                     = azurerm_subnet.hub-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-hub
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name   = azurerm_resource_group.hub-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-hub
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.hub-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.hub-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = azurerm_virtual_network_gateway.hub-vnet-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.onprem-vpn-gateway.id

      shared_key = local.shared-key
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = azurerm_virtual_network_gateway.onprem-vpn-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.hub-vnet-gateway.id

      shared_key = local.shared-key
    }
    ```
    
1. Spara filen och avsluta redigeraren.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en hubb för virtuella nätverk med terraform i Azure](./terraform-hub-spoke-hub-nva.md))