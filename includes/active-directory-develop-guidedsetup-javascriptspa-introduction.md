---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8d0b0c99b743461da41a518feae9c781f90c8b63
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988454"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Anropa Microsoft Graph API från ett JavaScript ensidesapplikation (SPA)

Den här guiden visar hur en JavaScript ensidesapplikation (SPA) kan logga in Mina, uppgifter och skolkonton, få en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som kräver åtkomsttoken från Azure Active Directory v2.0-slutpunkten.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Så här fungerar exempelapp som genererats av den här guiden](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Exempelprogrammet som skapats av den här guiden kan ett JavaScript SPA att fråga Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2.0-slutpunkten. Det här scenariot när en användare loggar in, har en åtkomsttoken begärt och läggs till HTTP-förfrågningar via auktoriseringsrubriken. Tokenförvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library för JavaScript-förhandsversion|

> [!NOTE]
> *msal.js* mål den *Azure Active Directory v2.0-slutpunkten* – vilket gör att personliga, skolan och arbetsrelaterade konton att logga in och hämta token. Den *Azure Active Directory v2.0-slutpunkten* har [vissa begränsningar](..\articles\active-directory\develop\active-directory-v2-limitations.md).
> Att förstå skillnaderna mellan slutpunkterna v1.0 och v2.0 läsa den [Endpoint-Jämförelseguide](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
