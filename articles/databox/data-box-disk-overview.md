---
title: Microsoft Azure Data Box Disk – översikt| Microsoft Docs i data
description: Beskriver Azure Data Box Disk, en molnlösning som gör det möjligt att överföra stora mängder data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 067d818b7d23fc0b83cb1d4255bfbb8659149412
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240732"
---
# <a name="what-is-azure-data-box-disk"></a>Vad är Azure Data Box Disk?

Med Microsoft Azure Data Box Disk-lösningen kan du skicka terabyte med lokala data till Azure på ett snabbt, kostnadseffektivt och tillförlitligt sätt. Den säkra dataöverföringen görs snabbare eftersom 1 till 5 SSD-diskar (solid state-hårddisk) levereras till dig. Dessa krypterade diskar på 8 TB skickas till ditt datacenter via regionalt flyg. 

Du kan snabbt konfigurera, ansluta och låsa upp diskarna via Data Box-tjänsten i Azure-portalen. Kopiera dina data till diskar och skicka tillbaka diskarna till Azure. I Azure-datacentret laddas dina data automatiskt upp från enheter till molnet med en snabb, privat nätverksöverföringslänk.

## <a name="use-cases"></a>Användningsfall

Använd Data Box Disk för att överföra flera TB med data i scenarier med ingen eller begränsad nätverksanslutning. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar. 

- **Engångsmigrering** – när stora mängder lokala data flyttas till Azure. Till exempel flyttning av data från offlineband till arkiveringsdata i lågfrekvent Azure-lagring.
- **Inkrementell överföring** – när en inledande massöverföring utförs med hjälp av Data Box-Disk (startvärde) följt av inkrementella överföringar över nätverket. Till exempel används Commvault och Data Box Disk för att flytta säkerhetskopior till Azure. Den här migreringen följs av kopiering av inkrementella data via nätverket till Azure-lagring. 
- **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.

## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Beställning** – skapa en beställning i Azure-portalen och ange leveransinformation och Azure-mållagringskonto för dina data. Om diskar är tillgängliga krypterar, förbereder och levererar Azure diskarna med ett leveransspårnings-ID.

2. **Ta emot** – när diskarna har levererats packar du upp och ansluter diskarna till den dator som har data som ska kopieras. Lås upp diskarna.
    
3. **Kopiera data** – dra och släpp för att kopiera data på diskarna.

4. **Returnera** – förbered och skicka tillbaka diskarna till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från diskarna till Azure. Diskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

Under den här processen meddelas du via e-post om alla statusändringar. Mer information om det detaljerade flödet finns på sidan om att [distribuera Data Box Disks i Azure-portalen](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Fördelar

Data Box Disk har utformats för att flytta stora mängder data till Azure utan att påverka nätverket. Lösningen har följande fördelar:

- **Hastighet** – Data Box Disk använder en USB 3.0-anslutning för att flytta upp till 35 TB data till Azure på mindre än en vecka.   

- **Lätt att använda** – Data Box är en lättanvänd lösning.

    - Diskarna använder USB-anslutning knappt kräver någon installationstid.
    - Diskarna har en liten formfaktor som gör dem enklare att hantera.
    - Diskarna kräver ingen extern ström.
    - Diskarna kan användas med en datacenterserver, stationär dator eller bärbar dator.
    - Lösningen ger slutpunkt till slutpunkt-spårning via Azure-portalen.    

- **Skydd** – Data Box Disk har inbyggt säkerhetsskydd för diskarna, data och tjänsten. 
    - Diskarna är manipulationsresistenta och stöder säker uppdateringsfunktion. 
    - Data på diskarna skyddas alltid med AES 128-bitars kryptering. 
    - Diskarna kan bara låsas upp med en nyckel som tillhandahålls i Azure-portalen. 
    - Tjänsten skyddas av säkerhetsfunktionerna i Azure. 
    - När dina data har laddats upp till Azure rensas diskarna i enlighet med standarderna NIST 800-88r1.  
    
Mer information finns i [Säkerhet och dataskydd i Azure Data Box Disk](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funktioner och specifikationer


| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Vikt                                                  | < 1 kg per låda. Upp till 5 diskar i lådan                |
| Dimensioner                                              | Disk – 2,5-tums SSD |            
| Kablar                                                  | 1 USB 3.1-kabel per disk|
| Lagringskapacitet per beställning                              | 40 TB (ca 35 TB användbart)|
| Disklagringskapacitet                                   | 8 TB (ca 7 TB användbart)|
| Datagränssnitt                                          | USB   |
| Säkerhet                                                | Förkrypterat med BitLocker och säker uppdatering <br> Nyckelskyddade diskar <br> Data hålls alltid krypterade  |
| Dataöverföringshastighet                                      | upp till 430 MB/s beroende på filstorlek      |
|Hantering                                               | Azure Portal |


## <a name="region-availability"></a>Regional tillgänglighet

Information om regiontillgänglighet finns i [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Disk kan också distribueras i Azure Government Cloud. Mer information finns i [Vad är Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).


## <a name="pricing"></a>Prissättning

Mer information om priser finns på [sidan med priser](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Nästa steg

- Gå igenom [kraven för Data Box Disk](data-box-disk-system-requirements.md).
- Förstå [Data Box Disk-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.
