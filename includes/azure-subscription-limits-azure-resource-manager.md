---
title: ta med fil
description: ta med fil
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 4cc115d068736f61f9edb4ec609ac592607e7fa0
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247185"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella datorer per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |10 000 <sup>1</sup> per region |10 000 per region |
| Totalt antal VM-kärnor per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region | Kontakta supporten |
| VM-kärnor per serie (Dv2, F osv.) per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per region | Kontakta supporten |
| [Medadministratörer](../articles/billing-add-change-azure-subscription-administrator.md) per prenumeration |Obegränsat |Obegränsat |
| [Storage-konton](../articles/storage/common/storage-quickstart-create-account.md) per region per prenumeration |200 |200<sup>2</sup> |
| [Resursgrupper](../articles/azure-resource-manager/resource-group-overview.md) per prenumeration |980 |980 |
| [Tillgänglighetsuppsättningar](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per prenumeration |2 000 per region |2 000 per region |
| Storleksförfrågningar med Resource Manager API |4 194 304 byte |4 194 304 byte |
| Taggar per prenumeration<sup>3</sup> |obegränsat |obegränsat |
| Unika taggberäkningar per prenumeration<sup>3</sup> | 10 000 | 10 000 |
| [Molntjänster](../articles/cloud-services/cloud-services-choose-me.md) per prenumeration |Inte tillämpligt<sup>4</sup> |Inte tillämpligt<sup>4</sup> |
| [Tillhörighetsgrupper](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per prenumeration |Inte tillämpligt<sup>4</sup> |Inte tillämpligt<sup>4</sup> |
| [Prenumeration på distributioner](../articles/azure-resource-manager/deploy-to-subscription.md) per plats | 800 | 800 |

<sup>1</sup>Standardgränserna varierar beroende på erbjudandets kategorityp, t.ex. kostnadsfri utvärderingsversion, betala per användning och serie, t.ex. Dv2, F eller G.

<sup>2</sup>Detta gäller både Standard- och Premium-lagringskonton. Om du behöver mer än 200 lagringskonton skickar du en begäran via [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton.

<sup>3</sup>Du kan använda ett obegränsat antal taggar per prenumeration. Antalet taggar per resurs eller resursgrupp är begränsat till 15. Resource Manager returnerar endast en [lista med unika taggnamn och taggvärden](/rest/api/resources/tags) i prenumerationen om antalet taggar är 10 000 eller mindre. Dock kan du fortfarande söka efter en resurs baserat på taggar om antalet överstiger 10 000.  

<sup>4</sup>Dessa funktioner krävs inte längre med Azure-resursgrupper och Azure Resource Manager.

> [!NOTE]
> Det är viktigt att betona att virtuella datorkärnor har en regional total gräns och en regional gräns per storleksserie (Dv2, F osv.) som tillämpas separat.  Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30.  Den här prenumerationen kan distribuera 30 virtuella datorer i A1-serien eller 30 virtuella datorer i D1-serien, eller en kombination av båda som inte överstiger sammanlagt 30 kärnor (t.ex. 10 virtuella datorer i A1-serien och 20 virtuella datorer i D1-serien).  
> <!-- -->
> 
> 

