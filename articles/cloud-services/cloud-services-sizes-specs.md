---
title: Storlekar på virtuella datorer för Azure Cloud-tjänster | Microsoft-dokument
description: Visar de olika storlekarna för virtuella datorer (och ID:er) för Azure-molntjänstwebb- och arbetsroller.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 34cb4282f64544e67b3724699380d1d54fd9b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247480"
---
# <a name="sizes-for-cloud-services"></a>Storlekar för molntjänster
I det här avsnittet beskrivs tillgängliga storlekar och alternativ för molntjänstrollinstanser (webbroller och arbetsroller). Det ger också distributionsöverväganden att vara medveten om när du planerar att använda dessa resurser. Varje storlek har ett ID som du lägger i [tjänstdefinitionsfilen](cloud-services-model-and-package.md#csdef). Priserna för varje storlek är tillgängliga på sidan [Cloud Services-priser.](https://azure.microsoft.com/pricing/details/cloud-services/)

> [!NOTE]
> Information om hur du ser relaterade Azure-begränsningar finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Storlekar för webb- och arbetarrollinstanser
Du kan välja mellan flera standardstorlekar i Azure. Det finns några saker som du bör tänka på när du väljer storlek:

* Virtuella datorer i D-serien är utformade för att köra program som kräver högre beräkningskraft och tillfälliga diskprestanda. Virtuella datorer i D-serien erbjuder snabbare processorer, högre ”minne till kärna”-förhållande och en Solid State-hårddisk (SSD) för den tillfälliga disken. Mer information finns i inlägget om [nya storlekar för virtuella datorer i D-serien](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) i Azure-bloggen.
* Dv3-serien, Dv2-serien, en uppföljning av den ursprungliga D-serien, har en mer kraftfull CPU. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den baseras på den senaste generationens 2,4 GHz Intel Xeon® E5-2673 v3-processor (Haswell) och kan uppnå 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.
* Virtuella datorer i G-serien erbjuder mest minne och körs på värdar som är utrustade med processorer i Intel Xeon E5 V3-familjen.
* Virtuella datorer i A-serien kan distribueras på olika maskinvarutyper och processorer. Storleken begränsas, baserat på maskinvaran, för att erbjuda konsekvent processorprestanda för den löp instans som körs, oavsett vilken maskinvara den distribueras på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn.
* A0-storleken har för hög andel prenumerationer på den fysiska maskinvaran. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

Storleken på den virtuella datorn påverkar priset. Storleken påverkar också den virtuella datorns bearbetnings-, minnes- och lagringskapacitet. Lagringskostnaderna beräknas separat baserat på använda sidor i lagringskontot. Mer information finns i [Prisinformation för Molntjänster](https://azure.microsoft.com/pricing/details/cloud-services/) och [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).

Följande information kan hjälpa dig att välja storlek:

* Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz. Detaljerad information och funderingar om hur du använder dessa storlekar finns i [Hög prestanda beräkna VM-storlekar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dv3-serien, Dv2-serien, D-serien, G-serien, är idealiska för program som kräver snabbare processorer, bättre lokal diskprestanda eller har högre minneskrav. De utgör en kraftfull kombination för många program i företagsklass.
* Vissa av de fysiska värdarna i Azure-datacenter kanske inte stöder större storlekar för virtuell dator, till exempel A5–A11. Det kan leda till att felmeddelandet **det gick inte att konfigurera namnet på den virtuella datorn {datornamn}** eller **att den virtuella datorn {datornamn} kunde skapas** när en befintlig virtuell dator ändras till en ny storlek. skapa en ny virtuell dator i ett virtuellt nätverk som skapats före den 16 april 2013. eller lägga till en ny virtuell dator i en befintlig molntjänst. Se [Fel: "Det gick inte att konfigurera den virtuella datorn"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) i supportforumet för lösningar för varje distributionsscenario.
* Din prenumeration kan även begränsa hur många kärnor som du kan distribuera i vissa storleksfamiljer. Kontakta Azure-supporten om du vill öka en kvot.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
Vi har skapat konceptet med Azure Compute Unit (ACU) för att tillhandahålla ett sätt att jämföra beräkningsprestanda (CPU) över Azure SKU:er och identifiera vilken SKU som mest sannolikt uppfyller dina prestandabehov.  ACU är för närvarande standardiserat på en liten virtuell dator (Standard_A1) och är 100, och alla andra SKU:er representerar ungefär hur mycket snabbare den SKU:n kan köra ett benchmark-standardtest.

> [!IMPORTANT]
> ACU är endast en riktlinje. Resultatet med din arbetsbelastning kan variera.
>
>

<br>

| SKU-familj | ACU/kärna |
| --- | --- |
| [ExtraSmå](#a-series) |50 |
| [Liten-ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [En v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

ACU:er som visas med * använder Intel® Turbo-teknik för att öka processorfrekvensen och prestanda. Prestandaökningens storlek kan variera beroende på storleken på den virtuella datorn, arbetsbelastningen och andra arbetsbelastningar som körs på samma värd.

## <a name="size-tables"></a>Storlekstabeller
Följande tabeller visar storlekarna och den kapacitet som de tillhandahåller.

* Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
* Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
* Datadiskar kan köras i cachelagrat eller icke cachelagrat läge. För diskåtgärder med cachelagrade data anges cacheläget till **ReadOnly** eller **ReadWrite**. För diskåtgärder med icke cachelagrade data anges cacheläget till **Inget**.
* Den maximala nätverksbandbredden är den högsta aggregerade bandbredden som allokeras och tilldelas per typ av virtuell dator. Den maximala bandbredden är en riktlinje som hjälper dig att välja rätt typ av virtuell dator för att säkerställa tillräcklig nätverkskapacitet. När du rör dig mellan Låg, Måttlig, Hög och Mycket hög ökar genomströmningen därefter. Faktiska nätverksprestanda beror på många faktorer, bland annat nätverks- och programbelastningar och programmets nätverksinställningar.

## <a name="a-series"></a>A-serien
| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig förvaring: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmå      | 1         | 0.768        | 20                   | 1 / låg |
| Liten           | 1         | 1.75         | 225                  | 1 / måttlig |
| Medel          | 2         | 3.5          | 490                  | 1 / måttlig |
| Stor           | 4         | 7            | 1000                 | 2 / hög |
| Extrastor      | 8         | 14           | 2040                 | 4 / hög |
| A5              | 2         | 14           | 490                  | 1 / måttlig |
| A6              | 4         | 28           | 1000                 | 2 / hög |
| A7 (S)              | 8         | 56           | 2040                 | 4 / hög |

## <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser
Information och funderingar om hur du använder dessa storlekar finns i [Hög prestanda beräkna VM-storlekar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig förvaring: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2 / hög |
| A9*             |16         | 112          | 1817                 | 4 / mycket hög |
| A10 (på nytt)             |8          | 56           | 1817                 | 2 / hög |
| A11             |16         | 112          | 1817                 | 4 / mycket hög |

\*RDMA kan

## <a name="av2-series"></a>Av2-serien

| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / måttlig                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2 / måttlig                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / hög                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / hög                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2 / måttlig                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / hög                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / hög                     |


## <a name="d-series"></a>D-serien
| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1 / måttlig |
| Standard_D2     | 2         | 7            | 100                  | 2 / hög |
| Standard_D3     | 4         | 14           | 200                  | 4 / hög |
| Standard_D4     | 8         | 28           | 400                  | 8 / hög |
| Standard_D11    | 2         | 14           | 100                  | 2 / hög |
| Standard_D12    | 4         | 28           | 200                  | 4 / hög |
| Standard_D13    | 8         | 56           | 400                  | 8 / hög |
| Standard_D14    | 16        | 112          | 800                  | 8 / mycket hög |

## <a name="dv2-series"></a>Dv2-serien
| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1 / måttlig |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / hög |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / hög |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / hög |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / extremt hög |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / hög |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / hög |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / hög |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / extremt hög |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8 / extremt hög |

## <a name="dv3-series"></a>Dv3-serien

| Storlek            | Processorkärnor | Minne: GiB   | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2 / måttlig |
| Standard_D4_v3  | 4         | 16            | 100                  | 2 / hög |
| Standard_D8_v3  | 8         | 32            | 200                  | 4 / hög |
| Standard_D16_v3 | 16        | 64            | 400                  | 8 / extremt hög |
| Standard_D32_v3 | 32        | 128           | 800                  | 8 / extremt hög |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8 / extremt hög |

## <a name="ev3-series"></a>Ev3-serien

| Storlek            | Processorkärnor | Minne: GiB   | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2 / måttlig |
| Standard_E4_v3  | 4         | 32            | 100                  | 2 / hög |
| Standard_E8_v3  | 8         | 64            | 200                  | 4 / hög |
| Standard_E16_v3 | 16        | 128           | 400                  | 8 / extremt hög |
| Standard_E32_v3 | 32        | 256           | 800                  | 8 / extremt hög |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8 / extremt hög |


## <a name="g-series"></a>G-serien
| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1 / hög |
| Standard_G2     | 4         | 56           | 768                  |2 / hög |
| Standard_G3     | 8         | 112          | 1,536                |4 / mycket hög |
| Standard_G4     | 16        | 224          | 3,072                |8 / extremt hög |
| Standard_G5     | 32        | 448          | 6,144                |8 / extremt hög |

## <a name="h-series"></a>H-serien
Virtuella datorer i Azure H-serien är nästa generations virtuella datorer för databehandling med höga prestanda och är avsedda för höga beräkningsbehov, som molekylär modellering och beräkningsströmningsdynamik. Dessa 8 och 16 kärn-virtuella datorer är byggda på Intel Haswell E5-2667 V3-processorteknik med DDR4-minne och lokal SSD-baserad lagring.

Förutom den imponerande processorkraften erbjuder H-serien olika alternativ för RDMA-nätverk med låg fördröjning med FDR InfiniBand och flera minneskonfigurationer som ger stöd för minnesintensiva beräkningskrav.

| Storlek            | Processorkärnor | Minne: GiB  | Tillfällig lagring (SSD): Gib       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / hög |
| Standard_H16    | 16        | 112          | 2000                 | 8 / mycket hög |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / hög |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / mycket hög |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / mycket hög |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / mycket hög |

\*RDMA kan

## <a name="configure-sizes-for-cloud-services"></a>Konfigurera storlekar för molntjänster
Du kan ange storleken på en rolltyp för en rollinstans som en del av tjänstmodellen som beskrivs av [tjänstdefinitionsfilen](cloud-services-model-and-package.md#csdef). Storleken på rollen avgör antalet CPU-kärnor, minneskapacitet och den lokala filsystemstorleken som allokeras till en instans som körs. Välj rollstorlek baserat på programmets resurskrav.

Här är ett exempel på hur du anger vilken rollstorlek som ska Standard_D2 för en webbrollinstans:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Ändra storleken på en befintlig roll

När arbetsbelastningens typ ändras eller nya vm-storlekar blir tillgängliga kanske du vill ändra storleken på din roll. För att göra det måste du ändra storleken på den virtuella datorn i tjänstdefinitionsfilen (som visas ovan), packa om molntjänsten och distribuera den.

>[!TIP]
> Du kanske vill använda olika VM-storlekar för din roll i olika miljöer (t.ex. test jämfört med produktion). Ett sätt att göra detta är att skapa flera tjänstdefinitionsfiler (.csdef) i projektet och sedan skapa olika molntjänstpaket per miljö under din automatiska version med cspack-verktyget. Mer information om elementen i ett molntjänstpaket och hur du skapar dem finns i [Vad är molntjänstmodellen och hur paketerar jag den?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Få en lista över storlekar
Du kan använda PowerShell eller REST API för att få en lista över storlekar. REST API dokumenteras [här](/previous-versions/azure/reference/dn469422(v=azure.100)). Följande kod är ett PowerShell-kommando som listar alla tillgängliga storlekar för Molntjänster. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azure-prenumerationer, tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).
* Läs mer [om hög prestanda beräkna VM storlekar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för HPC arbetsbelastningar.



