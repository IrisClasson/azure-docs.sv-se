---
title: Gränser för begäran och begränsning, Azure Resource Manager
description: Beskriver hur du använder begränsningar med Azure Resource Manager-begäranden när prenumerationsbegränsningar har uppnåtts.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: f457b316d9f499f2cab02452c1b03ad07a9aef27
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302834"
---
# <a name="throttling-resource-manager-requests"></a>Begränsningsbegäranden Resource Manager

För varje Azure-prenumeration och en klient, Resource Manager kan upp till 12 000 läsa förfrågningar per timme och 1 200 skriva förfrågningar per timme. Dessa gränser är begränsade till säkerhetsobjektet (användare eller program) som gör begär Anden och prenumerations-ID eller klient-ID. Om dina begär Anden kommer från fler än ett säkerhets objekt är gränsen för din prenumeration eller klient större än 12 000 och 1 200 per timme.

Begäranden som tillämpas på din prenumeration eller din klient. Prenumerations förfrågningar är sådana som innebär att du skickar ditt prenumerations-ID, till exempel att hämta resurs grupperna i din prenumeration. Klient-begäranden inkluderar inte ditt prenumerations-ID, till exempel hämta giltiga Azure-platser.

Dessa begränsningar gäller för varje Azure Resource Manager-instans. Det finns flera instanser i varje Azure-region och Azure Resource Manager distribueras till alla Azure-regioner.  Så i praktiken gränserna är effektivt mycket högre än de här gränserna, som användare är begäranden vanligtvis underhålls av många olika instanser.

Om ditt program eller skript når gränserna kan behöva du begränsa dina önskemål. Den här artikeln visar hur du avgör de återstående förfrågningar som du har innan du når gränsen och hur du svarar när du har nått gränsen.

När du når gränsen kan du få HTTP-statuskoden **429 för många begäranden**.

Azure Resource Graph begränsar antalet begär anden till åtgärder. Stegen i den här artikeln för att fastställa de återstående förfrågningarna och hur du svarar när gränsen nås gäller även för resurs diagram. Resurs diagram anger dock sin egen gräns och återställnings takt. Mer information finns i avsnittet [om begränsning i Azure Resource Graph](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Återstående begäranden
Du kan bestämma antalet återstående begäranden genom att undersöka svarshuvuden. Läs begär Anden returnerar ett värde i rubriken för antalet återstående Läs begär Anden. Skriv förfrågningar innehåller ett värde för antalet återstående Skriv förfrågningar. I följande tabell beskrivs de svarshuvuden som du kan undersöka för dessa värden:

| Svarshuvud | Beskrivning |
| --- | --- |
| x-MS-ratelimit-Remaining-Subscription-Reads |Prenumeration som omfattar läser återstående. Det här värdet returneras på läsåtgärder. |
| x-MS-ratelimit-Remaining-Subscription-Writes |Prenumeration som omfattar skriver återstående. Det här värdet returneras på skrivåtgärder. |
| x-MS-ratelimit-Remaining-tenant-Reads |Klient begränsad läser återstående |
| x-MS-ratelimit-Remaining-tenant-Writes |Klient begränsad skriver återstående |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests |Prenumeration begränsade typen resursbegäranden återstående.<br /><br />Det här rubrikvärdet returneras bara om en tjänst har åsidosatts Standardgränsen. Resource Manager lägger till det här värdet i stället för den prenumeration läsåtgärder eller skrivåtgärder. |
| x-MS-ratelimit-Remaining-Subscription-Resource-entities-Read |Prenumeration begränsade typen samling resursbegäranden återstående.<br /><br />Det här rubrikvärdet returneras bara om en tjänst har åsidosatts Standardgränsen. Det här värdet visar antalet begäranden om återstående (lista resurser). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests |Klient begränsade typen resursbegäranden återstående.<br /><br />Den här rubriken läggs endast för begäranden på klientnivån och endast om en tjänst har åsidosättas Standardgränsen. Resource Manager lägger till det här värdet i stället för den klient läsåtgärder eller skrivåtgärder. |
| x-MS-ratelimit-Remaining-tenant-Resource-entities-Read |Klient begränsade typen samling resursbegäranden återstående.<br /><br />Den här rubriken läggs endast för begäranden på klientnivån och endast om en tjänst har åsidosättas Standardgränsen. |

## <a name="retrieving-the-header-values"></a>Hämta huvudvärden
Hämtar dessa värden i huvudet i din kod eller skript skiljer än att hämta alla huvudets värde. 

Till exempel i **C#** , du hämta huvudvärde från en **HttpWebResponse** objekt med namnet **svar** med följande kod:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

I **PowerShell**, du hämta huvudets värde från en Invoke-WebRequest-åtgärd.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

En fullständig PowerShell-exempel finns i [Kontrollera Resource Manager-gränserna för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Om du vill se begäranden för felsökning kan du ange den **-felsöka** parametern på din **PowerShell** cmdlet.

```powershell
Get-AzResourceGroup -Debug
```

Som returnerar flera värden, inklusive följande Svarsvärde:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Använd en skrivåtgärd för att få skrivning gränser kan: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Som returnerar flera värden, inklusive följande värden:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

I **Azure CLI**, du hämta huvudets värde med hjälp av alternativet mer utförlig.

```azurecli
az group list --verbose --debug
```

Som returnerar flera värden, inklusive följande värden:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Använd en skrivåtgärd för att få skrivning gränser kan: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Som returnerar flera värden, inklusive följande värden:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Att vänta innan nästa begäran skickas
När du når gränsen för förfrågningar Resource Manager returnerar den **429** HTTP-statuskoden och en **Retry-After** värdet i huvudet. Den **Retry-After** värdet anger hur många sekunder som programmet bör vänta (eller strömsparläge) innan nästa förfrågan skickas. Om du skickar en begäran innan återförsöksvärdet har gått ut kan bearbeta inte din begäran och ett nytt försök värde returneras.

## <a name="next-steps"></a>Nästa steg

* En fullständig PowerShell-exempel finns i [Kontrollera Resource Manager-gränserna för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Mer information om begränsningar och kvoter finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
* Läs om hur du hanterar asynkrona REST-begäranden i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).
