---
title: Azure Analysis Services-utskalning | Microsoft Docs
description: Replikera Azure Analysis Services-servrar med skala ut
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5524645153db0468076cc9b567965bff79d915cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192334"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services-utskalning

Med skalbar, klientfrågor kan fördelas mellan flera *frågerepliker* i en *fråga pool*, vilket minskar svarstiderna under stora frågearbetsbelastningar arbetsbelastningar. Du kan också avgränsa bearbetning från frågepoolen, säkerställer att klientfrågor inte påverkas negativt av bearbetningsåtgärder. Skala ut kan konfigureras i Azure portal eller med hjälp av Analysis Services REST API.

Skala ut är tillgänglig för servrar i Standardprisnivån. Varje fråga replik debiteras till samma pris som din server. Alla frågerepliker skapas i samma region som din server. Antal frågerepliker som du kan konfigurera begränsas av den region som din server finns i. Mer information finns i [tillgänglighet efter region](analysis-services-overview.md#availability-by-region). Skala ut ökar inte mängden tillgängligt minne för servern. Om du vill öka minne, måste du uppgradera din plan. 

## <a name="why-scale-out"></a>Varför skala ut?

En server fungerar som både bearbetningsservern och fråge-i en typisk serverdistribution. Om antalet klientfrågor mot modeller på servern överskrider den enheter QPU (Query Processing) för din server plan, eller modellbearbetning inträffar samtidigt som hög frågearbetsbelastningar, kan försämra prestanda. 

Med skalbar, du kan skapa en frågepool med upp till sju ytterligare repliken resurser (åtta totalt, inklusive din *primära* server). Du kan skala antalet repliker i poolen fråga för att uppfylla QPU krav vid kritiska tidpunkter och du kan avgränsa en bearbetningsservern från frågepoolen när som helst. 

Oavsett hur många frågerepliker som du har i en frågepool fördelas bearbetningsbelastningar inte mellan frågerepliker. Den primära servern fungerar som bearbetningsservern. Frågerepliker fungerar bara frågor mot databasmodeller synkroniseras mellan den primära servern och varje replik i frågepoolen. 

Vid utskalning, kan det ta upp till fem minuter innan den nya frågerepliker som stegvis ska läggas till frågepoolen. När alla nya frågerepliker är igång och körs, att nya klientanslutningar finns belastningsutjämnas mellan resurser i frågepoolen. Befintliga klientanslutningar ändras inte från de för närvarande är anslutna till resursen. När skalning i avslutas de befintliga klientanslutningar i en poolresurs för frågan som tas bort från frågepoolen. Klienter kan återansluta till en återstående poolresurser för frågan.

## <a name="how-it-works"></a>Hur det fungerar

När du konfigurerar skalbar första gången, modell databaser på den primära servern är *automatiskt* synkroniseras med nya repliker i en ny frågepool. Automatisk synkronisering sker bara en gång. Under automatisk synkronisering kopieras den primära servern datafiler (krypterat i vila i blob storage) till en annan plats, också krypterade i vila i blob storage. Repliker i frågepoolen är sedan *hydrerat* med data från den andra uppsättningen av filer. 

När en automatisk synkronisering utförs bara när du skala ut en server för första gången, kan du också utföra en manuell synkronisering. Synkronisera säkerställer att data på repliker i frågepoolen matcha den primära servern. Vid bearbetning av modeller (uppdatera) på den primära servern, en synkronisering måste utföras *när* bearbetningen är avslutad. Den här synkroniseringen kopierar uppdaterade data från den primära servern filer i blob storage till den andra uppsättningen av filer. Repliker i frågepoolen är sedan ur med uppdaterade data från den andra uppsättningen av filer i blob storage. 

När du utför en efterföljande skalbara, till exempel är öka antalet repliker i frågepool från två till fem nya repliker hydrerat med data från den andra uppsättningen av filer i blob storage. Det finns ingen synkronisering. Om du utför hydrerat två gånger – en synkronisering efter skala ut, de nya replikerna i frågepoolen blir en redundant hydrering. När du utför en efterföljande skalbara, är det viktigt att tänka på:

* Synkronisera *före åtgärden skalbar* att undvika redundant hydrering har lagts till repliker. Samtidig synkronisering och skalbar åtgärder som körs på samma gång är inte tillåtna.

* Vid automatisering av båda bearbetning *och* skalbar åtgärder, är det viktigt att först bearbeta data på den primära servern och sedan utför en synkronisering och sedan utföra åtgärden för skala ut. Den här sekvensen säkerställer minimal inverkan på QPU-och minnesresurser.

* Synkronisering tillåts även om det finns inga repliker i frågepoolen. Om du skala ut från noll till en eller flera repliker med nya data från en bearbetning åtgärd på den primära servern, synkronisera först med inga repliker i frågepoolen och sedan skala ut. Synkronisera innan du skalar ut undviker redundanta hydrering nytillagda repliker.

* När du tar bort en modelldatabas från den primära servern, den inte automatiskt tas bort från repliker i frågepoolen. Du måste utföra en synkroniseringsåtgärd med hjälp av den [synkronisering AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell-kommando som tar bort filen/s för den här databasen från repliken delade blob-lagringsplats och tar sedan bort modellen databasen på repliker i frågepoolen. För att avgöra om en modelldatabas finns på repliker i frågepoolen men inte på den primära servern, se till att den **separera bearbetningsservern från att skicka frågor pool** ställs in på **Ja**. Använder SSMS för att ansluta till den primära servern med hjälp av den `:rw` kvalificerare om databasen finns. Anslut till repliker i frågepoolen genom att ansluta utan den `:rw` kvalificerare om samma databas finns också. Om databasen finns på repliker i frågepoolen men inte på den primära servern, kör du en synkroniseringsåtgärden.   

* När du ändrar en databas på den primära servern, finns det ytterligare ett steg krävs för att se till att databasen är korrekt synkroniserad till alla repliker. När du byter namn på, utför du en synkronisering med hjälp av den [synkronisering AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) kommando för att ange den `-Database` parametern med det gamla databasnamnet. Synkroniseringen tar bort databasen och filer med det gamla namnet från alla repliker. Utför sedan en annan synkronisering ange den `-Database` parametern med det nya databasnamnet. Den andra synkroniseringen kopierar den nya databasen till den andra uppsättningen av filer och hydrates alla repliker. Dessa synkroniseringar kan inte utföras med hjälp av kommandot Synkronisera modellen i portalen.

### <a name="separate-processing-from-query-pool"></a>Separata bearbetning från frågepool

Du kan välja att dela din bearbetningsservern från frågepoolen för maximal prestanda för såväl bearbetning frågeåtgärder. När separerade kan tilldelas nya klientanslutningar till frågerepliker i poolen fråga. Om bearbetningsåtgärder bara tar upp en kort tidsperiod, kan du avgränsa din bearbetningsservern från frågepoolen endast för den tid det tar att utföra åtgärder för bearbetning och synkronisering och Lägg sedan tillbaka till frågepoolen. När att separera bearbetningsservern från frågepoolen eller lägga till den tillbaka till frågepoolen kan ta upp till fem minuter innan åtgärden har slutförts.

## <a name="monitor-qpu-usage"></a>Övervaka QPU-användning

Övervaka din server i Azure portal för att avgöra om skalbarhet för din server är nödvändiga, med hjälp av mått. Om din QPU maximerar regelbundet, innebär det att antalet frågor mot dina modeller överskriden QPU-gränsen för din plan. Fråga pool jobbet kö längd mått ökar även när antalet frågor i frågekön tråd pool överskrider tillgängliga QPU. 

En annan bra mått för att titta på är genomsnittliga QPU av ServerResourceType. Det här måttet jämför genomsnittlig QPU för den primära servern med frågepoolen. 

![Fråga skala ut mått](media/analysis-services-scale-out/aas-scale-out-monitor.png)

### <a name="to-configure-qpu-by-serverresourcetype"></a>Konfigurera QPU av ServerResourceType
1. I ett linjediagram för mått, klickar du på **Lägg till mått**. 
2. I **RESOURCE**, markera din server, sedan i **mått namnområde**väljer **Analysis Services standardmått**, i **mått**, Välj **QPU**, och sedan i **aggregering**väljer **genomsnittlig**. 
3. Klicka på **gäller dela**. 
4. I **värden**väljer **ServerResourceType**.  

Läs [Övervaka servermått](analysis-services-monitor.md) för mer information.

## <a name="configure-scale-out"></a>Konfigurera utskalning

### <a name="in-azure-portal"></a>I Azure-portalen

1. I portalen klickar du på **skalbar**. Använd skjutreglaget för att välja antalet fråga replikservern. Antal repliker som du väljer är ett tillägg till din befintliga server.

2. I **separera bearbetningsservern från frågepoolen**, Välj Ja om du vill undanta dina bearbetningsservern från frågeservrar. Klienten [anslutningar](#connections) med standardanslutningssträngen (utan `:rw`) omdirigeras till repliker i frågepoolen. 

   ![Skala ut skjutreglaget](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicka på **spara** att etablera din nya replikservern för frågan. 

När du konfigurerar skalbar för en server för första gången, synkroniseras automatiskt modeller på den primära servern med repliker i frågepoolen. Automatisk synkronisering inträffar bara när när du först konfigurerar skalning till en eller flera repliker. Efterföljande ändringar i antalet repliker på samma server *utlöser inte en annan automatisk synkronisering*. Automatisk synkronisering utförs inte igen, även om du anger att servern noll repliker och sedan skala ut till valfritt antal repliker. 

## <a name="synchronize"></a>Synkronisera 

Synkroniseringsåtgärder måste utföras manuellt eller med hjälp av REST-API.

### <a name="in-azure-portal"></a>I Azure-portalen

I **översikt** > modellen > **synkronisera modellen**.

![Skala ut skjutreglaget](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API

Använd den **synkronisering** igen.

#### <a name="synchronize-a-model"></a>Synkronisera en modell   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Hämta synkroniseringsstatus för  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Status för returkoder:


|Kod  |Beskrivning  |
|---------|---------|
|-1     |  Ogiltig       |
|0     | Replikera        |
|1     |  Återställning       |
|2     |   Slutfört       |
|3     |   Misslyckad      |
|4     |    Slutför     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du använder PowerShell [installera eller uppdatera den senaste Azure PowerShell-modulen](/powershell/azure/install-az-ps). 

Kör synkronisering med [synkronisering AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Ange antal frågerepliker och [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den valfria `-ReadonlyReplicaCount` parametern.

Om du vill separera bearbetningsservern från frågepoolen använder [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den valfria `-DefaultConnectionMode` används till `Readonly`.

Mer information finns i [med ett huvudnamn för tjänsten med modulen Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Anslutningar

På översiktssidan för din server finns det två servernamn. Om du inte har konfigurerat skalbar för en server, fungerar på samma både servernamn. När du konfigurerar skalbar för en server, måste du ange namnet på lämplig servern beroende på vilken typ av anslutning. 

För slutanvändaren klientanslutningar som Power BI Desktop, Excel och anpassade appar, Använd **servernamn**. 

SSMS SSDT och anslutningssträngar i PowerShell, Azure-funktionsappar och AMO, använder du **hanteringsservernamnet**. Hanteringsserverns namn innehåller en särskild `:rw` kvalificerare (skrivskyddad). Bearbetningsåtgärder för alla inträffar på (primär) hanteringsserver.

![Servernamn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Felsöka

**Problem:** Användare får fel **servern hittades inte ”\<namnet på servern >' instansen i anslutningsläge” skrivskyddad ”.**

**Lösning:** När du väljer den **separera bearbetningsservern från frågepoolen** alternativet klientanslutningar med hjälp av standard-anslutningssträngen (utan `:rw`) omdirigeras till frågerepliker för poolen. Om repliker i frågepoolen inte är ännu online eftersom synkronisering inte har ännu har slutförts, kan omdirigerad klientanslutningar misslyckas. Om du vill förhindra att misslyckade anslutningar, måste det finnas minst två servrar i frågepoolen när du utför en synkronisering. Varje server synkroniseras individuellt medan andra är online. Om du väljer att inte installera bearbetningsservern i frågepoolen under bearbetning, kan du välja att ta bort den från poolen för bearbetning och sedan lägga tillbaka det i poolen när bearbetningen är klar, men innan du synkroniserar. Använda minne och QPU mått för att övervaka synkroniseringsstatus för.



## <a name="related-information"></a>Relaterad information

[Övervaka servermått](analysis-services-monitor.md)   
[Manage Azure Analysis Services](analysis-services-manage.md) 
