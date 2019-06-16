---
title: Rensa token-cache med hjälp av Microsoft Authentication Library för .NET – Azure
description: Lär dig mer om att rensa token cacheminnet med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544124"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Rensa token cacheminnet med hjälp av MSAL.NET

När du [hämta en åtkomsttoken](msal-acquire-cache-tokens.md) med hjälp av Microsoft Authentication Library för .NET (MSAL.NET), token cachelagras. När programmet behöver en token, bör det först anropa den `AcquireTokenSilent` metod för att verifiera om det är en godtagbar token i cacheminnet. 

Rensa cacheminnet uppnås genom att ta bort konton från cachen. Sessions-cookie som är i webbläsaren, men tas inte bort.  I följande exempel skapar en instans av en offentlig klientprogram, hämtar kontona för programmet och tar bort konton.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Läs mer om hämtar och cachelagring token [hämta en åtkomsttoken](msal-acquire-cache-tokens.md).
