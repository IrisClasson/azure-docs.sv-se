---
title: Anslut Azure AD Identity Protection data till Azure Sentinel
description: Lär dig att strömma loggar och varningar från Azure AD Identity Protection till Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: 69ab76bf213653ea10db8dfd181b615a7e0f47b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564476"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Anslut data från Azure Active Directory (Azure AD) Identity Protection

Du kan strömma loggar från [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) till Azure Sentinel för att strömma aviseringar till Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Azure Active Directory Identity Protection ger en samlad vy över risk användare, risk identifiering och sårbarheter, med möjlighet att åtgärda risker direkt och ange principer för att automatiskt reparera framtida händelser. Tjänsten bygger på Microsofts erfarenhet av att skydda konsument identiteter och får en fantastisk precision från signalen från över 13 000 000 000 inloggningar per dag. 

## <a name="prerequisites"></a>Krav

- Du måste ha en [Azure AD Premium P2-prenumeration](https://azure.microsoft.com/pricing/details/active-directory/).
- Du måste ha en användare med behörighet som global administratör eller säkerhets administratör.


## <a name="connect-to-azure-ad-identity-protection"></a>Anslut till Azure AD Identity Protection

Om du har en Azure AD Premium P2-prenumeration ingår Azure AD Identity Protection. Om några [principer är aktiverade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) och genererar aviseringar kan aviserings data enkelt strömmas till Azure Sentinel.

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure AD Identity Protection** .

1. Klicka på **Anslut** för att börja strömma Azure AD Identity Protection händelser till Azure Sentinel.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure AD Identity Protection aviseringar söker du efter **SecurityAlert**.

Om du vill testa anslutningen kan du [simulera identifieringar](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) för att generera exempel aviseringar som kommer att strömmas i Azure Sentinel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure AD Identity Protection till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
