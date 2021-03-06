---
title: Protokoll stöd för HTTP-huvuden i Azures front dörr | Microsoft Docs
description: I den här artikeln beskrivs HTTP-huvudprotokoll som stöds av front dörren.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: a1060cbf1b2204c3feba413b8c8ce0cba58941c6
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799131"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protokoll stöd för HTTP-huvuden i Azures front dörr
Den här artikeln beskriver det protokoll som stöds av front dörren med delar av anrops vägen (se bilden). I följande avsnitt finns mer information om HTTP-huvuden som stöds av front dörren.

![Protokoll för HTTP-meddelandehuvuden i Azure front dörr][1]

>[!IMPORTANT]
>Front dörren certifierar inte några HTTP-huvuden som inte dokumenteras här.

## <a name="client-to-front-door"></a>Klient till front dörr
Front dörren godkänner de flesta huvuden från den inkommande begäran utan att ändra dem. Vissa reserverade huvuden tas bort från den inkommande begäran om de skickas, inklusive huvuden med prefixet X-FD-*.

## <a name="front-door-to-backend"></a>Front dörr till Server del

Front dörren innehåller rubriker från en inkommande begäran om den inte tas bort på grund av begränsningar. Front dörren lägger också till följande rubriker:

| Sidhuvud  | Exempel och beskrivning |
| ------------- | ------------- |
| Rapportör |  Via: 1,1 Azure </br> Front dörren lägger till klientens HTTP-version följt av *Azure* som värde för via-huvudet. Den här rubriken anger klientens HTTP-version och den främre dörren var en mellanliggande mottagare för begäran mellan klienten och Server delen.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representerar den klient-IP-adress som är kopplad till den begäran som bearbetas. En begäran som kommer från en proxyserver kan till exempel lägga till den X-vidarebefordrade-för-rubriken för att ange IP-adressen för den ursprungliga anroparen. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representerar den IP-adress för socket som är kopplad till den TCP-anslutning som den aktuella begäran kommer från. En begär ande klients IP-adress kanske inte är lika med dess IP-adress för socket eftersom den kan skrivas över av en användare.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> En unik referens sträng som identifierar en begäran som betjänas av front dörren. Den används för att söka efter åtkomst loggar och viktiga för fel sökning.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hopp = 1 </br> En rubrik som används av front dörren för att identifiera begär ande slingor och användare bör inte ta ett beroende på den. |
| X-vidarebefordrad – för | X-vidarebefordrad – för: 127.0.0.1 </br> HTTP-huvudfältet X-forwarded – for (XFF)-HTTP-huvud identifierar ofta den ursprungliga IP-adressen för en klient som ansluter till en webb server via en HTTP-proxy eller belastningsutjämnare. Om det finns ett befintligt XFF-huvud lägger front dörren till klient-socket-IP: en eller lägger till XFF-huvudet med klientens IP-adress för socket. |
| X-vidarebefordrad-värd | X-vidarebefordrad-värd: contoso.azurefd.net </br> Fältet X-forwarded-Host-HTTP-huvud är en gemensam metod som används för att identifiera den ursprungliga värddatorn som begärs av klienten i rubriken HTTP-begäran för värd. Detta beror på att värd namnet från Front dörren kan skilja sig för Server dels servern som hanterar begäran. |
| X-vidarebefordrad – proto | X-vidarebefordrad-proto: http </br> Fältet X-forwarded-proto HTTP-huvud används ofta för att identifiera ursprungs protokollet för en HTTP-begäran, eftersom front dörren, baserat på konfiguration, kan kommunicera med Server delen med hjälp av HTTPS. Detta gäller även om begäran till den omvända proxyn är HTTP. |
| X-FD-HealthProbe | Fältet X-FD-HealthProbe HTTP-huvud används för att identifiera hälso avsökningen från Front dörren. Om den här rubriken har angetts till 1 är begäran hälso avsökning. Du kan använda när du vill begränsa åtkomsten från en viss front dörr med X-vidarebefordrade-värd huvud fält. |
|X-Azure-FDID | X-Azure-FDID-huvud: 437c82cd-360A-4a54-94c3-5ff707647783 </br> Det här fältet innehåller frontdoorID som kan användas för att identifiera vilken front-dörr inkommande begäran kommer från. Det här fältet fylls i av front dörrs tjänsten. | 

## <a name="front-door-to-client"></a>Klientens front dörr

Alla rubriker som skickas till frontend-dörren från Server delen skickas även till klienten. Följande är huvuden som skickas från klientens dörr till klienter.

| Sidhuvud  | Exempel |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Detta är en unik referens sträng som identifierar en begäran som hanteras av en front dörr. Detta är avgörande för fel sökning eftersom det används för att söka i åtkomst loggar.|

## <a name="next-steps"></a>Nästa steg

- [Skapa en Front Door](quickstart-create-front-door.md)
- [Så här fungerar en front dörr](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
