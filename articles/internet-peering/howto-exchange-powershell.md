---
title: Skapa eller ändra en Exchange-peering med hjälp av PowerShell
titleSuffix: Azure
description: Skapa eller ändra en Exchange-peering med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 1c0620875e7da4582156080c4a52fd6fbe0e573c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700254"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Skapa eller ändra en Exchange-peering med hjälp av PowerShell

Den här artikeln beskriver hur du skapar en Microsoft Exchange-peering med hjälp av PowerShell-cmdletar och distributions modellen för Resource Manager. Den här artikeln visar också hur du kontrollerar status för resursen, uppdaterar den eller tar bort och avetablerar den.

Om du vill kan du slutföra den här guiden med hjälp av Azure [Portal](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
* Granska [kraven](prerequisites.md) och [genom gången av Exchange-peering](walkthrough-exchange-all.md) innan du påbörjar konfigurationen.
* Om du redan har Exchange-peering med Microsoft som inte har konverterats till Azure-resurser, se [konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Skapa och etablera en Exchange-peering

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Hämta listan över peering-platser som stöds för Exchange-peering
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Skapa en Exchange-peering
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Hämta Exchange-peering
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Ändra en Exchange-peering
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Avetablera en Exchange-peering

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av PowerShell](howto-direct-powershell.md)
* [Konvertera en äldre direkt peering till en Azure-resurs med hjälp av PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser
Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```powershell
Get-Help Get-AzPeering -detailed
```

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md).
