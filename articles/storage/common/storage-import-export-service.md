---
title: Använda Azure Import/Export för att överföra data till och från Azure Storage | Microsoft Docs
description: Lär dig hur du skapar importera och exportera jobb i Azure-portalen för att överföra data till och från Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/11/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ccfc91e85196cbc8295bce71e512d9cf856a23c8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453391"
---
# <a name="what-is-azure-importexport-service"></a>Vad är tjänsten Azure Import/Export?

Azure Import/Export-tjänsten används för att importera stora mängder data på ett säkert sätt till Azure Blob storage och Azure Files genom att skicka diskenheter till en Azure-datacenter. Den här tjänsten kan också användas för att överföra data från Azure Blob storage till diskenheter och leverera till dina lokala platser. Data från en eller flera diskenheter kan importeras till Azure Blob storage eller Azure Files. 

Ange du egna enheter och överföra data med Azure Import/Export-tjänsten. Du kan också använda diskenheter som tillhandahålls av Microsoft. 

Om du vill överföra data med hjälp av diskenheter som tillhandahålls av Microsoft, kan du använda [Azure Data Box-Disk](../../databox/data-box-disk-overview.md) att importera data till Azure. Microsoft levereras upp till 5 krypterad disk SSD (solid-state drive) med en total kapacitet 40 TB per beställning till ditt datacenter till ett regionalt flygbolag. Du kan snabbt konfigurera diskenheter, kopiera data till enheter via en USB 3.0-anslutning och leverera diskenheter tillbaka till Azure. Mer information går du till [översikt över Azure Data Box-Disk](../../databox/data-box-disk-overview.md).

## <a name="azure-importexport-usecases"></a>Azure Import/Export-usecases

Överväg att använda Azure Import/Export-tjänsten när överföra eller hämta data över nätverket är långsamt eller få ytterligare nätverksbandbredd är oöverkomligt. Använd den här tjänsten i följande scenarier:

* **Migrering av data till molnet**: Flytta stora mängder data till Azure snabbt och kostnadseffektivt sätt.
* **Distribution av innehåll**: Skicka snabbt data till dina kundplatser.
* **Backup**: Ta säkerhetskopior av dina lokala data och lagra dem i Azure Storage.
* **Dataåterställning**: Återställa stora mängder data som lagras i storage och levereras till din lokala plats.

## <a name="importexport-components"></a>Import/Export-komponenter

Import/Export-tjänsten använder följande komponenter:

- **Import/Export-tjänsten**: Den här tjänsten som är tillgänglig i Azure portal hjälper användaren att skapa och spåra dataimporten (överföringen) och exportera (nedladdning) jobb.  

- **WAImportExport verktyget**: Det här är ett kommandoradsverktyg som gör följande: 
    - Förbereder diskenheterna som levereras för import.
    - Underlättar kopierar dina data till enheten.
    - Krypterar data på enheten med BitLocker.
    - Genererar enhet journalfiler används när import skapas.
    - Hjälper dig att identifiera antal enheter som behövs för exportjobb.
    
> [!NOTE]
> WAImportExport-verktyget är tillgängligt i två versioner, version 1 och 2. Vi rekommenderar att du använder:
> - Version 1 för import/export i Azure Blob storage. 
> - Version 2 för import av data till Azure files.
>
> Verktyget WAImportExport är endast kompatibel med 64-bitars Windows-operativsystem. För specifika OS-versioner som stöds, gå till [krav för Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Diskenheter**: Du kan leverera SSD (solid-state drive) eller hårddiskar (HDD) till Azure-datacentret. När du skapar ett importjobb kan leverera du diskenheter som innehåller dina data. När du skapar ett export-jobb kan leverera du tomma enheter till Azure-datacentret. För specifika disktyper, gå till [stöds disktyper](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hur fungerar Import/Export?

Azure Import/Export-tjänsten kan överföra data i Azure-Blobar och Azure Files genom att skapa jobb. Använda Azure portal eller Azure Resource Manager REST API för att skapa jobb. Varje jobb är associerat med ett enda lagringskonto. 

Jobb som du kan importera eller exportera jobb. Ett importjobb kan du importera data till Azure-Blobbar eller Azure files medan export-jobb gör att data kan exporteras från Azure Blobs. För ett importjobb leverera du enheter som innehåller dina data. När du skapar ett export-jobb kan leverera du tomma enheter till en Azure-datacenter. I båda fallen kan skicka du upp till 10 enheter per jobb.

### <a name="inside-an-import-job"></a>I ett importjobb

Ett importjobb omfattar följande steg på hög nivå:

1. De data som ska importeras, antalet enheter som du behöver, blob-målplatsen för dina data i Azure storage.
2. Verktyget WAImportExport används för att kopiera data till diskenheter. Kryptera diskenheter med BitLocker.
3. Skapa ett importjobb i mål-lagringskontot i Azure-portalen. Ladda upp journalfiler enhet.
4. Ange avsändaradressen och transportföretagets kontonummer för enheterna på väg tillbaka till dig.
5. Leverera diskenheter till leveransadressen som angavs under skapande av jobb.
6. Uppdatera leveransen spårningsnummer i jobbinformation för import och skicka importjobbet.
7. Enheterna tas emot och bearbetas i Azure-datacentret.
8. Enheterna levereras med ditt operatör till avsändaradressen i importjobbet.

> [!NOTE]
> Lokal (inom data center land) leveranser dela en inrikes transportföretagskonto 
>
> Utomlands (utanför data center land) leveranser dela en internationell transportföretagskonto

 ![Bild 1:Import jobbet flöde](./media/storage-import-export-service/importjob.png)

Stegvisa instruktioner om data Importera, gå till:

- [Importera data till Azure-Blobar](storage-import-export-data-to-blobs.md)
- [Importera data till Azure Files](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>I ett exportjobb

> [!IMPORTANT]
> Tjänsten stöder bara export av Azure-Blobar. Export av Azure files stöds inte.

På hög nivå omfattar ett exportjobb följande steg:

1. De data som exporteras, antal enheter du behöver, käll-blobbar eller sökvägarna till behållaren för dina data i Blob storage.
3. Skapa ett exportjobb i din källagringskontot i Azure-portalen.
4. Ange källa BLOB eller behållare sökvägar för data som ska exporteras.
5. Ange avsändaradressen och transportföretagets kontonummer för enheterna på väg tillbaka till dig.
6. Leverera diskenheter till leveransadressen som angavs under skapande av jobb.
7. Uppdatera leveransen spårningsnummer i jobbinformation för export och skicka export-jobbet.
8. Enheterna tas emot och bearbetas i Azure-datacentret.
9. Enheterna som är krypterade med BitLocker och nycklarna som är tillgängliga via Azure portal.  
10. Enheterna levereras med ditt operatör till avsändaradressen i importjobbet.

> [!NOTE]
> Lokal (inom data center land) leveranser dela en inrikes transportföretagskonto 
>
> Utomlands (utanför data center land) leveranser dela en internationell transportföretagskonto
  
 ![Bild 2:Export jobbet flöde](./media/storage-import-export-service/exportjob.png)

Stegvisa anvisningar för export av data, går du till [exportera data från Azure Blobs](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Regional tillgänglighet 

Azure Import/Export-tjänsten har stöd för kopiering av data till och från alla Azure storage-konton. Du kan leverera enheter till en av de angivna platserna. Om ditt lagringskonto är i en Azure-plats som inte anges här, tillhandahålls en alternativa leveransplats när du skapar jobbet.

### <a name="supported-shipping-locations"></a>Endash platser som stöds


|Land/region  |Land/region  |Land/region  |Land/region  |
|---------|---------|---------|---------|
|Östra USA    | Norra Europa        | Indien, centrala        |US Gov, Iowa         |
|Västra USA     |Västra Europa         | Södra Indien        | US DoD, östra        |
|USA, östra 2    | Östasien        |  Indien, västra        | US DoD, centrala        |
|Västra USA 2     | Sydostasien        | Centrala Kanada        | Östra Kina         |
|Centrala USA     | Östra Australien        | Östra Kanada        | Norra Kina        |
|Norra centrala USA     |  Sydöstra Australien       | Södra Brasilien        | Storbritannien, södra        |
|Södra centrala USA     | Västra Japan        |Sydkorea, centrala         | Centrala Tyskland        |
|Västra centrala USA     |  Östra Japan       | Virginia (USA-förvaltad region)        | Nordöstra Tyskland        |


## <a name="security-considerations"></a>Säkerhetsöverväganden

Data på enheten krypteras med BitLocker-diskkryptering. Den här krypteringen skyddar dina data i rörelse.

För importjobb krypteras enheter på två sätt.  


- Ange önskat alternativ när du använder *dataset.csv* filen när du kör verktyget WAImportExport vid förberedelsen av enheten. 

- Aktivera BitLocker-kryptering manuellt på enheten. Ange krypteringsnyckeln i den *driveset.csv* när du kör kommandoraden för WAImportExport verktyget vid förberedelsen av enheten.


För export-jobb krypterar tjänsten enheten med BitLocker innan levereras till dig när dina data kopieras till enheterna. Krypteringsnyckeln får du via Azure portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Prissättning

**Driv hanteringsavgift**

Det finns en avgift för hantering av enhet för varje enhet som behandlas som en del av din importera eller exportera jobbet. Se informationen om den [priser för Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Leveranskostnader**

När du skickar enheter till Azure betalar du kostnaden för leverans till transportföretaget. När Microsoft återgår enheterna till dig, debiteras kostnaden för leverans till transportföretagskonto som du angav vid tidpunkten för skapande av jobb.

**Transaktionskostnader**

Det finns inga transaktionskostnader förutom standardlagring transaktionskostnader när du importerar data till Azure Storage. Kostnader för standard utgående trafik gäller när data exporteras från Blob storage. Läs mer på transaktionskostnader [prisinformation om dataöverföring.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder Import/Export-tjänsten till:
* [Importera data till Azure-Blobar](storage-import-export-data-to-blobs.md)
* [Exportera data från Azure Blobs](storage-import-export-data-from-blobs.md)
* [Importera data till Azure Files](storage-import-export-data-to-files.md)

