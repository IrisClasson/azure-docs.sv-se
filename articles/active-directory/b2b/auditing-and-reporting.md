---
title: Granskning och rapportering en B2B-samarbete användare – Azure Active Directory | Microsoft Docs
description: Egenskaper för gäst-användare kan konfigureras i Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 506721a23a5a18eaea76efaca61df49f20dc9228
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65812471"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Granskning och rapportering av en användare för B2B-samarbete
Med gästanvändare har du granskningsfunktioner liknar med användare. 

## <a name="access-reviews"></a>Åtkomstgranskningar
Du kan använda åtkomstgranskningar regelbundet kontrollerar om gästanvändare fortfarande behöver åtkomst till dina resurser. Den **Åtkomstgranskningar** funktionen är tillgänglig i **Azure Active Directory** under **hantera** > **organisationens relationer**. (Du kan också söka efter ”åtkomstgranskningar” från **alla tjänster** i Azure-portalen.) Läs hur du använder åtkomstgranskningar i [hantera gäståtkomst med Azure AD åtkomst går igenom](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Granskningsloggar

Azure AD-audit-loggarna ger poster i system- och aktiviteter, inklusive aktiviteter som initierats av gästanvändare. Åtkomst till granskningsloggar, på **Azure Active Directory**under **övervakning**väljer **granskningsloggar**. Här är ett exempel på inbjudan och inlösen historiken för inbjudens Sam Oogle:

![Skärmbild som visar och exempel på audit logga utdata](./media/auditing-and-reporting/audit-log.png)

Du kan fördjupa dig i var och en av dessa händelser för att hämta information. Exempelvis kan du nu ska vi titta detaljinformationen godkännande.

![Skärmbild som visar och exempel på information om aktivitetsutdata](./media/auditing-and-reporting/activity-details.png)

Du kan också exportera dessa loggar från Azure AD och använda Rapporteringsverktyg föredrar för att hämta anpassade rapporter.

### <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)

