---
title: Skapa en åtkomstgranskning av grupper eller program – Azure Active Directory | Microsoft Docs
description: Lär dig hur du skapar en åtkomstgranskning för medlemmar i gruppen eller programmet åtkomst i Azure Active Directory-åtkomstgranskningar.
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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef72f1649c3f3e0af7fba53b2e8dbcee49d4b59
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734571"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning av grupper eller program i Azure AD-åtkomstgranskningar

Åtkomst till grupper och program för anställda och gäster ändras med tiden. För att minska riskerna med inaktuella åtkomsttilldelningar kan kan administratörer använda Azure Active Directory (AD Azure) för att skapa åtkomstgranskningar för gruppmedlemmar eller programåtkomst. Om du vill granska regelbundet tillgång kan skapa du också återkommande åtkomstgranskningar. Mer information om dessa scenarier finns i [hantera användaråtkomst](manage-user-access-with-access-reviews.md) och [hantera gäståtkomst](manage-guest-access-with-access-reviews.md).

Den här artikeln beskriver hur du skapar en eller flera åtkomstgranskningar för gruppmedlemmar eller programåtkomst.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Azure AD Premium P2
- Global administratör eller Användaradministratör

Mer information finns i [vilka användare måste ha licenser?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomstgranskningar

1. Logga in på Azure-portalen och öppna den [Identitetsstyrning sidan](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. I den vänstra menyn klickar du på **Åtkomstgranskningar**.

1. Klicka på **ny åtkomstgranskning** att skapa en ny åtkomstgranskning.

    ![Åtkomstgranskning - kontroller](./media/create-access-review/access-reviews.png)

1. Namnet åtkomstgranskningen. Du kan också ge granskningen en beskrivning. Namn och beskrivning visas granskarna.

    ![Skapa en åtkomstgranskning - granska namn och beskrivning](./media/create-access-review/name-description.png)

1. Ange den **startdatum**. Som standard en åtkomstgranskning sker en gång, startar samtidigt som den har skapats och slutar i en månad. Du kan ändra start- och slutdatum komma granska start i framtiden och senast hur många dagar som du vill.

    ![Skapa en åtkomstgranskning - Start- och slutdatum](./media/create-access-review/start-end-dates.png)

1. För att göra åtkomsten granska återkommande, ändra den **frekvens** från **en gång** till **veckovisa**, **månatliga**,  **Kvartalsvis** eller **årligen**. Använd den **varaktighet** skjutreglaget eller text om du vill definiera hur många dagar som varje granskning av serien med återkommande kommer att vara öppna för indata från granskare. Maximal varaktighet som du kan ange för den månatliga granskningen är till exempel 27 dagar att undvika överlappande granskningar.

1. Använd den **slutet** inställningen för att specificera hur du avslutar återkommande åtkomst granska serien. Serien kan sluta på tre sätt: det körs kontinuerligt för att starta granskningar på obestämd tid tills ett visst datum eller efter ett angivet antal förekomster har slutförts. Du administratören för en annan eller en annan Global administratör kan stoppa serien när du har skapat genom att ändra datumet i **inställningar**, så att den slutar på det datumet.

1. I den **användare** avsnittet, ange vilka användare som åtkomstgranskningen gäller för. Åtkomstgranskningar kan vara medlemmar i en grupp eller användare som har tilldelats till ett program. Du kan ytterligare begränsa åtkomsten endast granskning till granskning gästanvändare som är medlemmar (eller tilldelats programmet), i stället för att granska alla användare som är medlemmar eller som har åtkomst till programmet.

    ![Skapa en åtkomstgranskning - användare](./media/create-access-review/users.png)

1. I den **grupp** väljer du en eller flera grupper som du vill granska medlemskap.

    > [!NOTE]
    > Att välja mer än en grupp skapar flera åtkomstgranskningar. Till exempel skapar att välja grupper fem separata åtkomstgranskningar.
    
    ![Skapa en åtkomstgranskning - Välj grupp](./media/create-access-review/select-group.png)

1. I den **program** avsnittet (om du har valt **tilldelats till ett program** i steg 8), Välj de program som du vill granska åtkomst till.

    > [!NOTE]
    > Att välja mer än ett program skapar flera åtkomstgranskningar. Till exempel skapar att välja fem program fem separata åtkomstgranskningar.
    
    ![Skapa en åtkomstgranskning -: Välj program](./media/create-access-review/select-application.png)

1. I den **granskare** väljer du en eller flera personer att granska alla användare inom området. Eller du kan välja för att ha medlemmar granska sin egen åtkomst. Om resursen är en grupp, kan du be gruppägare att granska. Du kan också kräva att granskarna ska ange en anledning när de godkänner åtkomst.

    ![Skapa en åtkomstgranskning - granskare](./media/create-access-review/reviewers.png)

1. I den **program** väljer du det program som du vill använda. **Standardprogrammet** finns alltid.

    ![Skapa en åtkomstgranskning - program](./media/create-access-review/programs.png)

    Du kan förenkla spåra och samla in åtkomstgranskningar för olika ändamål genom att ordna dem i program. Varje åtkomstgranskning kan länkas till ett program. Sedan när du förbereder rapporter för en granskare kan du fokusera på åtkomstgranskningar inom omfånget för en viss initiativ. Program och åtkomstgranskningsresultaten är synliga för användare med Global administratör, Användaradministratör, säkerhetsadministratör eller säkerhetsläsarroll.

    Om du vill se en lista över program, gå till åtkomst går igenom sidan och väljer **program**. Om du arbetar i en Global administratör eller användarrollen administratör, kan du skapa ytterligare program. Du kan till exempel välja att ha ett program för varje efterlevnad eller företagsmål. Om du inte längre behöver ett program och den inte redan har alla kontroller som är länkad till den, kan du ta bort den.

### <a name="upon-completion-settings"></a>Vid slutförande-inställningar

1. Om du vill ange vad som händer när en granskning är klar, expandera den **vid slutförande-inställningar** avsnittet.

    ![Vid slutförande-inställningar](./media/create-access-review/upon-completion-settings.png)

1. Om du vill att automatiskt ta bort åtkomst för användare som nekades anger **tillämpa automatiskt resultaten på resursen** till **aktivera**. Om du vill tillämpa resultaten manuellt när granskningen har slutförts kan du ange växeln **inaktivera**.

1. Använd den **granskaren inte svarar** listan för att ange vad som händer för användare som inte har setts över av den inom granskningsperioden. Den här inställningen påverkar inte användare som har granskats av granskarna manuellt. Om den slutliga granskare beslut neka, bort användarens åtkomst.

    - **Ingen ändring** -lämna användarens åtkomst har inte ändrats
    - **Ta bort åtkomst** -ta bort användarens åtkomst
    - **Bevilja åtkomst** -godkänna användarens åtkomst
    - **Anta rekommendationerna** – ta systemets rekommendation neka eller godkänna användaren är fortsatt åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar, expandera den **avancerade inställningar** avsnittet.

    ![Avancerade inställningar](./media/create-access-review/advanced-settings.png)

1. Ange **visa rekommendationer** till **aktivera** för att visa granskarna systemet rekommendationer baserat information om användaren åtkomst.

1. Ange **Kräv orsak vid godkännande** till **aktivera** att kräva att granskaren anger skäl för godkännande.

1. Ange **e-postaviseringar** till **aktivera** till har Azure AD skickar e-postmeddelanden till granskare när en åtkomstgranskning startar och till administratörer när en granskning är klar.

1. Ange **påminnelser** till **aktivera** har Azure AD skickar påminnelser om åtkomstgranskningar pågår till granskare som inte har slutfört sina granskningar.

    Som standard skickar Azure AD automatiskt en påminnelse när halva tiden före slutdatumet har gått till granskarna som ännu inte har svarat.

## <a name="start-the-access-review"></a>Starta åtkomstgranskningen

När du har angett inställningarna för en åtkomstgranskning, klickar du på **starta**. Åtkomstgranskningen visas i listan med en indikator för dess status.

![Åtkomstgranskningar lista](./media/create-access-review/access-reviews-list.png)

Som standard skickar Azure AD ett e-postmeddelande till granskare strax efter det att granskningen startar. Om du väljer att inte har Azure AD skickar e-postmeddelandet, måste du meddela granskarna att en åtkomstgranskning väntar dem att slutföra. Visa instruktioner för hur du [granska åtkomst till grupper eller program](perform-access-review.md). Om din granskning är för gäster att granska sin egen åtkomst, visas instruktioner för hur du [granska åtkomst själv till grupper eller program](review-your-access.md).

Om du har tilldelat gäster som granskare och de inte har accepterat inbjudan, kommer de inte emot ett e-postmeddelande från åtkomstgranskningar eftersom de måste du acceptera inbjudan innan du granska.

## <a name="create-reviews-via-apis"></a>Skapa granskningar via API: er

Du kan också skapa åtkomstgranskningar med API: er. Vad du gör för att hantera åtkomst går igenom i grupper och användare i Azure-portalen kan också göras med hjälp av Microsoft Graph API: er. Mer information finns i den [API-referens för Azure AD-åtkomstgranskningar](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Finns ett kodexempel i [exempel för att hämta Azure AD-åtkomst går igenom via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nästa steg

- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Granska åtkomst själv till grupper eller program](review-your-access.md)
- [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md)
