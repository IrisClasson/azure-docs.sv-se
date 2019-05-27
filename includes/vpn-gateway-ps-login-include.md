---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157460"
---
Innan du påbörjar den här konfigurationen måste du logga in på kontot. Cmdleten uppmanar dig logga in autentiseringsuppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](../articles/powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen med förhöjd behörighet för att logga in och Anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```powershell
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
