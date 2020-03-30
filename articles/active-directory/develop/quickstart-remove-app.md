---
title: Ta bort app som är registrerad på Microsofts identitetsplattform | Azure
description: Lär dig hur du tar bort ett program som registrerats med Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 3cc9e4458f14a63bad7f484bc16683248895ede9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240851"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Snabbstart: Ta bort ett program som registrerats med Microsofts identitetsplattform

Företagsutvecklare och SaaS-leverantörer (Software as a Service) som har registrerat program med Microsoft Identity Platform kan behöva ta bort en programregistrering.

I den här snabbstarten lär du dig att:

* Ta bort ett program som skapats av dig eller din organisation
* Ta bort ett program som skapats av en annan organisation

## <a name="prerequisites"></a>Krav

Du måste ha en klient som har program registrerade. Om du vill lära dig lägga till och registrera appar kan du läsas [Registrera en app på Microsoft Identity Platform](quickstart-register-app.md).

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Ta bort ett program som skapats av dig eller din organisation

Program som du eller din organisation har registrerat representeras av både ett programobjekt och ett tjänsthuvudnamnsobjekt i din klientorganisation. Mer information finns i [Programobjekt och tjänsthuvudnamnsobjekt](active-directory-application-objects.md).

### <a name="to-remove-an-application"></a>Så tar du bort ett program

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
2. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
3. I det vänstra navigeringsfönstret väljer du **Azure Active Directory-tjänsten** och väljer sedan **Appregistreringar**. Leta upp och välj det program som du vill konfigurera. När du har valt appen ser du programmets **översiktssida**.
4. På sidan **Översikt** väljer du **Ta bort**.
5. Välj **Ja** för att bekräfta att du vill ta bort appen.

   > [!NOTE]
   > Om du vill ta bort ett program måste vara angiven som ägare av programmet eller ha administratörsbehörighet.

## <a name="remove-an-application-authored-by-another-organization"></a>Ta bort ett program som skapats av en annan organisation

Om du visar **Appregistreringar** i kontexten för en klientorganisation kommer en delmängd av de program som visas under fliken **Alla appar** från en annan klientorganisation och registrerades i din klientorganisation under medgivandeprocessen. Mer specifikt representeras de endast av en tjänsthuvudnamnsobjekt i din klientorganisation utan motsvarande programobjekt. Mer information om skillnaderna mellan program- och tjänsthuvudnamnsobjekt finns i [Programobjekt och tjänsthuvudnamnsobjekt i Azure AD](active-directory-application-objects.md).

För att kunna ta bort åtkomsten för ett program till din katalog (efter att medgivande har givits) måste företagets administratör ta bort dess tjänsthuvudnamn. Administratören måste ha behörighet som global administratör och kan ta bort programmet via Azure-portalen eller använda [Azure AD PowerShell-cmdletarna](https://go.microsoft.com/fwlink/?LinkId=294151) för att ta bort åtkomst.

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa andra relaterade snabbstarter för apphantering:

* [Registrera en app på Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurera ett klientprogram för att komma åt webb-API:er](quickstart-configure-app-access-web-apis.md)
* [Konfigurera en app att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md)
* [Ändra konton som stöds av en app](quickstart-modify-supported-accounts.md)
