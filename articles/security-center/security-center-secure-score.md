---
title: Skydda poäng i Azure Security Center | Microsoft Docs
description: " Prioritera dina säkerhetsrekommendationer som använder säker poängen i Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: v-mohabe
ms.openlocfilehash: c23c9a2b9af1947c397b96431ae3c3bcd0e30aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968301"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Förbättra dina säker poäng i Azure Security Center


Med så många tjänster som erbjuder säkerhetsfördelarna, är det ofta svårt att veta vilka steg du ska vidta först att skydda och förstärka din arbetsbelastning. Azure säker poängen går igenom din säkerhetsrekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer för att utföra först. Detta hjälper dig att hitta de mest allvarliga säkerhetsriskerna, så att du kan prioritera undersökningen. Säker poängen är ett verktyg som hjälper dig att bedöma din arbetsbelastning säkerhetsposition.

## <a name="secure-score-calculation"></a>Skydda poängberäkningen

Security Center imiterar verk som tillhör en säkerhetsanalytiker, granska dina säkerhetsrekommendationer och tillämpa avancerade algoritmer att avgöra hur viktigt är varje rekommendation.
Azure Security center ständigt granskar du aktiva rekommendationer och beräknar dina säker poäng som baseras på dem, poängen för en rekommendation härleds från dess allvarlighetsgrad och säkerhetsmetoder som påverkar säkerheten för din arbetsbelastning mest.

Security Center innehåller också en **övergripande skyddar score**. 

**Skydda övergripande poäng** är en anhopning av ditt rekommendation resultat. Du kan visa din övergripande säker poäng över dina prenumerationer och hanteringsgrupper, beroende på vad du väljer. Poängen varierar beroende på prenumerationen som valts och aktiva rekommendationer för dessa prenumerationer.

 
Om du vill kontrollera vilka rekommendationer påverka säker poängen mest, du kan visa de tre översta störst rekommendationerna i instrumentpanelen i Security Center eller du kan sortera rekommendationerna i den rekommendationer bladet med hjälp av den **säker poäng påverkan** kolumn.


Visa dina övergripande säker poäng:

1. I Azure-instrumentpanelen klickar du på **Security Center** och klicka sedan på **säker poäng**.
2. Överst ser du Secure bedöma viktiga funktioner:
   - **Övergripande säkerhetspoäng** motsvarar antal poäng per principer per vald prenumeration
   - **Skydda poäng efter kategori** visar vilka resurser behöver mest åtgärdas
   - **Främsta rekommendationer av säker poäng inverkan** tillhandahåller en lista över rekommendationer som förbättrar dina säker poäng mest om du implementerar dem.
 
   ![säker poäng](./media/security-center-secure-score/secure-score-dashboard.png)

3. Du kan se var och en av dina prenumerationer och den övergripande säker poängen för varje i tabellen nedan.

   > [!NOTE]
   > Summan av säkerhetspoängen för varje prenumeration är inte lika med den övergripande säkerhetspoängen. Säkerhetspoängen är en beräkning som bygger på förhållandet mellan dina felfria resurser och ditt sammanlagda antal resurser per rekommendation, inte summan av säkerhetspoäng för dina prenumerationer. 
   >
4. Klicka på **visa rekommendationer** att se rekommendationer för prenumerationen som du kan reparera för att förbättra dina säker poäng.
4. I listan över rekommendationer kan du se att det för varje rekommendation finns en kolumn som representerar den **skyddar score påverkan**. Det här talet anger hur mycket den totala säkra dina poäng förbättras om du följer rekommendationerna. Till exempel på skärmen nedan om du **åtgärda sårbarheter i behållaren säkerhetskonfigurationer**, säker resultatet kommer att ökas med 35 punkter.

   ![säker poäng](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Enskilda säker poäng

Dessutom om du vill visa enskilda säker poäng hittar du dessa alternativ inom bladet enskilda rekommendation.  

**Säkerhetspoäng för rekommendation** är en beräkning som bygger på förhållandet mellan dina felfria resurser och ditt sammanlagda antal resurser. Om antalet felfria resurser är lika med det totala antalet resurser, får du 50 poäng, vilket är högsta möjliga säkerhetspoäng för rekommendationer. Åtgärda resurserna med fel genom att följa rekommendationerna så kommer du närmare högsta möjliga säkerhetspoäng.

**Rekommendationernas inverkan** visar med hur mycket dina säkerhetspoäng ökar om du utför de rekommenderade stegen. Exempel: om din säkerhetspoäng är 42 och **rekommendationens inverkan** är + 3 förbättras dina poäng till 45 om du utför stegen i rekommendationen.

Rekommendationen visar vilka hot arbetsbelastningen utsätts för om åtgärdssteg inte utförs.

![enskilda rekommendation säker poäng](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du kan förbättra din säkerhet hållning med hjälp av **säker poäng** i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.


