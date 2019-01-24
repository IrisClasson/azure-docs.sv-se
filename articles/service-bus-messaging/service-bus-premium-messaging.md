---
title: Översikt över prisnivåer för Azure Service Bus Premium- och Standard-meddelanden | Microsoft Docs
description: Service Bus Premium- och Standard-meddelandenivåer
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2018
ms.author: aschhab
ms.openlocfilehash: ae35f73e601cfa83fc960c5331f9956863677941
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855303"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium- och Standard-meddelandenivåer

Service Bus-meddelanden, inklusive entiteter som köer och ämnen, kombinerar meddelandefunktioner för företag med utförlig publicerings-/prenumerationssemantik i molnskala. Service Bus-meddelanden används som kommunikationsryggrad i flera avancerade molnlösningar.

*Premium*-nivån av Service Bus-meddelanden är ett svar på vanliga kundönskemål gällande skala, prestanda och tillgänglighet för verksamhetskritiska program. Premium-nivån rekommenderas för användning i produktionsmiljö. Även om funktionsuppsättningarna är snudd på identiska är dessa två nivåer av Service Bus-meddelanden utformade för att passa olika användningsfall.

En del övergripande skillnader visas i tabellen nedan.

| Premium | Standard |
| --- | --- |
| Högt genomflöde |Variabelt genomflöde |
| Förutsägbar prestanda |Variabel svarstid |
| Fast prissättning |Variabla priser – betala per användning |
| Möjlighet att skala arbetsbelastningen uppåt och nedåt |Saknas |
| Meddelandestorlek upp till 1 MB |Meddelandestorlek upp till 256 kB |

**Service Bus Premium-meddelanden** ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resurscontainern kallas för en *meddelandefunktionsenhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2 eller 4 meddelandefunktionsenheter för varje Service Bus Premium-namnområde. En enda arbetsbelastning eller enhet kan spänna över flera meddelandefunktionsenheter, och antalet meddelandefunktionsenheter kan ändras när du vill, även om faktureringen är per 24 timmar eller daglig taxa. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen.

Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också. Service Bus Premium-meddelanden bygger på lagringsmotorn som introducerades i [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Med Premium-meddelanden är topprestandan mycket snabbare än på standardnivån.

## <a name="premium-messaging-technical-differences"></a>Premium-meddelanden – tekniska skillnader

I följande avsnitt diskuteras några skillnader mellan Premium- och Standard-meddelandenivåerna.

### <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Det finns inget stöd för partitionerade köer och ämnen i Premium Messaging. Mer information om partitionering finns i [Partitionerade köer och ämnen](service-bus-partitioning.md).

### <a name="express-entities"></a>Expressenheter

Eftersom meddelandehanteringen på premiumnivå körs i en helt isolerad körningsmiljö, stöds inte längre expressenheter i premiumnamnområden. Mer information om expressfunktionen finns i egenskapen [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Om du har kod som körs med meddelandehantering på standardnivå och vill portera den till premiumnivån kontrollerar du att egenskapen [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) har värdet **false** (standardvärdet).

## <a name="get-started-with-premium-messaging"></a>Kom igång med Premium-meddelandetjänster

Det är enkelt att komma igång med Premium-meddelandetjänster och processen liknar den för Standard-meddelandetjänster. Börja med att [skapa ett namnområde](service-bus-create-namespace-portal.md) i [Azure Portal](https://portal.azure.com). Se till att du väljer **Premium** under **Prisnivå**. Klicka på **Visa fullständiga prisuppgifter** om du vill se mer information om varje nivå.

![create-premium-namespace][create-premium-namespace]

Du kan också skapa [Premium-namnområden med hjälp av Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Nästa steg

I följande länkar kan du lära dig mer om Service Bus-meddelanden:

* [Introduktion till Azure Service Bus Premium-meddelanden (blogginlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduktion till Azure Service Bus Premium-meddelanden (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över Service Bus-meddelanden](service-bus-messaging-overview.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
