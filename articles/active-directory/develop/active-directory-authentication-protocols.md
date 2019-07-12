---
title: Azure Active Directory-autentiseringsprotokoll | Microsoft Docs
description: En översikt över protokoll för autentisering som stöds av Azure Active Directory (AD)
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964c5882432ae0637039e32ca961008e8223b6b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612573"
---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory-autentiseringsprotokoll
Azure Active Directory (Azure AD) stöder flera av de mest använda protokoll för autentisering och auktorisering. I det här avsnittet beskrivs protokoll som stöds och deras implementering i Azure AD. Ämnena ingår en granskning av anspråkstyper med stöd, en introduktion till användningen av federationsmetadata, detaljerad OAuth 2.0. och referensdokumentation för SAML 2.0-protokollet och instruktioner för felsökning.

## <a name="authentication-protocols-articles-and-reference"></a>Autentiseringsprotokoll artiklar och referens
* [Viktig Information om signering nyckel förnya i Azure AD](active-directory-signing-key-rollover.md) – Lär dig mer om Azure Active Directorys signering signeringsnyckelförnyelsekadens ändringar du gör för att uppdatera nyckeln automatiskt och beskrivning för hur du uppdaterar de vanligaste programscenarierna.
* [Token och anspråkstyper stöds](v1-id-and-access-tokens.md) – Lär dig mer om anspråken i token som Azure AD utfärdar.
* [Federationsmetadata](azure-ad-federation-metadata.md) – Lär dig hur du hittar och tolka metadata-dokument som genererar Azure AD.
* [OAuth 2.0 i Azure AD](v1-protocols-oauth-code.md) – Lär dig mer om implementering av OAuth 2.0 i Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) – Lär dig hur du använder OAuth 2.0, eventuellt auktoriseringsprotokoll för autentisering.
* [Tjänst till tjänst-anrop med klientautentiseringsuppgifter](v1-oauth2-client-creds-grant-flow.md) – Lär dig hur du använder OAuth 2.0 bevilja flödet för tjänst till tjänst-anrop.
* [Tjänst till tjänst-anrop med Behalf Flow](v1-oauth2-on-behalf-of-flow.md) – Lär dig hur du använder OAuth 2.0 Behalf flow för tjänst till tjänst-anrop.
* [Referens för SAML-protokollet](active-directory-saml-protocol-reference.md) – Lär dig mer om Azure AD enkel inloggning och enkel utloggning SAML-profiler.

## <a name="see-also"></a>Se även
[Utvecklarhandbok för Azure Active Directory](v1-overview.md)

[Active Directory-kodexempel](sample-v1-code.md)
