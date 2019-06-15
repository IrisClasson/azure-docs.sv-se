---
title: Anpassa inställningar för Azure Migrate utvärdering | Microsoft Docs
description: Beskriver hur du konfigurerar och kör en utvärdering för att migrera virtuella VMware-datorer till Azure med Azure-Migreringshanteraren
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129831"
---
# <a name="customize-an-assessment"></a>Anpassa en utvärdering

[Azure Migrate](migrate-overview.md) skapar utvärderingar med standardegenskaper. Du kan ändra standardegenskaperna med hjälp av anvisningarna i den här artikeln när du har skapat en utvärdering.


## <a name="edit-assessment-properties"></a>Redigera egenskaperna för utvärdering

1. På sidan **Utvärderingar** i migreringsprojektet väljer du utvärderingen och klickar på **Redigera egenskaper**.
2. Anpassa egenskaperna för utvärdering baserat på följande information:

    **Inställning** | **Detaljer** | **Standard**
    --- | --- | ---
    **Målplats** | Azure-platsen du vill migrera till.<br/><br/> För närvarande stöder Azure Migrate 30 regioner, bland andra: Australien, östra; Australien, sydöstra; Brasilien, södra; Kanada, centrala; Kanada, östra; Indien, centrala; USA, centrala; Kina, östra; Kina, norra; Asien, östra; USA, östra; Tyskland, centrala; Tyskland, nordöstra; USA, östra 2; Japan, östra; Japan, västra; Sydkorea, centrala; Sydkorea, södra; USA, norra centrala; Europa, norra; USA, södra centrala; Asien, sydöstra; Indien, södra; Storbritannien, södra; Storbritannien, västra; US Gov, Arizona; US Gov, Texas; US Gov, Virginia; USA, västra centrala; Europa, västra; Indien, västra; USA, västra och USA, västra 2. |  USA, västra 2 är standardplatsen.
    **Lagringstyp** | Du kan använda den här egenskapen för att ange vilken typ av diskar som du vill flytta till i Azure. För som-on-premises som ändrar storlek, du vill kan du ange måldisktyp som Premium-hanterade diskar eller Standard-hanterade diskar. För prestandabaserade storleksändringar behöver ange du disk måltypen som automatisk, Premium-hanterade diskar eller Standard-hanterade diskar. När du anger lagringstypen som automatisk görs disk rekommendationen baserat på prestandadata för diskar (IOPS och dataflöde). Exempel: Om du vill uppnå en [instans VM SLA på 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), kan du ange lagringstypen som Premium-hanterade diskar. Detta säkerställer att alla diskar i utvärderingen rekommenderas som Premium-hanterade diskar. Observera att Azure Migrate endast stöder hanterade diskar för migreringsutvärdering. | Standardvärdet är Premium-hanterade diskar (med storlekskriteriet som *som lokalt storlek*).
    **Reserverade instanser** |  Du kan också ange om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure så beräknar Azure Migrate kostnaden därefter. Reserverade instanser är för närvarande stöds endast för betala per användning-erbjudandet i Azure Migrate. | Standardvärdet för den här egenskapen är 3 års reserverade instanser.
    **Ändra storlek på kriterium** | Kriteriet som ska användas av Azure Migrate för att ställa in rätt storlek på virtuella datorer för Azure. Du kan antingen göra en *prestandabaserad* storleksändring eller ändra storlek på de virtuella datorerna *som lokalt*, utan att överväga prestandahistorik. | Standardalternativet är prestandabaserad storleksändring.
    **Prestandahistorik** | Tidsperioden att beakta när du utvärderar prestanda för de virtuella datorerna. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*. | Standardvärdet är en dag.
    **Percentilutnyttjande** | Percentilvärdet för prestandaexempeluppsättningen som beaktas för rätt storleksändring. Den här egenskapen gäller bara när storleksändringskriteriet är *prestandabaserad storleksändring*.  | Standardvärdet är 95: e percentilen.
    **VM-serie** | Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Till exempel om du har en produktionsmiljö som du inte planerar att migrera till A-seriens virtuella datorer i Azure kan du exkludera A-serien i listan eller serien och rätt storlek görs endast i den markerade serien. | Som standard markeras alla VM-serier.
    **Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. | Standardinställningen är 1.3x.
    **Erbjudande** | [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) som du är registrerad för. | [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) är standard.
    **Valuta** | Faktureringsvalutan. | Standardvärdet är USD.
    **Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. | Standardinställningen är 0%.
    **VM-drifttid** | Om dina virtuella datorer inte ska som körs 24 x 7 i Azure måste du ange varaktighet (antal dagar per månad) och antalet timmar per dag för som de skulle köra och kostnadsuppskattningar utförs i enlighet med detta. | Standardvärdet är 31 dagar per månad och 24 timmar per dag.
    **Azure Hybrid-förmån** | Ange om du har Software Assurance och är berättigad [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Om inställningen är Ja beaktas andra priser än Windows Azure-priser för virtuella Windows-datorer. | Standardvärdet är Ja.

3. Klicka på **spara** att uppdatera utvärderingen.

## <a name="faqs-on-assessment-properties"></a>Vanliga frågor och svar på utvärderingsegenskaperna

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Vad är skillnaden mellan som lokalt storlek och prestandabaserad storleksändring?

När du anger storlekskriteriet vara som lokalt storlek, Azure Migrate beräknar inte prestandadata för de virtuella datorerna och storlekar på virtuella datorer baserat på den lokala konfigurationen. Om storlekskriteriet är prestandabaserat, görs utskriftsserverns storlek baserat på användningsdata. Anta till exempel att det finns en lokal virtuell dator med 4 kärnor och 8 GB minne med 50 % processoranvändning och en minnesanvändning på 50 %. Om storlekskriteriet är lokal storleksändring rekommenderas en Azure VM SKU med 4 kärnor och 8 GB minne. Om storlekskriteriet däremot är prestandabaserat rekommenderas en VM SKU med 2 kärnor och 4 GB eftersom utnyttjandeprocenten tas i beaktande när storleken rekommenderas.

Diskar beror på samma sätt, disk-storlek på två utvärderingsegenskaperna – ändra storlek på kriterium och lagringstyp. Om storlekskriteriet är prestandabaserat och lagringstypen är automatisk, tas diskens IOPS- och dataflödesvärden i beaktande för att identifiera disktypen (Standard eller Premium). Om storlekskriteriet är prestandabaserat och lagringstypen är Premium, rekommenderas en premiumdisk. SKU för premiumdiskar i Azure väljs baserat på storleken på den lokala disken. Samma logik används för att bestämma diskstorleken när storlekskriteriet är lokal storleksändring och lagringstypen är Standard eller Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Vilken effekt har prestanda historik och percentil belastningen på storleksrekommendationer som?

Dessa egenskaper gäller endast för prestandabaserad storleksändring. Azure Migrate samlar in prestandahistorik för lokala datorer och använder den för att rekommendera VM-storleken och disktypen i Azure.

- Insamlingsprogrammet profilerar kontinuerligt den lokala miljön för att samla in användningsdata i realtid var 20:e sekund.
- Programmet samlar in stickprov var 20:e sekund och skapar en enskild datapunkt för varje kvart. För att skapa den enskilda datapunkten väljer programmet det högsta värdet från alla 20-sekundersstickprov och skickar det till Azure.
- När du skapar en utvärdering i Azure, baserat på percentilen för prestandavaraktighet och prestandahistorik, beräknar Azure Migrate det effektiva användningsvärdet och använder det för storleksändring.

Om du till exempel har ställt in prestandavaraktigheten på 1 dag och percentilvärdet till den 95:e percentilen använder Azure Migrate punkterna från 15-minutersstickproven som skickats av insamlaren den senaste dagen, sorterar dem i stigande ordning och hämtar värdet för den 95:e percentilen som den faktiska användningen. Värdet för den 95:e percentilen ser till att avvikande värden ignoreras, som kan tas med om du väljer den 99:e percentilen. Om du vill välja den högsta användningen för perioden och inte vill missa några avvikande värden bör du välja den 99:e percentilen.

## <a name="next-steps"></a>Nästa steg

[Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
