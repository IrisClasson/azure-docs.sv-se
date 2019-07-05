---
title: Inloggningstillgångar i Azure Automation
description: Inloggningstillgångar i Azure Automation innehåller säkerhetsreferenser som kan användas för att autentisera mot resurser som nås av runbook eller DSC-konfiguration. Den här artikeln beskriver hur du skapar inloggningstillgångar och använda dem i en runbook eller DSC-konfiguration.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 44bc49d10c492822c1b5d30ad5794ac2522cb918
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478157"
---
# <a name="credential-assets-in-azure-automation"></a>Inloggningstillgångar i Azure Automation

En Automation-autentiseringsuppgiftstillgång innehåller ett-objekt som innehåller säkerhetsreferenser, som ett användarnamn och lösenord. Runbooks och DSC-konfigurationer kan använda cmdlet: ar som accepterar ett PSCredential-objekt för autentisering eller de kan extrahera användarnamnet och lösenordet för PSCredential-objekt att förse vissa program eller tjänster som kräver autentisering. Egenskaperna för en autentiseringsuppgift lagras säkert i Azure Automation och kan nås i runbook eller DSC-konfiguration med den [Get-AutomationPSCredential](#activities) aktivitet.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Säkra tillgångar i Azure Automation omfattar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln lagras i Key Vault. Innan du lagrar en säker resurs som lästs in från Key Vault nyckeln och sedan används för att kryptera tillgången.

## <a name="azure-classic-powershell-cmdlets"></a>Azure klassiska PowerShell-cmdlets

Cmdlets i följande tabell används för att skapa och hantera inloggningstillgångar i automation med Windows PowerShell.  De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview), som är tillgängligt för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Hämtar information om en autentiseringsuppgifttillgång. Du kan bara hämta autentiseringsuppgifterna själva från **Get-AutomationPSCredential** aktivitet. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Skapar en ny Automation-autentiseringsuppgift. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Tar bort autentiseringsuppgifter för Automation. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Anger egenskaperna för befintliga autentiseringsuppgifter för Automation. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdletar

För AzureRM används cmdletar i följande tabell för att skapa och hantera inloggningstillgångar i automation med Windows PowerShell.  De levereras som en del av den [AzureRM.Automation modulen](/powershell/azure/overview), som är tillgängligt för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Hämtar information om en autentiseringsuppgifttillgång. Detta returnerar ett PSCredential-objekt.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Skapar en ny Automation-autentiseringsuppgift. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Tar bort autentiseringsuppgifter för Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Anger egenskaperna för befintliga autentiseringsuppgifter för Automation. |

## <a name="activities"></a>Aktiviteter

Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i en runbook och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:--- |:--- |
| Get-AutomationPSCredential |Hämtar en autentiseringsuppgift som ska användas i en runbook eller DSC-konfiguration. Returns a [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) object. |

> [!NOTE]
> Du bör undvika att använda variabler i – Name-parametern i Get-AutomationPSCredential eftersom detta kan göra det svårare att hitta beroenden mellan runbooks eller DSC-konfigurationer och autentiseringstillgångar vid designtillfället.

## <a name="python2-functions"></a>Python2-funktioner

Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en Python2-runbook.

| Funktion | Beskrivning |
|:---|:---|
| automationassets.get_automation_credential | Hämtar information om en autentiseringsuppgifttillgång. |

> [!NOTE]
> Du måste importera modulen ”automationassets” överst i Python-runbook för att komma åt funktionerna för tillgången.

## <a name="creating-a-new-credential-asset"></a>Skapa en ny autentiseringstillgång

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Skapa en ny autentiseringstillgång med Azure portal

1. Från ditt automation-konto väljer **autentiseringsuppgifter** under **delade resurser**.
1. Klicka på **+ Lägg till autentiseringsuppgift för**.
1. Fyll i formuläret och klicka på **skapa** att spara de nya autentiseringsuppgifter.

> [!NOTE]
> Användarkonton som använder multifaktorautentisering stöds inte för användning i Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Att skapa en ny autentiseringstillgång med Windows PowerShell

Följande exempelkommandon visar hur du skapar en ny automation-autentiseringsuppgift. Ett PSCredential-objekt skapas först med namn och lösenord och sedan används för att skapa autentiseringstillgång. Du kan också använda den **Get-Credential** cmdlet för att bli ombedd att ange ett namn och lösenord.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Med hjälp av en PowerShell-autentiseringsuppgift

Du kan hämta en autentiseringstillgång i en runbook eller DSC-konfiguration med den **Get-AutomationPSCredential** aktivitet. Detta returnerar en [PSCredential-objekt](/dotnet/api/system.management.automation.pscredential) som du kan använda med en aktivitet eller cmdlet som kräver en PSCredential-parameter. Du kan också hämta egenskaperna för objektet för autentiseringsuppgifter för att använda individuellt. Objektet har en egenskap för användarnamnet och säkert lösenord eller använda den **GetNetworkCredential** metod för att returnera en [NetworkCredential](/dotnet/api/system.net.networkcredential) objekt som ger en oskyddat version av den lösenordet.

> [!NOTE]
> **Get-AzureRmAutomationCredential** inte returnerar en **PSCredential** som kan användas för autentisering. Den innehåller endast information om autentiseringsuppgifterna. Om du behöver använda en autentiseringsuppgift i en runbook måste du använda den **Get-AutomationPSCredential** att hämta den **PSCredential** objekt.

### <a name="textual-runbook-sample"></a>Textbaserade runbook-exempel

Följande exempelkommandon visar hur du använder en PowerShell-autentiseringsuppgift i en runbook. I det här exemplet autentiseringsuppgifterna hämtas och dess användarnamn och lösenord som har tilldelats till variabler.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Du kan också använda en autentiseringsuppgift för att autentisera till Azure med [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). I de flesta fall bör du använda en [kör som-konto](../manage-runas-account.md) och hämta den med [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Grafisk runbook-exempel

Du lägger till en **Get-AutomationPSCredential** aktivitet för att en grafisk runbook genom att högerklicka på autentiseringsuppgiften i rutan bibliotek i den grafiska redigeraren och välja **Lägg till på ytan**.

![Lägg till autentiseringsuppgift på ytan](../media/credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgift i en grafisk runbook.  I det här fallet den används för autentisering för en runbook till Azure-resurser enligt beskrivningen i [autentisera Runbooks med Azure AD-användarkonto](../automation-create-aduser-account.md).  Den första aktiviteten hämtar de autentiseringsuppgifter som har åtkomst till Azure-prenumeration.  Den **Add-AzureAccount** aktiviteten använder sedan den här autentiseringsuppgiften för autentisering för alla aktiviteter som kommer efter den.  En [pipelinelänk](../automation-graphical-authoring-intro.md#links-and-workflow) är här sedan **Get-AutomationPSCredential** förväntar sig ett enda objekt.  

![Lägg till autentiseringsuppgift på ytan](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Med hjälp av en PowerShell-autentiseringsuppgift i DSC

När DSC-konfigurationer i Azure Automation kan referera till inloggningstillgångar med **Get-AutomationPSCredential**, inloggningstillgångar kan också skickas via parametrar, om du vill. Mer information finns i [kompilera konfigurationer i Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Med hjälp av autentiseringsuppgifter i Python2

I följande exempel visas ett exempel för att komma åt autentiseringsuppgifter i Python2-runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om länkar i grafisk redigering i [länkar i grafisk redigering](../automation-graphical-authoring-intro.md#links-and-workflow)
* Information om de olika autentiseringsmetoderna med Automation finns i [säkerheten i Azure Automation](../automation-security-overview.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](../automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](../automation-first-runbook-textual.md)
* Kom igång med Python2-runbooks, se [min första Python2-runbook](../automation-first-runbook-textual-python2.md) 
