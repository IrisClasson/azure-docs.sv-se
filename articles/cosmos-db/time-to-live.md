---
title: Ta bort data i Azure Cosmos DB med Time to Live
description: Med TTL tillhandahåller Microsoft Azure Cosmos DB möjligheten att låta dokument automatiskt bort från systemet efter en viss tidsperiod.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 692e0ec575904ff0a70b8c73268d2df62e776bb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978780"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live (TTL) i Azure Cosmos DB 

Med **Time to Live** eller TTL, Azure Cosmos DB ger dig möjlighet att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Du kan ange tiden för live på behållarenivån och åsidosättningsvärde på basis av per objekt som standard. När du ställer in TTL-Perioden på en behållare eller på en nivå, tar Azure Cosmos DB automatiskt bort dessa objekt efter hur lång tid sedan du de senast ändrades. Tid TTL-värde konfigureras på några sekunder. När du konfigurerar TTL systemet automatiskt att ta bort inaktuella objekt baserat på TTL-värdet utan att behöva en borttagningsåtgärd som uttryckligen har utfärdats av klientprogrammet.

## <a name="time-to-live-for-containers-and-items"></a>TTL-värde för behållare och objekt

Tiden TTL-värde har angetts i sekunder och tolkas det som en lista från den tidpunkt då ett objekt senast ändrades. Du kan ställa in TTL-värde på en behållare eller ett objekt i behållaren:

1. **Time to Live-värde i en behållare** (anges med `DefaultTimeToLive`):

   - Om det saknas (eller inställt på null) objekt inte har förfallit automatiskt.

   - Om närvarande och värdet är inställt på ”-1”, är det lika infinity och objekt som inte upphör att gälla som standard.

   - Om närvarande och värdet anges till något nummer *”n”* – objekt förfaller *”n”* sekunder efter att deras senast ändrades.

2. **Time to Live-värde på ett objekt** (anges med `ttl`):

   - Den här egenskapen gäller bara om `DefaultTimeToLive` finns och inte har angetts till null för den överordnade behållaren.

   - Om det finns åsidosätter den `DefaultTimeToLive` värdet för den överordnade behållaren.

## <a name="time-to-live-configurations"></a>Tid för Live-konfigurationer

* Om TTL-värde har angetts till *”n”* på en behållare, sedan objekten i den behållaren upphör att gälla efter *n* sekunder.  Om det finns objekt i samma behållare som har sina egna tid att live genom att ange -1 (som anger att de inte går ut) eller om några objekt har åsidosatts time to live inställning med ett annat nummer gäller de här objekten går ut baserat på sina egna konfigurerade TTL-värdet. 

* Om TTL inte har angetts för en behållare har ingen effekt med time to live på ett objekt i den här behållaren. 

* Om TTL-värdet från en behållare har värdet-1, ett objekt i den här behållaren som innehåller time to live inställt n, upphör att gälla efter n sekunder och återstående objekt upphör inte att gälla. 

Tar bort objekt baserat på TTL är kostnadsfri. Det finns ingen extra kostnad (det vill säga används inga ytterligare ru: er) när objektet tas bort till följd av TTL upphör.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar Time to Live i följande artiklar:

* [Så här konfigurerar du Time to Live](how-to-time-to-live.md)
