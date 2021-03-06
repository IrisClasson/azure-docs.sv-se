---
title: Diagnostisera och felsöka problem med Azure Cosmos DB .NET SDK
description: Använd funktioner som loggning på klient sidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Cosmos DB problem när du använder .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/16/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1dd6bdc66146eb7dfe155e7d1091eee5cca450a0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290916"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostisera och felsöka problem med Azure Cosmos DB .NET SDK

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Asynkron Java-SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Den här artikeln beskriver vanliga problem, lösningar, diagnostiska steg och verktyg när du använder [.NET SDK](sql-api-sdk-dotnet.md) med Azure Cosmos DB SQL API-konton.
.NET SDK tillhandahåller logisk representation på klient sidan för att få åtkomst till Azure Cosmos DB SQL API. I den här artikeln beskrivs verktyg och metoder för att hjälpa dig om du stöter på problem.

## <a name="checklist-for-troubleshooting-issues"></a>Check lista för fel söknings problem
Överväg följande check lista innan du flyttar ditt program till produktion. Genom att använda check listan förhindrar du flera vanliga problem som du kan se. Du kan också snabbt diagnostisera när ett problem uppstår:

*    Använd den senaste [SDK: n](sql-api-sdk-dotnet-standard.md). Preview SDK: er bör inte användas för produktion. Detta förhindrar att kända problem som redan har åtgärd ATS visas.
*    Granska [prestanda tipsen](performance-tips.md)och följ rekommendationerna. Detta bidrar till att förhindra skalning, svars tid och andra prestanda problem.
*    Aktivera SDK-loggning för att hjälpa dig att felsöka ett problem. Att aktivera loggning kan påverka prestanda, så det är bäst att aktivera det bara när du felsöker problem. Du kan aktivera följande loggar:
*    [Logga mått](monitor-accounts.md) med hjälp av Azure Portal. Portal mått visar Azure Cosmos DB telemetri, vilket är användbart för att avgöra om problemet motsvarar Azure Cosmos DB eller om det kommer från klient sidan.
*    Logga den [diagnostiska strängen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) i v2 SDK eller [diagnostik](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) i v3 SDK från åtgärds svar för punkt.
*    Logga [SQL-frågans mått](sql-api-query-metrics.md) från alla fråge svar 
*    Följ konfigurationen för [SDK-loggning]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Ta en titt på avsnittet [vanliga problem och lösningar](#common-issues-workarounds) i den här artikeln.

Se [avsnittet GitHub-problem](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) som är aktivt övervakat. Kontrol lera om det finns liknande problem med en lösning som redan har arkiverats. Om du inte hittar någon lösning kan du ange ett GitHub-problem. Du kan öppna ett support Tick för brådskande problem.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Vanliga fel och lösningar

### <a name="general-suggestions"></a>Allmänna förslag
* Kör din app i samma Azure-region som ditt Azure Cosmos DB-konto, närhelst det är möjligt. 
* Du kan stöta på problem med anslutning/tillgänglighet på grund av bristande resurser på klient datorn. Vi rekommenderar att du övervakar CPU-belastningen på noder som kör Azure Cosmos DB-klienten och skalar upp/ut om de körs vid hög belastning.

### <a name="check-the-portal-metrics"></a>Kontrol lera Portal måtten
Genom att kontrol lera [Portal måtten](monitor-accounts.md) kan du avgöra om det är problem med klient sidan eller om det är problem med tjänsten. Om måtten t. ex. innehåller en hög frekvens av avgiftsbelagda begär Anden (HTTP-statuskod 429), vilket innebär att begäran får en begränsning, kontrollerar du i avsnittet om [antalet begär Anden är för stort](troubleshoot-request-rate-too-large.md) . 

## <a name="common-error-status-codes"></a>Vanliga fel status koder<a id="error-codes"></a>

| Statuskod | Beskrivning | 
|----------|-------------|
| 400 | Felaktig begäran (beror på fel meddelandet)| 
| 401 | [Inte auktoriserad](troubleshoot-unauthorized.md) | 
| 404 | [Resursen hittades inte](troubleshoot-not-found.md) |
| 408 | [Tids gränsen nåddes för begäran](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Ett konflikt haveri är när det ID som angetts för en resurs på en Skriv åtgärd har tagits av en befintlig resurs. Använd ett annat ID för resursen för att lösa problemet eftersom ID måste vara unikt i alla dokument med samma partitionsnyckel. |
| 410 | Rest-undantag (tillfälligt fel som inte strider mot SLA) |
| 412 | Ett annat villkor är att åtgärden angav en eTag som skiljer sig från den version som är tillgänglig på servern. Det är ett optimistiskt samtidighets fel. Försök att utföra åtgärden igen när du har läst in den senaste versionen av resursen och uppdaterat eTag i förfrågan.
| 413 | [Begär ande enheten är för stor](concepts-limits.md#per-item-limits) |
| 429 | [För många begär Anden](troubleshoot-request-rate-too-large.md) |
| 449 | Tillfälligt fel som bara uppstår vid Skriv åtgärder och som är säkert att försöka igen |
| 500 | Åtgärden kunde inte utföras på grund av ett oväntat tjänst fel. Kontakta supporten. Mer information finns i [Skicka ett support ärende till Azure](https://aka.ms/azure-support). |
| 503 | [Tjänsten är inte tillgänglig](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Port överbelastning för Azure SNAT (PAT)

Om din app distribueras på [azure Virtual Machines utan en offentlig IP-adress](../load-balancer/load-balancer-outbound-connections.md), upprättar [Azure SNAT-portar](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) som standard anslutningar till en slut punkt utanför den virtuella datorn. Antalet anslutningar som tillåts från den virtuella datorn till Azure Cosmos DB slut punkten begränsas av [Azure SNAT-konfigurationen](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Den här situationen kan leda till anslutningens begränsning, anslutningens slut för ande, eller de [tids gränser](troubleshoot-dot-net-sdk-request-timeout.md)som anges ovan.

 Azure SNAT-portar används endast när den virtuella datorn har en privat IP-adress som ansluter till en offentlig IP-adress. Det finns två lösningar för att undvika begränsning av Azure SNAT (förutsatt att du redan använder en enda klient instans i hela programmet):

* Lägg till din Azure Cosmos DB tjänst slut punkt i under nätet för ditt Azure Virtual Machines virtuella nätverk. Mer information finns i [Azure Virtual Network Service-slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md). 

    När tjänstens slut punkt Aktiver ATS skickas inte längre begär Anden från en offentlig IP-adress till Azure Cosmos DB. I stället skickas det virtuella nätverket och under nätets identitet. Den här ändringen kan leda till att brand väggen släpper om bara offentliga IP-adresser är tillåtna. Om du använder en brand vägg, när du aktiverar tjänstens slut punkt, lägger du till ett undernät i brand väggen med hjälp av [Virtual Network ACL: er](../virtual-network/virtual-networks-acl.md).
* Tilldela en [offentlig IP-adress till din virtuella Azure-dator](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Hög nätverks fördröjning
Hög nätverks fördröjning kan identifieras med hjälp av den [diagnostiska strängen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) i v2 SDK eller [diagnostik](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) i v3 SDK.

Om det inte finns några [tids gränser](troubleshoot-dot-net-sdk-request-timeout.md) och diagnostiken visar enskilda förfrågningar där den höga svars tiden är tydlig på skillnaden mellan `ResponseTime` och `RequestStartTime` , t. ex. (>300 millisekunder i det här exemplet):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Den här fördröjningen kan ha flera orsaker:

* Ditt program körs inte i samma region som ditt Azure Cosmos DB-konto.
* Din [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) -eller [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) -konfiguration är felaktig och försöker ansluta till en annan region där programmet körs för tillfället.
* Det kan finnas en Flask hals i nätverks gränssnittet på grund av hög trafik. Om programmet körs på Azure Virtual Machines finns det möjliga lösningar:
    * Överväg att använda en [virtuell dator med accelererat nätverk aktiverat](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Aktivera [accelererat nätverk på en befintlig virtuell dator](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Överväg att använda en [högre slut virtuell dator](../virtual-machines/windows/sizes.md).

### <a name="slow-query-performance"></a>Långsam frågans prestanda
[Frågans mått](sql-api-query-metrics.md) hjälper till att avgöra var frågan kostar det mesta av tiden. Från frågans mått kan du se hur mycket av det som ägnas åt Server delen jämfört med klienten.
* Om backend-frågan returnerar snabbt och ägnar en lång tid på klienten kontrollerar du belastningen på datorn. Det beror förmodligen på att det inte finns tillräckligt med resurser och att SDK väntar på att resurser ska kunna hantera svaret.
* Om Server dels frågan är långsam försöker du [optimera frågan](optimize-cost-queries.md) och titta på den aktuella [indexerings principen](index-overview.md) 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)
* Lär dig mer om de [reaktorbaserade Java SDK: erna](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
