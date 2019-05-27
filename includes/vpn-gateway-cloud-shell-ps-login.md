---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146982"
---
Öppna PowerShell-konsolen med förhöjd behörighet.



Om du kör Azure PowerShell lokalt måste du ansluta till ditt Azure-konto. Den *Connect AzAccount* cmdlet uppmanar dig att autentiseringsuppgifter. När autentisering, hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell. Hoppa över detta steg om du inte kör PowerShell lokalt och i stället använder Azure Cloud Shell ”prova” i webbläsaren. Du ansluter till ditt Azure-konto automatiskt.

```azurepowershell
Connect-AzAccount
```

Om du har mer än en prenumeration kan du hämta en lista över dina Azure-prenumerationer.

```azurepowershell-interactive
Get-AzSubscription
```

Ange den prenumeration som du vill använda.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```