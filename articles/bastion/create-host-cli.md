---
title: Skapa en skydds-värd med hjälp av Azure CLI | Azure-skydds
description: I den här artikeln lär du dig hur du skapar och tar bort en skydds-värd
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255523"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Skapa en Azure skydds-värd med Azure CLI

Den här artikeln visar hur du skapar en Azure skydds-värd med Azure CLI. När du etablerar Azure skydds-tjänsten i det virtuella nätverket är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan också skapa en Azure skydds-värd med hjälp av [Azure Portal](bastion-create-host-portal.md)eller genom att använda [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en skydds-värd

Det här avsnittet hjälper dig att skapa en ny Azure skydds-resurs med hjälp av Azure CLI.

1. Skapa ett virtuellt nätverk och ett Azure skydds-undernät. Du måste skapa Azure skydds-undernätet med namnet Value **AzureBastionSubnet**. Med det här värdet kan Azure veta vilket undernät som skydds-resurserna ska distribueras till. Detta skiljer sig från ett Gateway-undernät. Du måste använda ett undernät med minst/27 eller större undernät (/27,/26 osv.). Skapa **AzureBastionSubnet** utan några routningstabeller eller delegeringar. Om du använder nätverks säkerhets grupper på **AzureBastionSubnet**, se artikeln [arbeta med NSG: er](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Skapa en offentlig IP-adress för Azure-skydds. Den offentliga IP-adressen är den offentliga IP-adressen som skydds-resursen som RDP/SSH kommer att få åtkomst till (via port 443). Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. Skapa en ny Azure skydds-resurs i AzureBastionSubnet för det virtuella nätverket. Det tar ungefär 5 minuter för skydds-resursen att skapa och distribuera.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Nästa steg

* Mer information finns i [vanliga frågor och svar om skydds](bastion-faq.md) .

* Information om hur du använder nätverks säkerhets grupper med Azure skydds-undernätet finns i [arbeta med NSG: er](bastion-nsg.md).
