---
title: Tillgänglighet för SAP HANA på Azure virtuella datorer – översikt | Microsoft Docs
description: Beskriver SAP HANA-åtgärder på interna virtuella Azure-datorer.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1db56ad31991b85ffad415818c7c67f0ee30808d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60708455"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA, hög tillgänglighet för Azure-datorer

Du kan använda ett flertal funktioner för Azure för att distribuera verksamhetskritiska databaser som SAP HANA på Azure Virtual Machines. Den här artikeln innehåller råd om hur du uppnår tillgänglighet för SAP HANA-instanser som är värdar för virtuella Azure-datorer. Artikeln beskriver flera scenarier som kan implementeras med hjälp av Azure-infrastrukturen för att öka tillgängligheten för SAP HANA i Azure. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här artikeln förutsätter att du är bekant med infrastruktur som en tjänst (IaaS) grunderna i Azure, inklusive: 

- Hur du distribuerar virtuella datorer eller virtuella nätverk via Azure portal eller PowerShell.
- Med Azures plattformsoberoende kommandoradsgränssnitt (Azure CLI), inklusive möjligheten att använda mallar i JavaScript Object Notation (JSON).

Den här artikeln förutsätter också att du är bekant med att installera SAP HANA-instanser och administrerar och drift av SAP HANA-instanser. Det är särskilt viktigt att känna till installationen och driften av HANA-systemreplikering. Detta omfattar till exempel säkerhetskopiering och återställning för SAP HANA-databaser.

Dessa artiklar innehåller en bra översikt över med SAP HANA i Azure:

- [Manuell installation av en instans SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Konfigurera SAP HANA-systemreplikering på virtuella Azure-datorer](sap-hana-high-availability.md)
- [Säkerhetskopiering av SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

Det är också bra att känna till de här artiklarna om SAP HANA:

- [Hög tillgänglighet för SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Vanliga frågor och svar: Hög tillgänglighet för SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Utföra systemreplikering för SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 Service Pack 01 vad nya: hög tillgänglighet](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Nätverksrekommendationer för SAP HANA-systemreplikering](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA-tjänsten automatisk omstart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurera SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Utöver att bekant med att distribuera virtuella datorer i Azure, innan du definierar din arkitektur för tillgänglighet i Azure rekommenderar vi att du läser [hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="service-level-agreements-for-azure-components"></a>Serviceavtal för Azure-komponenter

Azure har olika tillgänglighets-SLA för olika komponenter, t.ex. nätverks-, lagrings- och virtuella datorer. Alla serviceavtal dokumenteras. Mer information finns i [serviceavtal för Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

[SERVICEAVTAL för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) beskriver två olika serviceavtal för två olika konfigurationer:

- En enskild virtuell dator som använder [Azure premium SSD](../../windows/disks-types.md) för OS-disken och alla datadiskar. Det här alternativet innehåller en månatlig drifttid på 99,9%.
- Flera (minst två) virtuella datorer som är ordnade i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Det här alternativet innehåller en månatlig drifttid på 99,95 procent.

Mät dina krav på tillgänglighet mot de serviceavtal som ger Azure-komponenter. Välj dina scenarier för SAP HANA att uppnå dina krävs tillgänglighet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [SAP HANA-tillgänglighet inom en Azure-region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region).
- Lär dig mer om [tillgänglighet för SAP HANA i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions). 















  


