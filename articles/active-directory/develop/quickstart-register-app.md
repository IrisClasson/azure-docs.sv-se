---
title: Registrera en app med Microsoft identity-plattformen – Microsoft identity-plattformen
description: Lär dig hur du lägger till och registrerar ett program med Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ec1d5fb13d7fb191bfee6194e69a217bcaddbb9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482574"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snabbstart: Registrera ett program med Microsoft identity-plattformen

Företagsutvecklare och SaaS-leverantörer (software-as-a-service) kan utveckla kommersiella molntjänster och affärsprogram som kan integreras med Microsoft Identity Platform och då ger säker inloggning och behörighet till tjänsterna.

Den här snabbstarten visar hur du lägger till och registrera ett program med hjälp av den **appregistreringar** upplevelse i Azure portal så att din app kan integreras med Microsoft identity-plattformen. Mer information om nya funktioner och förbättringar i den nya upplevelsen för appregistreringar finns [det här blogginlägget](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrera ett nytt program med hjälp av Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du den **Azure Active Directory** tjänsten och välj sedan **appregistreringar > Ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:

   - **Namn** – ange ett beskrivande programnamn som ska visas för appens användare.
   - **Kontotyper som stöds** – välj vilka konton som du vill att programmet ska stödja.

       | Kontotyper som stöds | Beskrivning |
       |-------------------------|-------------|
       | **Endast konton i den här organisationskatalogen** | Välj det här alternativet om du skapar en verksamhetsspecifik app. Det här alternativet är inte tillgängligt om du inte registrerar programmet i en katalog.<br><br>Det här alternativet mappar endast till Azure AD för en enskild klientorganisation.<br><br>Det här är standardalternativet såvida du inte registrerar appen utanför en katalog. I fall då appen registreras utanför en katalog är standardalternativet Azure AD för flera klientorganisationer och personliga Microsoft-konton. |
       | **Konton i valfri organisationskatalog** | Välj det här alternativet om du vill rikta dig till mot alla företags- och utbildningskunder.<br><br>Det här alternativet mappar endast till Azure AD för flera klientorganisationer.<br><br>Om du registrerade appen som endast Azure AD för en enskild klientorganisation kan du uppdatera den till att bli endast Azure AD för flera klientorganisationer och tillbaka till en enskild klientorganisation via bladet **Autentisering**. |
       | **Konton i en valfri organisationskatalog och personliga Microsoft-konton** | Välj det här alternativet om målgruppen är bredast möjliga uppsättning av kunder.<br><br>Det här alternativet mappar till Azure AD för flera klientorganisationer och personliga Microsoft-konton.<br><br>Om du registrerade appen som Azure AD för flera klientorganisationer och personliga Microsoft-konton kan du inte ändra detta i användargränssnittet. Du måste i stället använda redigeringsprogrammet för applikationsmanifest för att ändra de kontotyper som stöds. |

   - **Omdirigerings-URI (valfritt)** – välj den typ av app som du skapar, **Webb** eller **Offentlig klient (mobila och skrivbord)** , och sedan ange omdirigerings-URI (eller svars-URL) för programmet.
       - För webbappar anger du grundläggande URL för appen. Till exempel kan `http://localhost:31544` vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram.
       - För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel `myapp://auth`.

     Specifika exempel på webbappar och interna program finns i [snabbstarterna](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. När det är klart väljer du **Registrera**.

    [![Visar skärmen för att registrera ett nytt program i Azure portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD tilldelar ett unikt program-ID (klient) till din app, och du kommer till programmets **översiktssida**. Om du vill lägga till ytterligare funktioner i programmet kan du välja andra konfigurationsalternativ inklusive varumärkesanpassning, certifikat och hemligheter, API-behörigheter och mer.

[![Exempel på översiktssidan för en nyligen registrerad app](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [behörigheter och medgivande](v2-permissions-and-consent.md).
- Om du vill aktivera konfigurationsfunktioner i din programregistrering, till exempel autentiseringsuppgifter och behörigheter och aktivera inloggning för användare från andra klientorganisationer kan du läsa följande snabbstarter:
    - [Konfigurera ett klientprogram för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md)
    - [Konfigurera ett program att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md)
    - [Ändra konton som stöds av ett program](quickstart-modify-supported-accounts.md)
- Välj en [snabbstart](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) för att snabbt bygga en app och lägga till funktioner som att hämta token, uppdatera token, logga in en användare, visa användarinformation med mera.
- Mer information om de två Azure AD-objekt som representerar ett registrerat program och relationen mellan dem finns i [Programobjekt och tjänsthuvudnamnsobjekt](app-objects-and-service-principals.md).
- Mer information om de varumärkesriktlinjer som du bör använda när du utvecklar appar finns i [Varumärkesriktlinjer för program](howto-add-branding-in-azure-ad-apps.md).
