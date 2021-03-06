---
title: 'Scenario: isolera virtuella nätverk'
titleSuffix: Azure Virtual WAN
description: Scenarier för att isolera virtuella nätverk
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 763a13cf2ecbe845619101bc9e325cc51564260a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553401"
---
# <a name="scenario-isolating-vnets"></a>Scenario: isolera virtuella nätverk

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot är målet att förhindra att virtuella nätverk kan komma åt andra. Detta kallas att isolera virtuella nätverk. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

I det här scenariot förblir arbets belastningen i ett visst VNet isolerad och kan inte kommunicera med andra virtuella nätverk. Virtuella nätverk krävs dock för att komma åt alla grenar (VPN, ER och användarens VPN). För att ta reda på hur många väg tabeller som behövs kan du bygga en anslutnings mat ris. I det här scenariot ser det ut som i följande tabell, där varje cell representerar om en källa (rad) kan kommunicera med ett mål (kolumn):

| Från |   Om du vill |  *Virtuella nätverk* | *Grenar* |
| -------------- | -------- | ---------- | ---|
| Virtuella nätverk     | &#8594;|           |     X    |
| Grenar   | &#8594;|    X     |     X    |

I var och en av cellerna i föregående tabell beskrivs om en virtuell WAN-anslutning ("från"-sidan i flödet, rad rubrikerna) lär sig ett måltema ("till"-sidan i flödet, kolumn rubrikerna i kursiv stil) för ett särskilt trafikflöde.

Den här anslutnings matrisen ger oss två olika rad mönster, som översätts till två väg tabeller. Det virtuella WAN-nätverket har redan en standard väg tabell, så vi behöver en annan routningstabell. I det här exemplet namnger vi routningstabellen **RT_VNET**.

Virtuella nätverk kommer att associeras med den här **RT_VNET** routningstabellen. Eftersom de behöver anslutning till grenar, behöver grenar spridas till **RT_VNET** (annars skulle virtuella nätverk inte lära sig att lära sig prefixen för grenen). Eftersom grenarna alltid är kopplade till standard väg tabellen måste virtuella nätverk spridas till standard väg tabellen. Därför är detta den slutliga designen:

* Virtuella nätverk:
  * Associerad routningstabell: **RT_VNET**
  * Sprider till routningstabeller: **standard**
* Delar
  * Associerad routningstabell: **standard**
  * Sprider till routningstabeller: **RT_VNET** och **standard**

Observera att eftersom endast grenar sprids till routningstabellen **RT_VNET**, kommer de att vara de enda prefix som virtuella nätverk kommer att lära sig, och inte andra virtuella nätverk.

Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

För att konfigurera det här scenariot vidtar du följande steg för att överväga:

1. Skapa en anpassad routningstabell i varje hubb. I exemplet är routningstabellen **RT_VNet**. Information om hur du skapar en routningstabell finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md). Mer information om vägvals tabeller finns i [om virtuell hubb](about-virtual-hub-routing.md).
2. Konfigurera följande inställningar när du skapar tabellen **RT_VNet** väg:

   * **Association**: Välj den virtuella nätverk som du vill isolera.
   * **Spridning**: Välj alternativet för grenar, vilket innebär att förgreningar (VPN/er/P2s)-anslutningar sprider vägar till den här väg tabellen.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Isolerade virtuella nätverk":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
