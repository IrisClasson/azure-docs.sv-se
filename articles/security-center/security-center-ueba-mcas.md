---
title: Hotidentifiering i Azure Security Center | Microsoft Docs
description: " Identifiera hot och skadlig genom att integrera Microsoft Cloud App Security med Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: af7896ec4afaeefda7261542bf593a89a7bb9ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551791"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA för Azure-resurser och användare 

Azure Security Center partner med Microsoft Cloud App Security för att få aviseringar baserat på användar- och beteendeanalyser entitet (UEBA) för dina Azure-resurser och användare (Azure-aktivitet). Dessa aviseringar identifierar avvikelser i användarnas beteende med hjälp av analys av användar- och entitetsbeteende samt maskininlärning (ML) så att du kan köra avancerad hotidentifiering omedelbart på aktiviteter i dina prenumerationer. Eftersom de aktiveras automatiskt, ger de nya avvikelseidentifieringarna omedelbart resultat genom att tillhandahålla omedelbara identifieringar av flera avvikande användaraktiviteter över alla användare och resurser som är associerade med din prenumeration. Dessutom utnyttjar aviseringarna ytterligare data som redan finns i Microsoft Cloud App Security-identifieringsmotorn för att stöda dig i undersökningsprocessen och hindra pågående hot. 

> [!NOTE]
> Azure Security Center, som kan lagra en kopia av säkerhetsrelaterade kunddata, som samlas in från eller som är associerade med en kund-resurs (t.ex. virtuella datorer eller Azure Active Directory-klient): (a) i samma geografiska område som resursen, utom i de geografiska områdena där Microsoft har än att distribuera Azure Security Center, i vilket fall en kopia av sådana data lagras i USA. och (b) om Azure Security Center använder en annan Microsoft-onlinetjänst för att bearbeta dessa data, det kan lagra dessa data i enlighet med geoplats reglerna för den andra onlinetjänst.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Förutsättningar

- En giltig aktiverat [Microsoft Cloud App Security-licens](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Security Center Standard-nivån](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Hotidentifieringsaviseringar

Security Center har stöd för Cloud App Security aviseringarna för avvikelseidentifiering, till exempel:

**Omöjlig resa**
-  Den här identifieringen identifierar två användaraktiviteter (är en eller flera sessioner) kommer från geografiskt avlägsna platser inom en tidsperiod som är kortare än tid det skulle ha tagit reser från den första platsen till den andra, som anger att användaren att en annan användare använder samma autentiseringsuppgifter. Den här identifieringen använder machine learning-algoritm som ignorerar uppenbara ”FALSKT positiva resultat” bidrar till villkoret omöjlig resa, till exempel virtuella privata nätverk och platser som ofta används av andra användare i organisationen. Identifieringen har en inledande inlärningsperiod på sju dagar under vilken den lär sig den nya användarens aktivitetsmönster.

**Aktivitet från ovanligt land**
- Den här identifieringen tar hänsyn till de senaste aktivitet platser att fastställa nya och ovanliga platser. Avvikelseidentifieringsmotorn lagrar information om tidigare platser som används av användarna i organisationen. En avisering utlöses när en aktivitet som utförs från en plats som nyligen eller aldrig inte besöktes av någon användare i organisationen. 

**Aktivitet från anonyma IP-adresser**
- Den här identifieringen identifierar att användarna var aktiva från en IP-adress som har identifierats som en anonym proxy IP-adress. Dessa proxyservrar används av personer som vill dölja sina enheters IP-adress och kan användas i skadligt syfte. Den här identifieringen använder machine learning-algoritm som minskar ”FALSKT positiva resultat”, till exempel argumentantal taggade IP-adresser som ofta används av användare i organisationen.
 
  ![Varning för hot](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Inaktivera hotidentifieringsaviseringar

Dessa aviseringar är aktiverade som standard, men du kan inaktivera dem:

1. I Security Center-bladet väljer **priser och inställningar** och välj prenumerationen som är tillämpligt.
2. Klicka på **Hotidentifiering**.
3. Under **för att göra integreringar**, avmarkera **Tillåt Microsoft Cloud App Security åtkomst till Mina data**, och klicka på **spara**.

   ![Varning för hot](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Det finns en inledande inlärningsperiod på sju dagar under vilka inte alla avvikelser identifiering av aviseringar har aktiverats. Efter denna period jämförs varje session med aktiviteten, när användarna var aktiva, IP-adresser, enheter, etc. som identifierats under den senaste månaden och riskpoängen för dessa aktiviteter. Dessa identifieringar är en del av de machine learning avvikelseidentifieringsmotorn de profiler för din miljö och utlöser aviseringar med avseende på en baslinje har lärt dig på din organisations aktivitet. Dessa identifieringar kan du även använda machine learning-algoritmer som utformats för att profilera de användare och logga in mönster för att minska antalet falska positiva identifieringar.
>
  
## <a name="next-steps"></a>Nästa steg
Den här artikeln visar att du kan arbeta med hotidentifiering i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
