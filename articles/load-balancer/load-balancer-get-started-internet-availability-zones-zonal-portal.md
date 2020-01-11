---
title: Skapa en Load Balancer med en zonindelade-frontend-Azure Portal
titleSuffix: Azure Load Balancer
description: Lär dig hur du skapar en Standard Load Balancer med zonindelade-frontend med Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 7395da5bc1bf3829cf652374a9a0bb60b4621042
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894510"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Skapa en Standard Load Balancer med zonindelade-frontend med Azure Portal

Den här artikeln beskriver hur du skapar en offentlig [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) med en IP-konfiguration för zonindelade-frontend. Information om hur tillgänglighetszoner fungerar med Standard Load Balancer finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Skapa en belastningsutjämnare med zonindelad klientdelens IP-adress

1. Från en webbläsare navigerar du till Azure portal: [ https://portal.azure.com ](https://portal.azure.com) och logga in med ditt Azure-konto.
2. Längst upp till vänster på skärmen Välj **skapa en resurs** > **nätverk** > **belastningsutjämnare.**
3. I den **skapa belastningsutjämnare** sidan under **namn** typ **myLoadBalancer**.
4. Under **Typ** väljer du **Offentlig**.
5. Välj under SKU, **Standard**.
6. Klicka på **välja en offentlig IP-adress**, klickar du på **Skapa nytt**, och i **skapa offentlig IP-adress** sidan under namn, typ **myPublicIPZonal**, SKU, Välj **Standard**, tillgänglighetszon, Välj **1**.
    
>[!NOTE] 
> Offentligt IP som skapats i det här steget är av Standard-SKU som standard.

1. För **resursgrupp**, klickar du på **Skapa nytt**, och skriv sedan **myResourceGroupZLB** som namnet på resursgruppen.
1. För **plats**väljer **Västeuropa**, och klicka sedan på **OK**. Nu startar distributionen av lastbalanseraren. Distributionen tar några minuter att slutföra.

    ![Skapa zon – redundanta Standard Load Balancer med Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Nästa steg
- Läs mer om [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).



