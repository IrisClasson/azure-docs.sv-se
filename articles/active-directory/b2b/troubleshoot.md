---
title: Felsökning av B2B - samarbete i Azure Active Directory | Microsoft Docs
description: Friskrivning från ansvar för vanliga problem med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185d29ff1770ed9549b4b63a2e5da579bcf054f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767161"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Felsökning av Azure Active Directory B2B-samarbete

Här finns några lösningar för vanliga problem med Azure Active Directory (Azure AD) B2B-samarbete.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Jag har lagt till en extern användare men ser inte dem i den globala adressboken eller i Personväljaren

Objektet kan ta några minuter att replikera i fall där externa användare inte fylls i listan.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>En B2B-gästanvändare inte visas i SharePoint Online/OneDrive Personväljaren

Möjligheten att söka efter befintliga gästanvändare i Personväljaren SharePoint Online (SPO) har värdet OFF som standard så att de matchar äldre beteende.

Du kan aktivera den här funktionen med hjälp av inställningen ”ShowPeoplePickerSuggestionsForGuestUsers” på samlingsnivå klient- och plats. Du kan ange funktionen med hjälp av Set-SPOTenant och Set-SPOSite cmdletar som ger möjlighet att söka efter alla befintliga gästanvändare i katalogen. Ändringar i klientorganisationsområdet påverkar inte redan etablerade SPO-webbplatser.

## <a name="invitations-have-been-disabled-for-directory"></a>Inbjudningar har inaktiverats för katalog

Om du får ett meddelande om att du inte har behörighet att bjuda in användare, verifiera att ditt användarkonto har behörighet att bjuda in extern användare under användarinställningar:

![Skärmbild som visar inställningar för externa användare](media/troubleshoot/external-user-settings.png)

Om du nyligen har ändrat dessa inställningar eller tilldelat rollen Gästinbjudare till en användare kan det finnas en fördröjning på 15 – 60 minuter innan ändringarna träder i kraft.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Användaren som jag får ett fel under inlösning

Vanliga fel är:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>När en användare administratör inte tillåter EmailVerified användare skapas i deras klienter

När du bjuder in användare vars organisation använder Azure Active Directory, men där användarens konto inte finns (till exempel användaren finns inte i Azure AD-contoso.com). Administratören för contoso.com kan ha en princip på plats som förhindrar att användare håller på att skapas. Du måste kontrollera med administratören för att bestämma om externa användare. Den externa användaren administratör kan behöva tillåta e-verifierad användare inom sin domän (finns i den här [artikeln](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) på e-verifierat användarna).

![Felmeddelande om klienten inte att e-postverifierade användare](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Extern användare finns inte redan i en federerad domän

Om du använder federation-autentisering och användaren inte redan finns i Azure Active Directory, kan användaren inte bjudas in.

Den externa användaren administratören måste synkronisera användarens konto till Azure Active Directory för att lösa problemet.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hur gör\#”, vilket är vanligtvis inte ett giltigt tecken synkronisering med Azure AD?

”\#” är ett reserverat tecken i UPN-namn för Azure AD B2B-samarbete eller externa användare, eftersom det inbjudna kontot user@contoso.com blir user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Därför \# i UPN-namn som kommer från en lokal tillåts inte att logga in på Azure-portalen. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Jag får ett felmeddelande när du lägger till externa användare till en synkroniserad grupp

Externa användare kan läggas endast till ”tilldelats” eller ”säkerhetsgrupper” och inte till grupper som är hanterad lokalt.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Min extern användare tog inte emot ett e-postmeddelande för att lösa in

När en användare bör kontrollera med sina ISP: N eller skräppost filtret så att du får följande adress: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Jag har sett att det anpassade meddelandet inte får ingår i inbjudan meddelanden ibland

För att uppfylla sekretesslagar våra API: er inte inkluderar anpassade meddelanden i din e-postinbjudan när:

- Avsändaren har inte en e-postadress i bjuder in klienten
- När ett huvudnamn för appservice skickar inbjudan

Om det här scenariot är viktiga för dig kan du utelämna e-postinbjudan vårt API och skicka den via den e-postmekanism. Kontakta din organisations är jurist att kontrollera att alla e-postmeddelanden som du skickar detta sätt uppfyller gällande sekretesslagstiftning.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Du får felmeddelandet ”AADSTS65005” när du försöker logga in på en Azure-resurs

En användare med ett gästkonto kan inte logga in och får följande felmeddelande visas:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Användaren har en Azure-konto och är en av viral klientorganisation som har avbrutit eller ohanterade. Dessutom finns inte global eller det företag administratörer i klienten.

För att lösa problemet måste du ta över övergivna klienten. Referera till [ta över en ohanterad katalog som administratör i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Du måste också komma åt mot internet-DNS för domänsuffix i fråga för att tillhandahålla direkt bevis på att du har kontroll över namnområdet. När klienten har returnerats till ett hanterat tillstånd diskutera med kunden om lämnar användarna och verifierat domännamn är det bästa alternativet för organisationen.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>En gästanvändare med en just-in-time- eller ”viral”-klienten är inte kan återställa sina lösenord

Om klienten identitet är en just-in-time (JIT) eller av viral klientorganisation kan (dvs. det är en separat, ohanterad Azure-klient), bara gästanvändaren återställa sina lösenord. Ibland en organisation kommer [tar över hanteringen av viral klienter](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) som skapas när anställda använda sina e-postadresser för att registrera dig för tjänster. När organisationen tar över en av viral klientorganisation kan kan bara en administratör i organisationen återställa användarens lösenord eller aktivera SSPR. Om det behövs, som organisationen som bjuder in kan du ta bort användarkontot från din katalog och skicka om inbjudan.

## <a name="next-steps"></a>Nästa steg

[Få support för B2B-samarbete](get-support.md)
