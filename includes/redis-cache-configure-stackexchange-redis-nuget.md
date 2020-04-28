---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "68286227"
---
.NET-program kan använda cacheklienten **StackExchange.Redis**. Den kan konfigureras i Visual Studio med ett NuGet-paket som förenklar konfigurationen av cacheklientprogram. 

> [!NOTE]
> Mer information finns på sidan [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) GitHub och [stackexchange. Azure cache för Redis-klient dokumentation](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Om du vill konfigurera ett klientprogram i Visual Studio med hjälp av NuGet-paketet för StackExchange.Redis, högerklickar du på projektet i **Solution Explorer** och väljer **Hantera NuGet-paket**. 

![Hantera NuGet-paket](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Skriv **StackExchange.Redis** eller **StackExchange.Redis.StrongName** i sökrutan, markera den önskade versionen i resultaten och klicka på **Installera**.

> [!NOTE]
> Om du vill använda en starkt krypterad version av klientbiblioteket **StackExchange.Redis** väljer du **StackExchange.Redis.StrongName**, annars väljer du **StackExchange.Redis**.
>
>

![NuGet-paket för StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna för klientprogrammet för att få åtkomst till Azure Cache for Redis med cacheklienten StackExchange.Azure Cache for Redis.

> [!NOTE]
> Om du tidigare har konfigurerat ditt projekt till att använda StackExchange.Redis kan du söka efter uppdateringar till paketet från **NuGet Package Manager**. Om du vill söka efter och installera uppdaterade versioner av paketet StackExchange. Redis NuGet klickar du på **uppdateringar** i fönstret **NuGet Package Manager** . Om det finns en uppdatering av paketet StackExchange.Redis NuGet kan du uppdatera ditt projekt för att använda den uppdaterade versionen.
>
>

Du kan också installera StackExchange.Redis NuGet-paketet genom att klicka på **NuGet Package Manager**, klicka på **Package Manager Console** på **Tools**-menyn och köra följande kommando från fönstret **Package Manager-konsol**.

```
Install-Package StackExchange.Redis
```
