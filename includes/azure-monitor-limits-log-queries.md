---
title: inkludera fil
description: inkludera fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82072645"
---
### <a name="general-query-limits"></a>Allmänna begränsningar för frågor

| Gräns | Description |
|:---|:---|
| Frågespråk | Azure Monitor använder samma [frågespråk för Kusto](/azure/kusto/query/) som Azure datautforskaren. Se [Azure Monitor logg frågor språk skillnader](../articles/azure-monitor/log-query/data-explorer-difference.md) för KQL språk element som inte stöds i Azure Monitor. |
| Azure-regioner | Logg frågor kan uppleva onödig belastning när data sträcker sig Log Analytics arbets ytor i flera Azure-regioner. Mer information finns i [fråga om begränsningar](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Kors resurs frågor | Maximalt antal Application Insights-resurser och Log Analytics arbets ytor i en enda fråga som är begränsade till 100.<br>Frågan över resurser stöds inte i View Designer.<br>Frågan över resurser i logg aviseringar stöds i det nya scheduledQueryRules-API: et.<br>Se [gränser för kors resurs frågor](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) för mer information. |

### <a name="user-query-throttling"></a>Begränsning av användar frågor
Azure Monitor har flera begränsnings gränser som skyddar mot användare som skickar ett stort antal frågor. Detta beteende kan eventuellt överbelasta systemets Server dels resurser och äventyra tjänstens svars tider. Följande gränser är utformade för att skydda kunder mot avbrott och säkerställa konsekvent service nivå. Användarens begränsning och begränsningar har utformats för att endast påverka extrema användnings scenarier och bör inte vara relevanta för typisk användning.


| Mått | Begränsa per användare | Description |
|:---|:---|:---|
| Samtidiga frågor | 5 | Om det redan finns 5 frågor som körs för användaren placeras alla nya frågor i en transaktionskö per användare. När en av de frågor som körs avslutas kommer nästa fråga att hämtas från kön och startas. Detta omfattar inte frågor från varnings regler.
| Tid i samtidighets kön | 2,5 minuter | Om en fråga finns i kön i mer än 2,5 minuter utan att startas, avbryts den med ett HTTP-felsvar med koden 429. |
| Totalt antal frågor i samtidighets kön | 40 | När antalet frågor i kön når 40 kommer eventuella ytterligare frågor att avvisas med en HTTP-felkod 429. Det här talet är förutom de 5 frågor som kan köras samtidigt. |
| Frågefrekvens | 200 frågor per 30 sekunder | Detta är den övergripande hastigheten som frågor kan skickas av en enskild användare till alla arbets ytor.  Den här gränsen gäller för programmatiska frågor eller frågor som initieras av visualiserings delar som Azure-instrumentpaneler och sammanfattnings sidan Log Analytics-arbetsyta. |

- Optimera dina frågor enligt beskrivningen i [optimera logg frågor i Azure Monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Instrument paneler och arbets böcker kan innehålla flera frågor i en enda vy som genererar en burst med frågor varje gång de läser in eller uppdaterar. Överväg att dela upp dem i flera vyer som läses in på begäran. 
- I Power BI kan du bara extrahera sammansatta resultat i stället för obehandlade loggar.