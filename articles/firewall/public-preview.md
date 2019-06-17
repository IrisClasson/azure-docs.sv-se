---
title: Aktivera den offentliga förhandsversionen av Azure-brandvägg
description: Använd Azure PowerShell för att aktivera den offentliga förhandsversionen av Azure-brandvägg
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193070"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Aktivera den offentliga förhandsversionen av Azure-brandvägg

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Aktivera med hjälp av Azure PowerShell

Om du vill aktivera den offentliga förhandsversionen av Azure-brandväggen, använder du följande Azure PowerShell-kommandon:

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Det tar upp till 30 minuter för funktionsregistrering av att slutföra. Du kan kontrollera din registreringsstatus som genom att köra följande Azure PowerShell-kommandon:

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
När registreringen är klar kör du följande kommando:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)

