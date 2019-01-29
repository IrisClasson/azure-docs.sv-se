---
title: Starta en åtkomstgranskning med Azure AD-Åtkomstgranskningar | Microsoft Docs
description: Lär dig mer om att starta en åtkomstgranskning med hjälp av Azure Active Directory-Åtkomstgranskningar.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: bac5ddc1ee6f002781dc73845773379804a2ac1c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179403"
---
# <a name="start-an-access-review-with-azure-ad-access-reviews"></a>Starta en åtkomstgranskning med Azure AD-Åtkomstgranskningar

Azure Active Directory (AD Azure) gör det enklare företag hantera åtkomst till program och medlemmar i grupper i Azure AD och andra Microsoft Online Services med en funktion som kallas åtkomst går igenom. Kanske du har fått ett e-postmeddelande från Microsoft som ber dig att granska åtkomst för medlemmar i en grupp eller användare med åtkomst till ett program. 

## <a name="open-an-access-review"></a>Öppna en åtkomstgranskning

Om du vill se de väntande åtkomstgranskningar, klickar du på länken granska åtkomst för e-postmeddelandet. Från och med augusti 2018, har e-postmeddelanden för Azure AD-roller en uppdaterad design. Nedan visas ett exempel e-postmeddelande som skickas när en användare bjuds in till granskare. 

![Granska åtkomst till e-post](./media/perform-access-review/new-ar-email.png)

Om du inte har e-postmeddelandet, kan du hitta åtkomstgranskningar genom att följa dessa steg:

1. Logga in på den [Azure AD-åtkomstpanelen](https://myapps.microsoft.com).

2. Välj symbolen användaren i det övre högra hörnet på sidan som visar organisationens namn och en standard. Om mer än en organisation visas, väljer du den organisation som har begärt en åtkomstgranskning.

3. Om en panel märkt **Åtkomstgranskningar** är till höger på sidan, väljer den. Om panelen inte visas, det finns inga åtkomstgranskningar att utföra för organisationen och ingen åtgärd krävs just nu.

## <a name="fill-out-an-access-review"></a>Fylla i en åtkomstgranskning

När du väljer en åtkomstgranskning i listan visas namnen på användare som behöver granskas. Visas endast en namn – din egen--om begäran var att granska din egen åtkomst.

För varje rad i listan kan du bestämma om du vill godkänna eller neka användaren åtkomst. Välj raden och välj om du vill godkänna eller neka. (Om du inte vet att användaren kan du ange som för.)

Granskaren kan kräva att du ange en motivering för godkännande av fortsatt åtkomst och gruppmedlemskap.

## <a name="next-steps"></a>Nästa steg

En användare nekas åtkomst tas inte bort direkt. Det kan tas bort när granskningen är klar eller när en administratör slutar granskningen. Om du vill ändra ditt svar och godkänna en tidigare nekade användare eller neka en tidigare godkända användare, Välj raden, återställa svaret och välj ett nytt svar. Du kan göra det här steget tills åtkomstgranskningen är klar.



