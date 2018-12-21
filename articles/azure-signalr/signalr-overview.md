---
title: Vad är Azure SignalR
description: Översikt över Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: e66326c6c4d93a92c579255cb00b6614ecc03b8c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255184"
---
# <a name="what-is-azure-signalr-service"></a>Vad är Azure SignalR Service?

Azure SignalR Service förenklar arbetet med att lägga till webbfunktioner i realtid för program via HTTP. Denna realtidsfunktion gör att tjänsten kan skicka innehållsuppdateringar till anslutna klienter, till exempel en webbplats med en sida eller ett mobilt program. Därmed uppdateras klienterna utan att man behöver avsöka servern eller skicka nya HTTP-begäranden om uppdateringar.

Den här artikeln ger en översikt över Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Vad används Azure SignalR Service till?

Det finns många programtyper som kräver uppdatering av innehållet i realtid. Följande exempel är goda kandidater för användning av Azure SignalR Service:

* Appar som kräver högfrekvent uppdatering från servern. Exempel är spel, röstning, auktioner, kartor och GPS-appar.
* Instrumentpaneler och övervakningsappar. Exempel är företagsinstrumentpaneler och omedelbara försäljningsuppdateringar.
* Samarbetsappar. Whiteboard-appar och programvaror för teammöten är exempel på samarbetsappar.
* Appar som kräver aviseringar. Sociala nätverk, e-post, chattar, spel, reseaviseringar och många andra appar använder sig av aviseringar.

SignalR ger en abstrakt samling tekniker som används för att skapa webbappar av realtidstyp. [WebSockets](https://wikipedia.org/wiki/WebSocket) är den optimala transportmetoden, men andra metoder som [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) och lång avsökning används när andra alternativ inte är tillgängliga. SignalR identifierar och initierar automatiskt lämplig transportmetod baserat på de funktioner som stöds på servern och klienten.

SignalR tillhandahåller dessutom en programmeringsmodell för realtidsprogram som gör att servern kan skicka meddelanden till alla anslutningar eller till en delmängd av anslutningar som tillhör en viss användare eller som har placerats i en valfri grupp.

## <a name="how-to-use-azure-signalr-service"></a>Så använder du Azure SignalR Service

Det finns för närvarande tre sätt att använda Azure SignalR Service:

- **[Skala en ASP.NET Core SignalR-App](signalr-overview-scale-aspnet-core.md)** – Integrera Azure SignalR Service med ett ASP.NET Core SignalR-program för att skala ut till hundratusentals anslutningar.
- **[Skapa serverlösa realtidsappar](signalr-overview-azure-functions.md)** – Använd Azure Functions-integrering med Azure SignalR Service för att skapa serverlösa realtidsprogram med språk som JavaScript, C# och Java.
- **[Skicka meddelanden från servern till klienter via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Azure SignalR Service har REST API så att program kan skicka meddelanden till klienter som är anslutna med SignalR Service i alla REST-kompatibla programmeringsspråk.