---
title: Vad är en princip för migrering i Azure Active Directory villkorlig åtkomst? | Microsoft Docs
description: Lär dig vad du behöver veta för att migrera klassiska principer i Azure-portalen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7464546a78e1b54cdea3bd6dd66656f5b189bc02
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506815"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Vad är en princip för migrering i Azure Active Directory villkorlig åtkomst? 

[Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active directory (Azure AD) som ger dig möjlighet att styra hur behöriga användare har åtkomst till dina appar i molnet. Syftet är fortfarande samma, har lanseringen av den nya Azure-portalen infört avsevärda förbättringar för hur villkorlig åtkomst fungerar.

Överväg att migrera de principer som du inte har skapat i Azure-portalen eftersom:

- Du kan nu hantera scenarier som du inte kan hantera innan.
- Du kan minska antalet principer som du behöver hantera genom att konsolidera dem.   
- Du kan hantera alla principer för villkorlig åtkomst på en central plats.
- Den klassiska Azure-portalen kommer att dras tillbaka.   

Den här artikeln förklarar vad du behöver veta för att migrera de befintliga principerna för villkorlig åtkomst till det nya ramverket.
 
## <a name="classic-policies"></a>Klassiska principer

I den [Azure-portalen](https://portal.azure.com), [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) är din startpunkt för principer för villkorlig åtkomst. Men i din miljö kan du också principer för villkorlig åtkomst som du inte har skapats med den här sidan. Dessa principer kallas *klassiska principer*. Principer för klassiska principer för villkorlig åtkomst, du har skapat i:

- Den klassiska Azure-portalen
- Klassiska Intune-portalen
- Intune App Protection-portalen

På den **villkorlig åtkomst** kan du kan komma åt din klassiska principer genom att klicka på [ **klassiska principer (förhandsversion)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) i den **hantera** avsnittet. 

![Azure Active Directory](./media/policy-migration/71.png)

Den **klassiska principer** vyn ger dig möjlighet att:

- Filtrera din klassiska principer.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Inaktivera klassiska principer.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Granska inställningarna på den klassiska principen (och att inaktivera den).

   ![Azure Active Directory](./media/policy-migration/74.png)

Om du har inaktiverat den klassiska principen, kan inte du ångra det här steget längre. Det här är anledningen till att du kan ändra gruppmedlemskap i en klassiska principen med hjälp av den **information** vy. 

![Azure Active Directory](./media/policy-migration/75.png)

Genom att antingen ändra de valda grupperna eller genom att exkludera specifika grupper, kan du testa effekten av en inaktiverad klassiska principen för några testanvändare innan du inaktiverar principen för alla inkluderade användare och grupper. 

## <a name="azure-ad-conditional-access-policies"></a>Principer för Azure AD villkorlig åtkomst

Du kan hantera alla dina principer på en central plats med villkorlig åtkomst i Azure-portalen. Eftersom implementeringen av hur villkorlig åtkomst har ändrats, bör du bekanta dig med de grundläggande begreppen innan du migrerar din klassiska principer.

Se:

- [Vad är villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md) vill veta mer om grundläggande koncept och terminologi.
- [Bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md) att få vägledning om att distribuera villkorlig åtkomst i din organisation.
- [Kräva MFA för specifika appar med Azure Active Directory villkorsstyrd åtkomst](app-based-mfa.md) att bekanta dig med användargränssnittet i Azure-portalen.
 
## <a name="migration-considerations"></a>Överväganden vid migrering

I den här artikeln, principer för Azure AD villkorlig åtkomst är kallas även *nya principer*.
Din klassiska principer fortsätta att fungera sida vid sida med din nya principer tills du inaktivera eller ta bort dem. 

Följande aspekter är viktiga i samband med en princip för konsolidering:

- Medan klassiska principer är knutna till en specifik molnapp, kan du välja så många appar i molnet som du behöver i en ny princip.
- Kontroller av den klassiska principen och en ny princip för en molnapp kräver alla kontroller (*och*) uppfylls. 
- I en ny princip kan du:
   - Kombinera flera villkor om det krävs av ditt scenario. 
   - Välj flera bevilja krav som åtkomst kontroll och kombinera dem med ett logiskt *eller* (kräver en av de valda kontrollerna) eller med en logisk *och* (kräver alla de valda kontrollerna).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Om du vill migrera klassiska principer för **Office 365 Exchange online** som omfattar **Exchange Active Sync** klient apps villkor är kanske det inte att konsolidera dem till en ny princip. 

Detta gäller, till exempel om du vill kunna använda alla typer av klienten appar. I en ny princip som har **Exchange Active Sync** klient apps villkor är du inte välja andra klientappar.

![Azure Active Directory](./media/policy-migration/64.png)

En konsolidering till en ny princip är inte möjligt om din klassiska principer innehåller flera villkor. En ny princip som har **Exchange Active Sync** klientappar villkor som konfigurerats stöder inte andra villkor:   

![Azure Active Directory](./media/policy-migration/08.png)

Om du har en ny princip som har **Exchange Active Sync** som klientappar villkor som konfigurerats som du behöver att se till att alla övriga tillstånd som inte har konfigurerats. 

![Azure Active Directory](./media/policy-migration/16.png)
 
[Appbaserad](technical-reference.md#approved-client-app-requirement) klassiska principer för Office 365 Exchange Online som innehåller **Exchange Active Sync** som klient apps villkor Tillåt **stöds** och **stöds inte** [enhetsplattformar](technical-reference.md#device-platform-condition). Medan du inte kan konfigurera enskilda enhetsplattformar i en ny princip för relaterade, kan du begränsa stödet till [enhetsplattformar som stöds](technical-reference.md#device-platform-condition) endast. 

![Azure Active Directory](./media/policy-migration/65.png)

Du kan konsolidera flera klassiska principer som innehåller **Exchange Active Sync** som klient apps villkor om de har:

- Endast **Exchange Active Sync** som villkor 
- Flera krav för att bevilja åtkomst är konfigurerat

Ett vanligt scenario är sammanställning av:

- En enhetsbaserad klassiska principen från den klassiska Azure-portalen 
- En app-baserade klassiska principen i Intune app protection-portalen 
 
I det här fallet kan du konsolidera din klassiska principer till en ny princip som har båda krav som valts.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Enhetsplattformar

Klassiska principer med [appbaserad kontroller](technical-reference.md#approved-client-app-requirement) är förkonfigurerad med iOS och Android som den [enheten plattform villkor](technical-reference.md#device-platform-condition). 

I en ny princip, du måste välja den [enhetsplattformar](technical-reference.md#device-platform-condition) du vill stödja individuellt.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med Azure Active Directory villkorsstyrd åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa den [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
