---
title: ta med fil
description: ta med fil
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187259"
---
## <a name="scenario"></a>Scenario

Det här dokumentet används för att visa hur du skapar ett virtuellt nätverk och undernät kan följande scenario:

![VNet-scenario](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

I det här scenariot skapar du ett virtuellt nätverk med namnet **TestVNet**, med det reserverade CIDR-blocket för **192.168.0.0./16**. Det virtuella nätverket innehåller följande undernät: 

* **FrontEnd**, som använder **192.168.1.0/24** som sitt CIDR-block.
* **BackEnd**, som använder **192.168.2.0/24** som sitt CIDR-block.

