---
title: Vanliga frågor och svar – Azure Monitor för SAP-lösningar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Monitor för SAP-lösningar
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: cf0366300c4fab18a0f6231a97ca050eddd50132
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852436"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Vanliga frågor och svar om Azure Monitor för SAP-lösningar (för hands version)
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Monitor för SAP-lösningar.  

 - **Måste jag betala för Azure Monitor för SAP-lösningar?**  
Det finns ingen licens avgift för Azure Monitor för SAP-lösningar.  
Kunder är dock ansvariga för att bära kostnaden för hanterade resurs grupps komponenter.  

 - **I vilka regioner är den här tjänsten tillgänglig för för hands version?**  
För offentlig för hands version är tjänsten tillgänglig i USA, östra 2, västra USA 2, östra USA och Västeuropa.  

 - **Måste jag ange behörigheter för att tillåta distribution av hanterade resurs grupper i min prenumeration?**  
Nej, explicita behörigheter krävs inte.  

 - **Var finns den virtuella datorn för insamlaren?**  
När du distribuerar Azure Monitor för SAP-lösningar, rekommenderar vi att kunderna väljer samma VNET för övervakning av resurser som deras SAP HANA-Server. Därför rekommenderas den virtuella insamlaren att finnas i samma VNET som SAP HANA Server. Om kunder använder en icke-HANA-databas finns den virtuella datorn i samma VNET som en databas som inte är HANA-databas.  

 - **Vilka versioner av HANA stöds?**  
HANA 1,0 SPS 12 (Rev. 120 eller högre) och HANA 2,0 SPS03 eller högre  

 - **Vilka HANA-konfigurationer stöds?**  
Följande konfigurationer stöds:
   - En nod (skala upp) och flera noder (skala ut)  
   - Behållare för enkel databas (HANA 1,0 SPS 12) och flera databas behållare (HANA 1,0 SPS 12 eller HANA 2,0)  
   - Redundans för automatisk värd (n + 1) och HSR  

 - **Vilka SQL Server versioner stöds?**  
SQL Server 2012 SP4 eller senare.  

 - **Vilka SQL Server konfigurationer stöds?**  
Följande konfigurationer stöds:
   - Standard eller namngivna fristående instanser i en virtuell dator  
   - Klustrade instanser eller instanser i en AlwaysOn-konfiguration när antingen använder det virtuella namnet för den klustrade resursen eller AlwaysOn-lyssnar namnet. För närvarande samlas inga kluster eller AlwaysOn-/regionsspecifika mått in    
   - Azure SQL Database (PAAS) stöds inte för närvarande  

 - **Vad händer om jag oavsiktligt tar bort den hanterade resurs gruppen?**  
Den hanterade resurs gruppen är låst som standard. Därför är risken för oavsiktlig borttagning av den hanterade resurs gruppen av kunder minuscule.  
Om en kund tar bort den hanterade resurs gruppen slutar Azure Monitor för SAP-lösningar att fungera. Kunden måste distribuera en ny Azure Monitor för SAP Solutions-resursen och börja om.  

 - **Vilka roller behöver jag i min Azure-prenumeration för att distribuera Azure Monitor för SAP Solutions-resurser?**  
Deltagar roll.  

 - **Vad är service avtalet för den här produkten?**  
För hands versioner är exkluderade från service nivå avtal. Läs den fullständiga licens termen genom Azure Monitor för SAP-lösningar Marketplace-avbildning.  

 - **Kan jag övervaka hela landskapet genom den här lösningen?**  
Du kan för närvarande övervaka HANA-databas, underliggande infrastruktur, kluster med hög tillgänglighet och Microsoft SQL Server i offentlig för hands version.  

 - **Ersätter den här tjänsten SAP Solution Manager?**  
Nej. Kunder kan fortfarande använda SAP Solution Manager för övervakning av affärs processer.  

 - **Vad är värdet för den här tjänsten över traditionella lösningar som SAP HANA cockpit/Studio?**  
Azure Monitor för SAP-lösningar är inte HANA-databas. Azure Monitor för SAP-lösningar stöder även AnyDB.  

## <a name="next-steps"></a>Nästa steg

- Skapa din första Azure Monitor för SAP-lösnings resurs.