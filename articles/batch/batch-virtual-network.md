---
title: Etablerar en pool i ett virtuellt nätverk – Azure Batch | Microsoft Docs
description: Hur du skapar en Batch-pool i Azure-nätverk så att beräkningsnoder kan kommunicera säkert med andra virtuella datorer i nätverket, till exempel en filserver.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 2583e7e218e765e0d7745978582e19a5a4fe17ce
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003521"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Skapa en Azure Batch-pool i ett virtuellt nätverk

När du skapar ett Azure Batch-pool kan du etablera poolen i ett undernät för ett [Azure-nätverk](../virtual-network/virtual-networks-overview.md) (VNet) som du anger. Den här artikeln förklarar hur du ställer in en Batch-pool i ett virtuellt nätverk. 

## <a name="why-use-a-vnet"></a>Varför ska jag använda ett virtuellt nätverk?

Ett Azure Batch-pool har inställningar för compute-noder att kommunicera med varandra – till exempel, för att köra aktiviteter med flera instanser. De här inställningarna kräver inte ett separat virtuellt nätverk. Noderna inte kan dock kommunicera med virtuella datorer som inte är en del av Batch-pool, till exempel en server eller en server som standard. Du kan etablera poolen i ett undernät för ett virtuellt Azure nätverk för att tillåta compute-noder i poolen kan kommunicera säkert med andra virtuella datorer eller med ett lokalt nätverk. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Autentisering**. Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory). 

* **Ett Azure VNet**. Se följande avsnitt för VNet-krav och konfiguration. Du kan använda Azure-portalen, Azure PowerShell, Azure-kommandoradsgränssnittet (CLI) eller andra metoder för att förbereda ett virtuellt nätverk med ett eller flera undernät i förväg.  
  * Om du vill skapa en Azure Resource Manager-baserad VNet [skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ett virtuellt Resource Manager-baserade nätverk rekommenderas för nya distributioner och stöds endast på pooler i konfigurationen av virtuella datorn.
  * För att skapa ett klassiskt virtuellt nätverk, se [skapa ett virtuellt nätverk (klassiskt) med flera undernät](../virtual-network/create-virtual-network-classic.md). Ett klassiskt virtuellt nätverk stöds endast i pooler i konfigurationen för molntjänster.

## <a name="vnet-requirements"></a>VNet-krav

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Skapa en pool med ett virtuellt nätverk i portalen

När du har skapat ditt virtuella nätverk och tilldelat ett undernät till det, du kan skapa en Batch-pool med det virtuella nätverket. Följ dessa steg för att skapa en pool från Azure portal: 

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som den resursgrupp som innehåller det virtuella nätverket som du tänker använda. 
2. I den **inställningar** fönstret till vänster, Välj den **pooler** menyalternativ.
3. I den **pooler** väljer den **Lägg till** kommando.
4. På den **Lägg till Pool** fönstret väljer du planerar att använda från den **bildtyp** listrutan. 
5. Välj rätt **utgivare/erbjudande/Sku** för den anpassade avbildningen.
6. Ange de återstående nödvändiga inställningar, inklusive den **nodstorlek**, **reserverade Målnoder**, och **låg prioritet noder**, samt alla önskade valfria inställningar.
7. I **virtuellt nätverk**, Välj det virtuella nätverk och undernät som du vill använda.
  
   ![Lägg till pool med virtuella nätverk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade vägar för Tvingad tunneltrafik

Du kan behöva kraven i din organisation omdirigering (force) Internet-bunden trafik från undernätet tillbaka till din lokala plats för granskning och loggning. Du kan aktivera Tvingad tunneltrafik för undernät i ditt virtuella nätverk. 

För att säkerställa att dina beräkningsnoder för Azure Batch-poolen fungerar i ett virtuellt nätverk som har Tvingad tunneltrafik är aktiverat, måste du lägga till följande [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) för undernätet:

* Batch-tjänsten behöver för att kommunicera med beräkningsnoder i poolen för schemaläggning av uppgifter. Lägg till en användardefinierad väg för varje IP-adress som används av Batch-tjänsten i den region där Batch-kontot finns om du vill aktivera den här kommunikationen. Om du vill hämta listan över IP-adresser för Batch-tjänsten kontaktar du Azure-supporten.

* Se till att all utgående trafik till Azure Storage (mer specifikt URL: er i formatet `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, och `<account>.blob.core.windows.net`) inte har blockerats via ditt lokala nätverk installationen.

När du lägger till en användardefinierad väg, ange rutten för varje relaterade Batch-IP-adressprefix och ange **nästa hopptyp** till **Internet**. Se följande exempel:

![Användardefinierad väg](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Nästa steg

- En detaljerad översikt över Batch finns [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
- Mer information om hur du skapar en användardefinierad väg finns i [skapa en användardefinierad väg - Azure-portalen](../virtual-network/tutorial-create-route-table-portal.md).
