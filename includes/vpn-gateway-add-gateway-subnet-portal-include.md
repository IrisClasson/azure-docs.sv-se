---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170089"
---
1. På portalen går du till det virtuella Resource Manager-nätverk som du vill skapa en virtuell nätverksgateway för.
2. I avsnittet **Inställningar** på sidan för det virtuella nätverket, klickar du på **Undernät** så att sidan Undernät öppnas.
3. På sidan **Undernät** klickar du på **+Gateway-undernät** längst upp så att sidan **Lägg till undernät** öppnas.

   ![Lägg till gatewayundernätet](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Lägg till gatewayundernätet")
  
4. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. GatewaySubnet-värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera de automatiskt ifyllda värdena för **adressintervall** så att de motsvarar dina konfigurationskrav.

   ![Lägg till gatewayundernät](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Lägg till gatewayundernät")
  
5. Klicka på **OK** längst ned på sidan för att skapa undernätet.
