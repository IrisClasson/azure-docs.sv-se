---
title: Vad är Azure Active Directory? – Azure Active Directory | Microsoft Docs
description: Översikt och konceptuell information om Azure Active Directory, inklusive terminologi, vilka licenser är tillgängliga och en lista över associerade funktioner med länkar till mer information.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 05/08/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fafa7bd95801be46025727b2261fc95bc539988
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440533"
---
# <a name="what-is-azure-active-directory"></a>Vad är Azure Active Directory?

Azure Active Directory (Azure AD) är Microsofts molnbaserade identitets- och management-tjänsten, som hjälper till att dina anställda använder logga in och komma åt resurser i:

- Externa resurser, som Microsoft Office 365, Azure-portalen och tusentals andra SaaS-program.

- Interna resurser, som appar i företagets nätverk och intranät, tillsammans med molnappar som utvecklats av din egen organisation.

Du kan använda olika [Microsoft Cloud for Enterprise Architects Series](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity)-affischer för att bättre förstå de grundläggande identitetstjänsterna i Azure, Azure AD och Office 365.

## <a name="who-uses-azure-ad"></a>Vem använder Azure AD?

Azure AD är avsedd för:

- **IT-administratörer.** Som IT-administratör kan du använda Azure AD till att kontrollera åtkomsten till appar och appresurser, utifrån dina affärsbehov. Du kan till exempel använda Azure AD till att kräva multifaktorautentisering vid åtkomst av viktiga organisationsresurser. Dessutom kan du använda Azure AD till att automatisera användaretablering mellan en befintlig Windows Server AD och molnappar, till exempel Office 365. Och till sist: Azure AD ger dig kraftfulla verktyg för att automatiskt skydda användaridentiteter och autentiseringsuppgifter samt uppfylla dina åtkomststyrningskrav. Kom igång genom att registrera dig för en [kostnadsfri utvärderingsversion av Azure Active Directory Premium i 30 dagar](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Apputvecklare.** Som apputvecklare ger Azure AD dig en standardbaserad metod för att lägga till enkel inloggning (SSO) för appen, så att den kan fungera med en användares befintliga autentiseringsuppgifter. Azure AD tillhandahåller också API: er som kan hjälpa dig att skapa anpassade appupplevelser med hjälp av befintliga organisationsdata. Kom igång genom att registrera dig för en [kostnadsfri utvärderingsversion av Azure Active Directory Premium i 30 dagar](https://azure.microsoft.com/trial/get-started-active-directory/). Mer information finns också i [Azure Active Directory för utvecklare](../develop/index.yml).

- **Prenumeranter på Microsoft 365, Office 365, Azure eller Dynamics CRM Online.** Som prenumerant använder du redan Azure AD. Varje Microsoft 365-, Office 365-, Azure- och Dynamics CRM Online-klient är automatiskt en Azure AD-klient. Du börjar direkt att hantera åtkomsten till dina integrerade molnappar.

## <a name="what-are-the-azure-ad-licenses"></a>Vad är Azure AD-licenser?

Microsoft Online-företagstjänster, som Office 365 eller Microsoft Azure, kräver Azure AD för inloggning och för att hjälpa till med identitetsskydd. Om du prenumererar på alla Microsoft Online-företagstjänst får automatiskt Azure AD med åtkomst till alla kostnadsfria funktioner.

För att förbättra din Azure AD-implementering kan du lägga till betalfunktioner genom att uppgradera till Azure Active Directory Basic-, Premium P1- eller Premium P2-licenserna. Azure AD betald licenser är byggda på en befintlig kostnadsfria katalog med självbetjäning och förbättrad övervakning, säkerhetsrapportering och säker åtkomst för dina mobila användare.

>[!Note]
>Prisalternativ för de här licenserna finns i [prisinformation för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1, Premium P2 och Azure Active Directory Basic stöds för närvarande inte i Kina. Mer information om priser för Azure AD, kontaktar du den [Azure Active Directory-forumet](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Free.** Innehåller användar- och grupphantering, lokal katalogsynkronisering, grundläggande rapporter, ändring av lösenord för molnanvändare och enkel inloggning i Azure, Office 365 och många populära SaaS-appar.

- **Azure Active Directory Basic.** Utöver Free-funktionerna innehåller Basic även molncentrerad appåtkomst, gruppbaserad åtkomsthantering, lösenordsåterställning med självbetjäning för molnappar och Azure AD-programproxy, där du kan publicera webbappar med hjälp av Azure AD.

- **Azure Active Directory Premium P1.** Utöver Free- och Basic-funktionerna får du med P1 även funktioner för hybridanvändare att få åtkomst till både lokala och molnbaserade resurser. Det finns även stöd för avancerad administration, tom dynamiska grupper, grupphantering via självbetjäning, Microsoft Identity Manager (en lokal programsvit för identitets- och åtkomsthantering) och funktioner för tillbakaskrivning i molnet, med lösenordsåterställning via självbetjäning för dina lokala användare.

- **Azure Active Directory Premium P2.** Förutom funktionerna som är kostnadsfri, Basic och P1, P2 erbjuder även [Azure Active Directory Identity Protection](../identity-protection/enable.md) för att ge riskbaserad villkorlig åtkomst till dina appar och viktiga företagsdata och [Privileged Identity Hantering av](../privileged-identity-management/pim-getting-started.md) för att upptäcka, begränsa och övervaka administratörer och deras åtkomst till resurser och ger just-in-time-åtkomst när det behövs.

- **Funktionslicenser med användningsbaserad betalning.** Du finns även extra funktionslicenser, till exempel Azure Active Directory Business-to-Customer (B2C). B2C kan hjälpa dig att ge identitets- och åtkomsthanteringslösningar för dina kundriktade appar. Mer information finns i [Azure Active Directory B2C-dokumentationen](../../active-directory-b2c/index.yml).

Mer information om hur du kopplar en Azure-prenumeration till Azure AD finns i [: Hur du: Kopplar eller lägger till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md). Mer information om att tilldela licenser till användare finns i [Hur du: Tilldela eller ta bort licenser i Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologi

Att bättre förstå Azure AD och dess dokumentation, rekommenderar vi granska följande villkor.

|Term eller begrepp|Beskrivning|
|---------------|-----------|
|Identitet| En sak som kan få autentiseras. En identitet kan vara en användare med ett användarnamn och lösenord. Identiteter kan även innehålla program eller andra servrar som kan kräva autentisering via hemliga nycklar och certifikat.|
|Konto| En identitet som har data som är associerade med den. Du kan inte ha ett konto utan en identitet.|
|Azure AD-konto| En identitet som skapas via Azure AD eller någon annan Microsoft-molntjänst, till exempel Office 365. Identiteter lagras i Azure AD och är tillgängliga för organisationens prenumerationer på molntjänster. Det här kontot kallas ibland även för arbets- eller skolkonto.|
|Azure-prenumeration| Används för att betala för Azure-molntjänster. Du kan ha många prenumerationer och de är länkade till ett kreditkort.|
|Azure-klientorganisation| En dedikerad och betrodd instans av Azure AD som skapas automatiskt när organisationen registrerar sig för en Microsoft-molntjänstprenumeration som Microsoft Azure, Microsoft Intune eller Office 365. En Azure-klientorganisation representerar en enskild organisation.|
|Enskild klientorganisation| Azure-klienter som har åtkomst till andra tjänster i en dedikerad miljö betraktas som en enskild klientorganisation.|
|Flera innehavare| Azure-klientorganisationer som har åtkomst till andra tjänster i en delad miljö, i flera organisationer, anses vara för flera innehavare.|
|Azure AD-katalog|Varje Azure-klientorganisation har en dedikerad och betrodd Azure AD-katalog. Azure AD-katalogen innehåller klientorganisationens användare, grupper och appar och används till att utföra identitets- och åtkomsthanteringsfunktioner för klientresurser.|
|Anpassad domän|Varje ny Azure AD-katalog levereras med ett initialt domännamn, domännamn.onmicrosoft.com. Utöver det ursprungliga namnet kan du även lägga till organisationens domännamn, som inkluderar de namn du använder för att göra affärer och dina användare använder för att få åtkomst till organisationens resurser, i listan. När du lägger till anpassade domännamn kan du skapa användarnamn som är bekanta för dina användare, till exempel alain@contoso.com.|
|Kontoadministratör|Den här administratörsrollen för klassiska prenumerationer är begreppsmässigt faktureringsägaren av en prenumeration. Den här rollen har åtkomst till [Azure-kontocenter](https://account.azure.com/Subscriptions) och med den kan du hantera alla prenumerationer på ett konto. Mer information finns i [administratörsroller för klassiska prenumeration, roller för Azure rollbaserad åtkomstkontroll (RBAC) och Azure AD-administratörsroller](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Tjänstadministratör|Med den här administratörsrollen för klassiska prenumerationer kan du hantera alla Azure-resurser, inklusive åtkomst. Den här rollen har likvärdig åtkomst som en användare som har tilldelats rollen Ägare i prenumerationsomfånget. Mer information finns i [Administratörsroller för klassiska prenumerationer, Azure RBAC-roller och administratörsroller för Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Ägare|Den här rollen hjälper dig att hantera alla Azure-resurser, inklusive åtkomst. Den här rollen är byggd på ett nyare auktoriseringssystem som kallas för rollbaserad åtkomstkontroll (RBAC) som ger detaljerad åtkomsthantering för Azure-resurser. Mer information finns i [Administratörsroller för klassiska prenumerationer, Azure RBAC-roller och administratörsroller för Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Global Azure AD-administratör|Den här administratörsrollen tilldelas automatiskt till den som har skapat Azure AD-klienten. Globala administratörer kan utföra alla administrativa funktioner för Azure AD och alla tjänster som federera till Azure AD som Exchange Online, SharePoint Online och Skype för företag Online. Du kan flera globala administratörer men bara globala administratörer kan tilldela administratörsroller (inklusive tilldela andra globala administratörer) till användare.<br><br>**Obs!**<br>Administratörsrollen kallas för Global administratör i Azure-portalen men kallas för **Företagsadministratör** i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell.<br><br>Mer information om olika administratörsroller finns i artikeln om [behörigheter för administratörsrollen i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Microsoft-konto (kallas även MSA)|Personliga konton som ger åtkomst till dina kundorienterade Microsoft-produkter och -molntjänster som Outlook, OneDrive, Xbox LIVE eller Office 365. Ditt Microsoft-konto skapas och lagras i Microsofts kontosystem för konsumentidentiteter som drivs av Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Vilka funktioner fungerar i Azure AD?

När du har valt Azure AD-licens, får du åtkomst till vissa eller alla av följande funktioner för din organisation:

|Category|Beskrivning|
|-------|-----------|
|Programhantering|Hantera dina molnbaserade och lokala appar med programproxy, enkel inloggning Mina appar-portalen (kallas även åtkomstpanelen) och Software as a Service-appar (SaaS). Mer information finns i [Ge säker fjärråtkomst till lokala program](../manage-apps/application-proxy.md) och i [dokumentationen om programhantering](../manage-apps/index.yml).|
|Autentisering|Hantera Azure Active Directory-lösenordsåterställning via självbetjäning, Multi-Factor Authentication, listor över anpassade förbjudna lösenord och smart utlåsning. Mer information finns i [dokumentationen om Azure AD-autentisering](../authentication/index.yml).|
|Business-to-Business (B2B)|Hantera dina gästanvändare och externa partners, och behåll kontrollen över egna företagsdata. Mer information finns i [Azure Active Directory B2B-dokumentationen](../b2b/index.yml).|
|Business-to-Customer (B2C)|Anpassa och kontrollera hur användare registrerar sig, loggar in och hanterar sina profiler när de använder dina appar. Mer information finns i [Azure Active Directory B2C-dokumentationen](../../active-directory-b2c/index.yml).|
|Villkorlig åtkomst|Hantera åtkomst till molnappar. Mer information finns i [dokumentationen om villkorsstyrd åtkomst i Azure AD](../conditional-access/index.yml).|
|Azure Active Directory för utvecklare|Skapa appar som loggar in alla Microsoft-identiteter, hämta token för att anropa Microsoft Graph, andra Microsoft-API:er eller anpassade API:er. Mer information finns i [Microsoft Identity Platform (Azure Active Directory för utvecklare)](../develop/index.yml).|
|Enhetshantering|Hantera hur dina molnbaserade eller lokala enheter får åtkomst till företagets data. Mer information finns i [dokumentationen om enhetshantering i Azure AD](../devices/index.yml).|
|Domain Services|Anslut virtuella Azure-datorer till en domän utan att använda domänkontrollanter. Mer information finns i [Azure AD Domain Services-dokumentationen](../../active-directory-domain-services/index.yml).|
|Företagsanvändare|Hantera licenstilldelningen, åtkomsten till appar och konfigurera ombud med hjälp av grupper och administratörsroller. Mer information finns i [dokumentationen om användarhantering i Azure Active Directory](../users-groups-roles/index.yml).|
|Hybrididentitet|Använd Azure Active Directory Connect och Connect Health för att tillhandahålla en enda användaridentitet för autentisering och autentisering för alla resurser, oavsett plats (moln eller lokalt). Mer information finns i [dokumentationen om hybrididentiter](../hybrid/index.yml).|
|Identitetsstyrning|Hantera organisationens identitet via åtkomstkontroller för medarbetare, affärspartners, leverantörer, tjänster och appar. Du kan även utföra åtkomstgranskningar. Mer information finns i [dokumentationen om identitetsstyrning i Azure AD](../governance/identity-governance-overview.md) och [Azure AD-åtkomstgranskningar](../governance/access-reviews-overview.md).|
|Identitetsskydd|Identifiera potentiella sårbarheter som påverkar organisationens identiteter, konfigurera principer för att svara på misstänkta handlingar och sedan vidta lämpliga åtgärder för att lösa dem. Mer information finns i [Azure AD Identity Protection](../identity-protection/index.yml).|
|Hanterade identiteter för Azure-resurser|Ger dina Azure-tjänster en automatiskt hanterad identitet i Azure AD som kan autentisera alla autentiseringstjänster med stöd för Azure AD, till exempel Key Vault. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|Hantera, kontrollera och övervaka åtkomst i organisationen. Den här funktionen ger åtkomst till resurser i Azure AD och Azure och andra Microsoft Online Services, som Office 365 eller Intune. Mer information finns i [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Rapporter och övervakning|Få insikter om säkerhet och användningsmönster i din miljö. Mer information finns i [Azure Active Directory-rapporter och -övervakning](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Nästa steg

- [Registrera dig för Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associera en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Komma åt Azure Active Directory och skapa en ny klientorganisation](active-directory-access-create-new-tenant.md)

- [Checklista för distribution av funktioner i Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)
