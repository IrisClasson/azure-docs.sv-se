---
title: Vad är gruppbaserad licensiering i Azure Active Directory? | Microsoft Docs
description: Läs mer om Azure Active Directory gruppbaserad licensiering, inklusive hur det fungerar och bästa praxis.
services: active-directory
keywords: Azure AD-licensiering
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3f23b28c1b20155e50fddf17db90cd2a53c04855
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209827"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Vad är gruppbaserad licensiering i Azure Active Directory?

Microsoft-betaltjänster i molnet, till exempel Office 365, Enterprise Mobility + Security, Dynamics 365 och andra liknande produkter kräver licenser. Dessa licenser tilldelas till varje användare som behöver åtkomst till dessa tjänster. För att hantera licenser använder administratörer någon av hanteringsportalerna (Office eller Azure) och PowerShell-cmdlets. Azure Active Directory (Azure AD) är den underliggande infrastrukturen som har stöd för identitetshantering för alla Microsofts molntjänster. Azure AD lagrar information om användarnas licenstilldelningsstatus.

Hittills har det endast gått att tilldela licenser på nivån för enskilda användare, vilket kan försvåra storskalig hantering. För att till exempel lägga till eller ta bort licenser baserat på organisationsförändringar, till exempel om användare börjar eller slutar på företaget eller på en avdelning, måste administratören ofta skriva ett komplext PowerShell-skript. Det här skriptet gör separata anrop till molntjänsten.

För att underlätta för administratörer erbjuder Azure AD nu gruppbaserad licensiering. Du kan tilldela en eller flera produktlicenser till en grupp. Azure AD ser till att licenserna tilldelas till alla medlemmar i gruppen. Nya medlemmar som ansluter till gruppen tilldelas korrekta licenser. När de lämnar gruppen tas dessa licenser bort. Detta innebär att du inte behöver automatisera licenshanteringen via PowerShell för att återspegla ändringar i organisations- och avdelningsstrukturen baserat på enskilda användare.


## <a name="features"></a>Funktioner

Här är de viktigaste funktionerna i gruppbaserad licensiering:

- Licenser kan tilldelas till valfri säkerhetsgrupp i Azure AD. Säkerhetsgrupper kan synkroniseras från en lokal plats, med hjälp av Azure AD Connect. Du kan också skapa säkerhetsgrupper direkt i Azure AD (även kallade molnspecifika grupper) eller automatiskt via funktionen för dynamiska grupper i Azure AD.

- När en produktlicens har tilldelats en grupp kan administratören inaktivera en eller flera tjänstplaner i produkten. Detta görs vanligtvis när organisationen inte är helt redo att börja använda en tjänst som ingår i en produkt. Administratören kan till exempel tilldela Office 365 till en avdelning, men tillfälligt inaktivera tjänsten Yammer.

- Alla Microsoft-molntjänster som kräver licensiering på användarnivå stöds. Detta inkluderar alla Office 365-produkter, Enterprise Mobility + Security och Dynamics 365.

- Gruppbaserad licensiering är för närvarande bara tillgängligt via [Azure Portal](https://portal.azure.com). Om du främst använder andra hanteringsportaler för användar- och grupphantering, till exempel Office 365-portalen kan du fortsätta att göra det. Men du bör använda Azure Portal för att hantera licenser på gruppnivå.

- Azure AD hanterar automatiskt licensändringar som uppstår vid ändringar av gruppmedlemskap. Licensändringarna börjar normalt att tillämpas inom några minuter efter en ändring av gruppmedlemskapet.

- En användare kan vara medlem i flera grupper med definierade licensprinciper. En användare kan också ha vissa licenser som tilldelats direkt, utanför grupper. Det slutliga användartillståndet är en kombination av alla tilldelade produkt- och tjänstlicenser. Om en användare har tilldelats samma från flera källor, förbrukas licensen bara en gång.

- I vissa fall kan licenser inte tilldelas till en användare. Exempelvis kanske det inte finns tillräckligt med tillgängliga licenser i klientorganisationen, eller så kanske tjänster som står i konflikt har tilldelats på samma gång. Administratörer har åtkomst till information om användare för vilka Azure AD inte helt kan bearbeta grupplicenser. De kan sedan vidta åtgärder baserat på den informationen.

- Det krävs en betald eller utvärderingslicens prenumeration för Azure AD Basic eller en betald eller utvärderingslicens Office 365 Enterprise E3, Office 365 A3 och senare versioner i klienten att använda licenshantering av gruppbaserad. Den här funktionen kräver en licens för varje unika användare som är medlem i grupper som har tilldelats licens. Du behöver inte tilldela licenser till användare för dem att vara medlemmar i grupper som har tilldelats licens, men du måste ha det minsta antalet licenser i klientorganisationen för att täcka alla användare. Till exempel om du hade en totalsumma på 1 000 unika användare i alla grupper med licenser i din klient behöver du minst 1 000 licenser för att uppfylla kravet på licens.

## <a name="your-feedback-is-welcome"></a>Vi vill gärna ha din feedback!

Lämna gärna feedback eller önskemål om funktioner i [forumet för Azure AD-administratörer](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via gruppbaserad licensiering finns här:

* [Tilldela licenser till en grupp i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
