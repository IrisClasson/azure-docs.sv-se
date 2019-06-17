---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f48ad6ca74e6ce10148d66549fea16bc74015b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171223"
---
I följande tabell visas kvotinformation som är specifika för Azure Service Bus-meddelanden. Information om priser och andra kvoter för Service Bus finns i [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Kvotnamn | Scope | Anteckningar | Värde |
| --- | --- | --- | --- |
| Maximalt antal Basic eller Standard-namnområden per Azure-prenumeration |Namnrymd |Efterföljande begäranden om ytterligare namnområden på Basic eller Standard avvisas av Azure-portalen. |100|
| Maximalt antal Premium-namnområden per Azure-prenumeration |Namnrymd |Efterföljande begäranden om ytterligare Premium-namnområden avvisas av portalen. |25 |
| Storlek på kö eller ett ämne |Entitet |Definierats vid skapande av kön eller ämnet. <br/><br/> Efterföljande inkommande meddelanden avvisas och ett undantag tas emot av den anropande koden. |1, 2, 3, 4 eller 5 GB.<br /><br />I Premium-SKU och Standard-SKU med [partitionering](/azure/service-bus-messaging/service-bus-partitioning) aktiverad maxstorleken för kön eller ämnet är 80 GB. |
| Antalet samtidiga anslutningar för ett namnområde |Namnrymd |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. REST-åtgärder räknas inte mot samtidiga TCP-anslutningar. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Antalet parallella ta emot förfrågningar för en entitet i kön, ämnet eller prenumeration |Entitet |Efterföljande ta emot begäranden avvisas och ett undantag tas emot av den anropande koden. Den här kvoten gäller för det kombinerade antalet parallella får åtgärder för alla prenumerationer på ett ämne. |5,000 |
| Antal ämnen eller köer per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa ett nytt ämne eller en kö i namnområdet avvisas. Resultatet blir att, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från ett hanteringsgränssnitt emot ett undantag av den anropande koden. |1 000 Basic eller Standard-nivån. Det totala antalet ämnen och -köer i ett namnområde måste vara mindre än eller lika med 1 000. <br/><br/>För Premium-nivån är 1 000 per meddelandeenhet (MU). Högsta gränsen är 4 000. |
| Antal [partitionerad ämnen eller köer](/azure/service-bus-messaging/service-bus-partitioning) per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa en ny partitionerad ämne eller en kö i namnområdet avvisas. Resultatet blir att, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från hanterings-API, undantaget **QuotaExceededException** tas emot av den anropande koden. |Grundläggande och Standard-nivån: 100.<br/><br/>Partitionerade enheter stöds inte i den [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) nivå.<br/><br />Varje partitionerad kö eller ämne räknas mot kvoten på 1 000 enheter per namnområde. |
| Maximal storlek för alla meddelanden entitetssökväg: kö eller ett ämne |Entitet |- |260 tecken. |
| Maximal storlek på alla meddelanden entitetsnamn: namnrymd, prenumeration eller prenumeration regel |Entitet |- |50 tecken. |
| Maximal storlek på en [meddelande-ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entitet |- | 128 |
| Maximal storlek på ett meddelande [sessions-ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitet |- | 128 |
| Storlek för en entitet i kön, ämnet eller prenumeration |Entitet |Inkommande meddelanden som överskrider kvoterna avvisas och ett undantag tas emot av den anropande koden. |Maximal storlek: 256 KB för [standardnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB för [premiumnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />På grund av system som är mindre än värdena i den här gränsen.<br /><br />Maximal huvudstorlek: 64 KB.<br /><br />Maximalt antal egenskaper för sidhuvud i egenskapsuppsättningen: **byte/int. MaxValue**.<br /><br />Maximal storlek för egenskap i egenskapsuppsättningen: Ingen explicit gräns. Begränsas av högsta huvudstorlek. |
| Egenskapsstorlek för en entitet i kön, ämnet eller prenumeration |Entitet | Undantaget **SerializationException** genereras. |Maximal egenskapsstorlek för varje egenskap är 32 000. Sammanlagda storleken för alla egenskaper får inte överskrida 64,000. Den här begränsningen gäller för hela rubriken för den [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), som har både användaregenskaper och Systemegenskaper, till exempel [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), och [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Antalet prenumerationer per ämne |Entitet |Efterföljande förfrågningar för att skapa ytterligare prenumerationer för ämnet avvisas. Om konfigureras via portalen, därför kan visas ett felmeddelande. Om den anropas från ett hanteringsgränssnitt emot ett undantag av den anropande koden. |Standard och Premium-nivån: Varje prenumeration räknas mot kvoten av 1 000 enheter, det vill säga köer, ämnen och prenumerationer per namnområde. |
| Antal SQL-filter per ämne |Entitet |Efterföljande förfrågningar för att skapa ytterligare filter på ämnet avvisas och ett undantag tas emot av den anropande koden. |2,000 |
| Antal korrelationsfilter per ämne |Entitet |Efterföljande förfrågningar för att skapa ytterligare filter på ämnet avvisas och ett undantag tas emot av den anropande koden. |100,000 |
| Storleken på SQL-filter eller åtgärder |Namnrymd |Efterföljande förfrågningar för att skapa ytterligare filter avvisas och ett undantag tas emot av den anropande koden. |Maximal längd för Filtersträngen villkor: 1,024 (1 K).<br /><br />Maximal längd på regeln åtgärd sträng: 1,024 (1 K).<br /><br />Maximalt antal uttryck per Regelåtgärd: 32. |
| Antal [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regler per namnområde, kö eller ett ämne |Entiteten, namnområde |Efterföljande begäranden om skapande av ytterligare regler avvisas och ett undantag tas emot av den anropande koden. |Maximalt antal regler: 12. <br /><br /> Regler som är konfigurerade på en Service Bus-namnrymd gäller för alla köer och ämnen i det namnområdet. |
| Antal meddelanden per transaktion | Transaktionen | Ytterligare inkommande meddelanden avvisas och ett undantag om ”det går inte att skicka fler än 100 meddelanden i en enda transaktion” tas emot av den anropande koden. | 100 <br /><br /> För både **Send()** och **SendAsync()** åtgärder. |

[Azure portal]: https://portal.azure.com
