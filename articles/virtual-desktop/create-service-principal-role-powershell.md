---
title: Skapa Windows Virtual Desktop förhandsversion tjänstens huvudnamn och rolltilldelningar med hjälp av PowerShell - Azure
description: Hur du skapar tjänstens huvudnamn och tilldela roller med hjälp av PowerShell i förhandsversion för virtuella skrivbord i Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 44c823653ecbad1c4dd1fd35b676c8a6d8bd1620
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206666"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Självstudier: Skapa tjänstens huvudnamn och rolltilldelningar med hjälp av PowerShell

Tjänstens huvudnamn är identiteter som du kan skapa i Azure Active Directory för att tilldela roller och behörigheter för ett visst syfte. I Windows Virtual Desktop förhandsversion, kan du skapa en tjänst huvudnamn till:

- Automatisera hanteringsuppgifter för specifika virtuella Windows-skrivbordet.
- Använd som autentiseringsuppgifter i stället för MFA-krävs användare när du kör en Azure Resource Manager-mall för virtuella Windows-skrivbordet.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett huvudnamn för tjänsten i Azure Active Directory.
> * Skapa en rolltilldelning i virtuella Windows-skrivbordet.
> * Logga in på virtuella Windows-skrivbordet genom att använda tjänstens huvudnamn.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan skapa tjänstens huvudnamn och rolltilldelningar, måste du göra tre saker:

1. Installera modulen AzureAD. Kör PowerShell som administratör och kör följande cmdlet för att installera modulen:

    ```powershell
    Install-Module AzureAD
    ```

2. Kör följande cmdlets med värdena i citattecken ersättas med värdena som är relevanta för din session.

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. Följ alla anvisningarna i den här artikeln i samma PowerShell-session. Det kanske inte fungerar om du stänger fönstret och tillbaka till den senare.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Skapa ett huvudnamn för tjänsten i Azure Active Directory

När du har uppfyllt kraven i din PowerShell-session, kör du följande PowerShell-cmdletar för att skapa en multitenant-tjänst tjänstobjekt i Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Skapa en rolltilldelning i förhandsversion för virtuella skrivbord i Windows

Nu när du har skapat ett tjänstens huvudnamn kan använda du det för att logga in på virtuella Windows-skrivbordet. Se till att logga in med ett konto som har behörighet att skapa rolltilldelningen.

Först [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

Kör följande PowerShell-cmdletar för att ansluta till virtuella Windows-skrivbordet och skapa en rolltilldelning för tjänsten huvudnamn.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Logga in med tjänstens huvudnamn

När du skapar en rolltilldelning för tjänsten huvudnamn kan du kontrollera att tjänstens huvudnamn kan logga in på virtuella Windows-skrivbordet genom att köra följande cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

När du har loggat in måste du kontrollera att allt fungerar genom att testa några Windows Virtual Desktop PowerShell-cmdlets med tjänstens huvudnamn.

## <a name="view-your-credentials-in-powershell"></a>Visa dina inloggningsuppgifter i PowerShell

Innan du avslutar din PowerShell-session kan visa dina autentiseringsuppgifter och Skriv ned dem för framtida bruk. Lösenordet är särskilt viktigt eftersom du kan inte hämta det när du stänger det här PowerShell-session.

Här följer tre autentiseringsuppgifterna som du bör anteckna de cmdletar som du behöver köra för att få dem:

- Lösenord:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Klient-ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Program-ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>Nästa steg

När du har skapat tjänstens huvudnamn och tilldelats en roll i din klient för virtuella Windows-skrivbordet, kan du använda det för att skapa en pool med värden. Om du vill veta mer om värden pooler kan du fortsätta till självstudien för att skapa en pool med värden i virtuella Windows-skrivbordet.

 > [!div class="nextstepaction"]
 > [Virtuella Windows-skrivbordet värd pool självstudien](./create-host-pools-azure-marketplace.md)
