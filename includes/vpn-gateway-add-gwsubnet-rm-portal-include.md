---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150284"
---
1. I den [Azure-portalen](http://portal.azure.com), Välj det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway.

2. I den **inställningar** på virtuellt nätverkssidan, väljer **undernät** att expandera den **undernät** sidan.

3. På den **undernät** väljer **gatewayundernätet** att öppna den **Lägg till undernät** sidan.

   ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Lägg till gatewayundernätet")

4. Den **namn** för ditt undernät är automatiskt autofilled med värdet *GatewaySubnet*. Det här värdet krävs för Azure för att kunna identifiera undernätet som gateway-undernätet. Justera autofilled **adressintervall** värden så att de matchar konfigurationskraven, välj sedan **OK** att skapa undernätet.

   ![Lägga till undernätet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Lägga till undernätet")