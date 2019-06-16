---
title: Hantera den lokala administratörsgruppen på Azure AD-anslutna enheter | Microsoft Docs
description: Lär dig hur du tilldelar Azure-roller till den lokala administratörsgruppen för en Windows-enhet.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: joflore
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f19a9dfe683ab1c58d373cb8ba88b6523d43623e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110738"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Hantera den lokala administratörsgruppen på Azure AD-anslutna enheter

Du måste vara medlem i gruppen lokala administratörer för att hantera en Windows-enhet. Som en del av processen i Azure Active Directory (AD Azure) uppdaterar Azure AD medlemskap i den här gruppen på en enhet. Du kan anpassa medlemskap uppdateringen för att uppfylla dina affärsbehov. Uppdateringen av gruppmedlemskap är till exempel användbart om du vill aktivera supportpersonalen att utföra uppgifter som kräver administratörsbehörighet på en enhet.

Den här artikeln förklarar hur medlemskap uppdateringen fungerar och hur du kan anpassa den under en Azure AD Join. Innehållet i den här artikeln gäller inte för en **hybrid** Azure AD-anslutning.


## <a name="how-it-works"></a>Hur det fungerar

När du ansluter en Windows-enhet med Azure AD med en Azure AD-anslutning, lägger Azure AD följande säkerhetsprinciper till den lokala administratörsgruppen på enheten:

- Den globala administratörsrollen för Azure AD
- Administratörsrollen för Azure AD-enhet 
- Användaren som utför Azure AD-anslutning   

Genom att lägga till Azure AD-roller till den lokala administratörsgruppen, kan du uppdatera de användare som kan hantera en enhet när som helst i Azure AD utan att ändra något på enheten. För närvarande kan tilldela du inte en administratörsroll.
Azure AD lägger också till administratörsrollen för Azure AD-enheten till den lokala administratörsgruppen för principen om lägsta behörighet (PoLP). Utöver de globala administratörerna kan du också aktivera användare som har *endast* tilldelade enhetsadministratörens roll att hantera en enhet. 


## <a name="manage-the-global-administrators-role"></a>Hantera den globala administratörsrollen

Om du vill visa och uppdatera medlemskap i rollen som global administratör, se:

- [Visa alla medlemmar i en administratörsroll i Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)

- [Tilldela en användare till administratörsroller i Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Hantera enhetsadministratörens roll 

I Azure-portalen kan du hantera enhetsadministratörens roll på den **enheter** sidan. Öppna den **enheter** sidan:

1. Logga in på din [Azure-portalen](https://portal.azure.com) som global administratör eller enhetsadministratör för.
2. I det vänstra navigeringsfältet, klickar du på **Azure Active Directory**. 
3. I den **hantera** klickar du på **enheter**.
4. På den **enheter** klickar du på **Enhetsinställningar**.

Om du vill ändra enhetsadministratörens roll, konfigurera **ytterligare lokala administratörer på Azure AD-anslutna enheter**.  

![Ytterligare lokala administratörer](./media/assign-local-admin/10.png)

>[!NOTE]
> Det här alternativet kräver en Azure AD Premium-klient. 


Enhetsadministratörer har tilldelats till alla Azure AD-anslutna enheter. Du kan inte begränsa enhetsadministratörer till en specifik uppsättning enheter. Uppdatera enhetsadministratörens roll ha inte nödvändigtvis en omedelbar inverkan på de berörda användarna. För enheterna en användare redan är inloggad på, privilegier uppdateringen sker:
     

- När en användare loggar.
- Efter 4 timmar när en ny utfärdas primär uppdatera Token. 




## <a name="manage-regular-users"></a>Hantera vanliga användare

Som standard Azure AD lägger du till användaren som utför Azure AD-anslutning i administratörsgruppen på enheten. Om du vill förhindra att vanliga användare blir lokala administratörer har följande alternativ:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) -Windows Autopilot tillhandahåller ett alternativ för att förhindra att den primära användaren som utför kopplingen blir lokal administratör. Du kan göra detta genom att [skapar en Autopilot-profilen](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
 
- [Massregistrering](https://docs.microsoft.com/intune/windows-bulk-enroll) – en Azure AD-koppling som utförs i kontexten för en massregistrering sker i samband med en automatiskt skapade användare. Användare som loggar in när en enhet har varit ansluten läggs inte till i administratörsgruppen.   



## <a name="manually-elevate-a-user-on-a-device"></a>Höj manuellt för en användare på en enhet 

Förutom att använda Azure AD join-processen, kan du också manuellt höja en vanlig användare om att bli en lokal administratör på en specifik enhet. Det här steget kräver att du redan är medlem i gruppen lokala administratörer. 

Från och med den **Windows 10 1709** versionen kan du utföra den här uppgiften från **Inställningar -> konton -> andra användare**. Välj **lägga till en arbets- eller skolkonto användare**, ange användarens UPN under **användarkonto** och välj *administratör* under **kontotyp**  
 
Dessutom kan du lägga till användare med hjälp av Kommandotolken:

- Om dina klientanvändare synkroniseras från den lokala Active Directory måste du använda `net localgroup administrators /add "Contoso\username"`.

- Om dina klientanvändare skapas i Azure AD, använda `net localgroup administrators /add "AzureAD\UserUpn"`


## <a name="considerations"></a>Överväganden 

Du inte kan tilldela grupper till enhetsadministratörens roll, tillåts bara enskilda användare.

Enhetsadministratörer har tilldelats till alla enheter i Azure AD har anslutits. De kan inte begränsas till en specifik uppsättning enheter.

När du tar bort användare från administratörsrollen enhet ha de fortfarande lokal administratörsbehörighet på en enhet så länge som de har loggat in till den. Behörigheten har återkallats under nästa inloggningen eller efter 4 timmar när en ny primär uppdateringstoken utfärdas.



## <a name="next-steps"></a>Nästa steg

- Om du vill ha en översikt över hantering av enheter i Azure-portalen kan du läsa om att [hantera enheter med Azure-portalen](device-management-azure-portal.md)

- Läs mer om enhetsbaserad villkorlig åtkomst i [konfigurera principer för Azure Active Directory enhetsbaserad villkorlig åtkomst](../conditional-access/require-managed-devices.md).


