---
title: Vad är Azure Application Gateway?
description: Lär dig hur du kan använda Azure Application Gateway för att hantera webbtrafik till din app.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78302584"
---
# <a name="what-is-azure-application-gateway"></a>Vad är Azure Application Gateway?

Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Traditionella lastbalanserare fungerar med transportlagret (OSI lager 4 – TCP och UDP) och dirigera trafik baserat på källans IP-adress och port till en mål-IP-adress och -port.

Application Gateway kan fatta beslut om routning baserat på ytterligare attribut för en HTTP-begäran, till exempel URI-sökväg eller värd rubriker. Du kan till exempel dirigera trafik baserat på inkommande URL. Så om `/images` finns i inkommande URL kan du dirigera trafik till en specifik uppsättning servrar (kallas även pool) som har konfigurerats för avbildningar. Om `/video` finns i URL dirigeras trafiken till en annan pool som är optimerad för videor.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Den här typen av routning kallas belastningsutjämning för programlager (OSI lager 7). Azure Application Gateway kan göra URL-baserad routning med mera.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du behöver hög prestanda, låg latens, Layer-4 belastnings utjämning, se [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Om du letar efter global belastnings utjämning för DNS, se [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera dessa lösningar.
>
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Funktioner

Mer information om Application Gateway funktioner finns i [Azure Application Gateway-funktioner](features.md).

## <a name="pricing-and-sla"></a>Priser och service nivå avtal

Application Gateway pris information finns i [Application Gateway prissättning](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway SLA-information finns i [Application Gateway Service avtal](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="next-steps"></a>Nästa steg

Beroende på dina krav och din miljö kan du skapa ett test Application Gateway med hjälp av antingen Azure Portal, Azure PowerShell eller Azure CLI.

- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure CLI](quick-create-cli.md)
