---
title: Hantera Azure Media Services v3-konton | Microsoft Docs
description: För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. Den här artikeln förklarar hur du hanterar Azure Media Services v3-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 46f5644fb47b840f273ccab37da4f4743ad335f3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053118"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Hantera Azure Media Services v3-konton

För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Mer information finns i [lagrings konton](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Flytta ett Media Services konto mellan prenumerationer 

Om du behöver flytta ett Media Services konto till en ny prenumeration måste du först flytta hela resurs gruppen som innehåller Media Servicess kontot till den nya prenumerationen. Du måste flytta alla anslutna resurser: Azure Storage konton, Azure CDN profiler osv. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Precis som med alla resurser i Azure kan det ta en stund att flytta resurs gruppen.

> [!NOTE]
> Media Services v3 stöder flera innehavares modeller.

### <a name="considerations"></a>Överväganden

* Skapa säkerhets kopior av alla data i ditt konto innan du migrerar till en annan prenumeration.
* Du måste stoppa alla slut punkter för direkt uppspelning och Live streaming-resurser. Användarna kommer inte att kunna komma åt ditt innehåll under hela resurs grupps flyttningen. 

> [!IMPORTANT]
> Starta inte slut punkten för direkt uppspelning förrän flyttningen har slutförts.

### <a name="troubleshoot"></a>Felsök 

Försök att rotera lagrings konto nycklarna om ett Media Services konto eller ett associerat Azure Storage-konto blir "frånkopplat" efter flyttningen av resurs gruppen. Om rotationen av lagrings konto nycklarna inte löser den "frånkopplade" statusen för Media Services-kontot, så kan du skicka en ny supportbegäran från menyn "support + fel sökning" i Media Services-kontot.  

## <a name="next-steps"></a>Nästa steg

[Skapa ett konto](./create-account-howto.md)
