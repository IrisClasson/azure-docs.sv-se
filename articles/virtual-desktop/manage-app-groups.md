---
title: Hantera app-grupper för Windows Virtual Desktop Preview – Azure
description: Beskriver hur du ställer in förhandsversion för virtuella skrivbord i Windows-klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: cba13012bf165a097bd1382da8ef9897b0584d28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066881"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Självstudier: Hantera app-grupper för Windows Virtual Desktop-förhandsversion

Standardgrupp för app som skapats för en ny pool för förhandsversion för virtuella skrivbord i Windows-värd publicerar även hela skrivbordet. Dessutom kan du skapa en eller flera RemoteApp programgrupper för värd-poolen. Den här kursen för att skapa en grupp för RemoteApp-app och publicera enskilda appar för Start-menyn.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApps.

Innan du börjar [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) virtuellt skrivbord i Windows-modul ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

1. Kör följande PowerShell-cmdlet för att skapa en ny tom RemoteApp appgrupp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Valfritt) Du kan köra följande cmdlet om du vill se en lista över alla app-grupper för värden poolen för att verifiera app-gruppen har skapats.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Kör följande cmdlet för att få en lista över start menyn appar för poolen värd avbildning av virtuell dator. Anteckna värdena för **FilePath**, **IconPath**, **IconIndex**, och annan viktig information för programmet som du vill publicera.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Kör följande cmdlet för att installera appen baserat på dess appalias. appalias blir synliga när du kör utdata från steg 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Valfritt) Kör följande cmdlet för att publicera en ny RemoteApp i programgruppen som skapades i steg 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Kontrollera att appen har publicerats med följande cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Upprepa steg 1 – 5 för varje program som du önskar att publicera för den här app-gruppen.
8. Kör följande cmdlet för att ge användarna åtkomst till RemoteApps i app-gruppen.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa app-grupper, fyller den med RemoteApps och tilldela användare till app-gruppen. Läs så här skapar du en värd för verifiering i poolen, vilket kan du övervaka uppdateringar av tjänsten innan du distribuerar dem till produktionsmiljön, skapa en värd-pool för att verifiera självstudie för service-uppdateringar.

> [!div class="nextstepaction"]
> [Skapa en pool med värden för att verifiera självstudie för service-uppdateringar](./create-validation-host-pool.md)
