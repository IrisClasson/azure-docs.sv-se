---
title: Administration i Azure EA-portalen för partner
description: Beskriver portaladministrationsämnen som gäller partner
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: banders
ms.openlocfilehash: 1fb49f3a58235ac196d7fb619b10517cb54908c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039539"
---
# <a name="azure-ea-portal-administration-for-partners"></a>Administration i Azure EA-portalen för partner

I den här artikeln förklaras några vanliga uppgifter som en EA-administratör hos en partner utför i Azure EA-portalen (https://ea.azure.com).

## <a name="manage-partner-administrators"></a>Hantera partneradministratörer

Varje partneradministratör i Azure EA-portalen kan lägga till eller ta bort andra partneradministratörer. Partneradministratörer är associerade med partnerorganisationerna för indirekta registreringar och är inte direkt associerade med registreringarna.

### <a name="add-a-partner-administrator"></a>Lägga till en partneradministratör

Om du vill visa en lista över alla registreringar som är associerade till samma partnerorganisation som den aktuella användaren klickar du på fliken **Enrollment** (Registrering) och väljer en önskad registreringsruta.

1. Logga in som partneradministratör.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Partner**.
1. Välj **+ Lägg till administratör** och fyll i e-postadress, meddelandekontakt och meddelandeinformation.
1. Välj **Lägg till**.

### <a name="remove-a-partner-administrator"></a>Ta bort en partneradministratör

Om du vill visa en lista över alla registreringar som är associerade till samma partnerorganisation som den aktuella användaren väljer du fliken **Enrollment** (Registrering) och väljer en önskad registreringsruta.

1. Logga in som partneradministratör.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Partner**.
1. Under avsnittet Administrator (Administratör) väljer du lämplig rad för den administratör du vill ta bort.
1. Välj X-symbolen till höger.
1. Bekräfta att du vill ta bort.

## <a name="manage-partner-notifications"></a>Hantera partnermeddelanden

Partneradministratörer kan hantera hur ofta de ska få användningsmeddelanden för sina registreringar. De får automatiskt ett meddelande om utestående saldo varje vecka. De kan ändra hur ofta enskilda meddelanden ska skickas till varje månad, varje vecka, varje dag eller aldrig.

Om ett meddelande till en användare uteblir gör du så här för att kontrollera att användarens meddelandeinställningar är korrekta.

1. Logga in på Azure EA-portalen som partneradministratör.
2. Välj **Hantera** och sedan fliken **Partner**.
3. Granska listan med administratörer i avsnittet Administrator (Administratör).
4. Om du vill redigera meddelandeinställningarna hovrar du över administratören och väljer pennsymbolen.
5. Öka meddelandefrekvensen och livscykelmeddelandena efter behov.
6. Lägg till en kontakt om det behövs och välj **Lägg till**.
7. Välj **Spara**.

![Exempel som visar rutan Lägg till kontakt ](./media/ea-partner-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Visa registreringar för partneradministratörer

Partneradministratörer kan visa en lista över alla direkta och indirekta registreringar i Azure EA-portalen. Rutor som innehåller en översikt över alla registreringar visas med registreringsnummer, registreringens namn, saldo och överförbrukningsbelopp.

### <a name="view-a-list-of-enrollments"></a>Visa en lista över registreringar

1. Logga in som partneradministratör.
1. Välj **Hantera** i navigeringsfältet till vänster på sidan.
1. Välj fliken **Registrering**.
1. Markera rutan för registrering.

En vy över alla registreringar blir kvar högst upp på sidan, med rutor för varje registrering. Dessutom kan du navigera mellan registreringar genom att välja det aktuella registreringsnumret på navigeringsfältet till vänster på sidan. En utfällning visas där du kan söka efter registreringar eller välja en annan registrering genom att välja lämplig ruta.

## <a name="next-steps"></a>Nästa steg

- Grundläggande information om Azure EA-portalen finns i artikeln [Kom igång med Azure EA-portalen](ea-portal-get-started.md).
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](ea-portal-troubleshoot.md).