---
title: Logga in på Azure Notebooks för hands version
description: Konfigurera ditt konto för Azure-anteckningsböcker med ett Microsoft-konto eller ett arbete/skola-konto.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646304"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Ditt användar konto för Azure Notebooks för hands version

Du kan använda Azure-anteckningsböcker med eller utan att logga in med ett användarkonto:

- Utan att logga in, kan du skapa och kör anteckningsböcker, men det går inte att behålla anteckningsböcker eller data som en del av projekt. Användare som tar emot en länk till en Azure-anteckningsbok kan till exempel få anteckningsboken utan att behöva logga in.
- När du har loggat in, behåller Azure anteckningsböcker alla dina projekt med ditt konto. Inloggade användare också ha ett användar-ID som låter dem dela sina projekt och anteckningsböcker med andra.
- Om kontot som används för Azure-datorer är även associerat med en Azure-prenumeration, får du ytterligare fördelar, till exempel köra anteckningsböcker på mer kraftfulla servrar, skapa privata anteckningsböcker och ge behörigheter till anteckningsböcker för enskilda användare.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Logga in på Azure-datorer kräver antingen ett Account eller ett ”arbete eller skola”-konto. Du uppmanas för ditt konto när du väljer den **logga In** på det övre högra hörnet av sidan för bärbara datorer:

![Logga in kommandot för Azure-anteckningsböcker](media/accounts/sign-in-command.png)

Allt arbete som du gör i Azure-datorer är associerad med det konto som används för att logga in. Varje konto måste också ha ett unikt användar-ID din [användarprofil](azure-notebooks-user-profile.md). Därför kan du kan logga in på Azure-anteckningsböcker med olika konton om du behöver underhålla separata uppsättningar av projekt och separata identiteter. Alla medlemmar i en datavetenskapliga team kan till exempel ha både enskilda konton tillsammans med som en delad grupp-konto som de använder för att presentera sitt arbete till personer utanför företaget. Lärare, på samma sätt kan ha ett konto för sina undervisning roll som skiljer sig från ett konto som används i externa hjälp eller arbete med öppen källkod.

## <a name="microsoft-accounts"></a>Microsoft-konton

Microsoft-konton används för att logga in på flera Microsoft-produkter och tjänster, till exempel Windows, Azure, outlook.com, OneDrive och XBox Live. Om du använder någon av dessa tjänster är det troligt att du redan har ett Account som du kan använda med Azure-datorer.

Om du är osäker, väljer du den **skapa en** i konto-prompten. Du kan skapa ett nytt microsoftkonto med valfri e-postadress från valfri provider.

![Kommando för att skapa ett nytt microsoftkonto](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto som är associerat med det kan du se meddelandet "du kan inte registrera dig här med en e-postadress till arbetet eller skolan. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande. " I så fall kan du försöka logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

För barnkonton blockeras åtkomst till Azure-anteckningsböcker som standard. Logga in med ett barnkonto visar felet visas nedan:

![Fel vid försök att logga in med ett barnkonto: Det uppstod ett fel, din förälder har blockerat åtkomsten](media/accounts/child-account-error.png)

Om du vill aktivera åtkomst, måste en överordnad gör följande:

1. Besök `https://account.live.com/mk` och logga in med ett överordnat konto.
1. I avsnittet för barn i fråga, Välj **hantera det här barnets åtkomst till appar från tredje part**.
1. På nästa sida väljer **Aktivera åtkomst**.
1. När den underordnade bredvid används för att logga in på Azure-datorer, väljer **Ja** i meddelandet för behörigheter som visas.

> [!Warning]
> Aktivera åtkomst till appar från tredje part för Azure-datorer kan också åtkomst för alla andra tredjepartsappar. Föräldrar rekommenderas var försiktig när du aktiverar åtkomst till och kanske vill övervaka deras underordnade närmare.

## <a name="work-or-school-accounts"></a>Arbets- eller skolkonton

Ett arbets- eller skolkonto konto har skapats av en organisations administratör för att ge en medlem i organisationen åtkomst till Microsofts molntjänster som Office 365 och även som ett konto för att logga in på Windows på en domänansluten dator. Ett arbets- eller skolkonto konto vanligtvis använder en organisations e-postadress, till exempel any-user@contoso.com.

Loggar in på Azure-anteckningsböcker med ett arbets- eller skolkonto konto kan du kräva administratörens godkännande eftersom Azure anteckningsböcker samlar in eller använder (men inte röjer) information, till exempel kontots e-postadress och användarens webbläsare. (Webbläsardata används för att optimera funktioner efter populära användning.)

Administratör för ett organisationskonto måste ge medgivande åt användare om användare är begränsade från medgivandedialogen individuellt. I det här fallet ser användare meddelandet ”du inte kan komma åt det här programmet”:

![”Du kan inte komma åt det här programmet” meddelande när du använder ett arbets- eller skolkonto konto](media/accounts/consent-permissions-denied.png)

För att ge ditt medgivande som administratör måste använda den [administratör samtyckessida](https://notebooks.azure.com/account/adminConsent), som går igenom processen.

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Redigera din profil och användar-ID](azure-notebooks-user-profile.md)
