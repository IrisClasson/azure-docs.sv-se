---
title: Kvoter och begränsningar – Azure Batch-tjänsten | Microsoft Docs
description: Lär dig mer om standard Azure Batch-kvoter, gränser och begränsningar för och hur du begär kvot ökar
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: de32ae16ea4d3c52b8017f35ae5af6009ab59205
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080919"
---
# <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten

Som med andra Azure-tjänster, det finns begränsningar för vissa resurser som är associerade med Batch-tjänsten. Många av dessa begränsningar är standardkvoter som tillämpas av Azure-prenumeration eller kontonivå. Den här artikeln beskrivs de standardinställningarna och hur du kan begära en kvot ökar.

Tänk på dessa kvoter du design- och skala upp dina Batch-arbetsbelastningar. Till exempel om din pool inte når antalet beräkningsnoder som du angav kan kanske du har nått grundkvoten för Batch-kontot.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

Om du planerar att köra arbetsbelastningar i produktion i Batch kan du behöva öka en eller flera av kvoter ovanför standardvärdet. Om du vill öka en kvot kan du öppna en online [kundsupport](#increase-a-quota) utan kostnad.

## <a name="resource-quotas"></a>Resurskvoter

En kvot är en kreditgräns, inte en garanti för kapacitet. Kontakta Azure-supporten om du har storskaliga kapacitetsbehoven.

Observera att kvoter inte är garanterad också värden. Kvoter kan variera beroende på ändringar från Batch-tjänsten eller en begäran om att ändra ett kvotvärde.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kärnkvoter i användarprenumerationsläge

Om du har skapat ett Batch-konto med poolallokeringsläget inställt **användarprenumeration**, kvoter tillämpas på olika sätt. I detta läge gäller skapas virtuella datorer i Batch och andra resurser direkt i prenumerationen när en pool skapas. Azure Batch-kärnkvoter gäller inte för ett konto som skapats i det här läget. I stället compute-kvoten i prenumerationen för regionala kärnor och andra resurser tillämpas. Mer information om dessa kvoter i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Storleksgränser för poolen

Storleksgränsen för poolen bestäms av Batch-tjänsten. Till skillnad från [resurskvoter](#resource-quotas), dessa värden kan inte ändras. Endast pooler med kommunikation mellan noder och anpassade avbildningar har begränsningar som skiljer sig från standardkvoten.

| **Resurs** | **Övre gräns** |
| --- | --- |
| **Compute-noder i [mellan noder kommunikation aktiverat pool](batch-mpi.md)**  ||
| Poolallokeringsläget för batch-tjänsten | 100 |
| Poolallokeringsläget för batch-prenumeration | 80 |
| **Compute-noder i [poolen som skapats med en anpassad virtuell datoravbildning](batch-custom-images.md)** <sup>1</sup> ||
| Dedikerade noder | 2000 |
| Lågprioriterade virtuella noder | 1000 |

<sup>1</sup> för pooler som inte är mellan noder kommunikation aktiverat.

## <a name="other-limits"></a>Andra gränser

Ytterligare begränsningar som angetts av Batch-tjänsten. Till skillnad från [resurskvoter](#resource-quotas), dessa värden kan inte ändras.

| **Resurs** | **Övre gräns** |
| --- | --- |
| [Samtidiga uppgifter](batch-parallel-node-tasks.md) per compute-nod | 4 x antalet kärnor för noden |
| [Program](batch-application-packages.md) per Batch-konto | 20 |
| Programpaket per program | 40 |
| Programpaket per pool | 10 |
| Maximal aktiviteternas livslängd | 180 dagar<sup>1</sup> |

<sup>1</sup> högsta livstid för en uppgift, från när den läggs till i jobbet tills den slutförs, är 180 dagar. Slutförda uppgifter finns kvar i sju dagar; data för uppgifter som inte slutförts inom den maximala livstiden är inte tillgänglig.

## <a name="view-batch-quotas"></a>Visa Batch-kvoter

Visa dina kvoter med Batch-konto i den [Azure-portalen][portal].

1. Välj **Batch-konton** i portalen och markera Batch-kontot som du är intresserad av.
1. Välj **kvoter** på menyn för Batch-kontot.
1. Visa kvoter som för närvarande används till Batch-konto

    ![Kvoter för batch-konto][account_quotas]

## <a name="increase-a-quota"></a>Öka en kvot

Följ dessa steg för att begära en kvot öka för ditt Batch-konto eller din prenumeration med hjälp av den [Azure-portalen][portal]. Vilken typ av kvot beror på poolallokeringsläget för Batch-kontot. Om du vill begära en utökad kvot måste du inkludera VM-serierna som du vill öka kvoten för. När kvot tillämpas, tillämpas den på alla serier för virtuella datorer.

### <a name="increase-cores-quota-in-batch"></a>Öka kärnkvoten i Batch 

1. Välj den **hjälp + support** panelen på instrumentpanelen i portalen eller frågetecknet ( **?** ) i det övre högra hörnet i portalen.
1. Välj **ny supportbegäran** > **grunderna**.
1. I **grunderna**:
   
    a. **Typ av problem** > **begränsningar för tjänsten och -prenumeration (kvoter)**
   
    b. Välj din prenumeration.
   
    c. **Typ av kvot** > **Batch**
      
    Välj **Nästa**.
    
1. I **information**:
      
    a. I **ger information om**anger plats, typ av kvot och Batch-konto.
    
    ![Batch-kvot][quota_increase]

    Kvottyper:

    * **Per Batch-konto**  
        Värden som är specifika för en enskild Batch-konto, inklusive dedikerade och lågprioriterade kärnor och antal jobb och pooler.
        
    * **Per region**  
        Värden som gäller för alla Batch-konton i en region och innehåller antalet Batch-konton per region per prenumeration.

    Med låg prioritet kvoten är ett enstaka värde för alla VM-serier. Om du behöver begränsad SKU: er, måste du välja **lågprioritetskärnor** och inkludera VM-familjer att begära.

    b. Välj en **allvarlighetsgrad** enligt din [inverkan på företaget][support_sev].

    Välj **Nästa**.

1. I **kontaktinformation**:
   
    a. Välj en **prioriterade kontaktmetod**.
   
    b. Verifiera och ange nödvändiga kontaktinformation.
   
    Välj **skapa** att skicka en supportförfrågan.

När du har skickat din supportbegäran, Azure-supporten kommer att kontakta dig. Kvotbegäranden kan slutföras inom ett par minuter, eller upp till två arbetsdagar.

## <a name="related-quotas-for-vm-pools"></a>Relaterade kvoter för VM-pooler

Batch-pooler i konfigurationen av virtuell dator distribueras i ett Azure-nätverk automatiskt allokera ytterligare Azure-nätverksresurser. Följande resurser krävs för varje 50 poolnoder i ett virtuellt nätverk:

* En [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md#network-security-groups)
* En [offentlig IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* En [belastningsutjämnare](../load-balancer/load-balancer-overview.md)

Dessa resurser allokeras i den prenumeration som innehåller det virtuella nätverket som anges när du skapar Batch-pool. Dessa resurser begränsas av prenumerationens [resurskvoter](../azure-subscription-service-limits.md). Om du planerar distributioner av stor pool i ett virtuellt nätverk Kontrollera prenumerationens kvoter för dessa resurser. Om det behövs kan du begära en ökning i Azure portal genom att välja **hjälp + support**.


## <a name="related-topics"></a>Relaterade ämnen
* [Skapa ett Batch-konto med Azure portal](batch-account-create-portal.md)
* [Funktionsöversikt för Azure Batch](batch-api-basics.md)
* [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
