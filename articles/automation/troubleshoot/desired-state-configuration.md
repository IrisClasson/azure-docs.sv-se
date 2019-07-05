---
title: Felsökning av problem med Azure Automation Desired State Configuration (DSC)
description: Den här artikeln innehåller information om hur du felsöker Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53fef426c927c690a3b697055f467f6cd35c532c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477527"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Felsöka Desired State Configuration (DSC)

Den här artikeln innehåller information om felsökning av problem med Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Vanliga fel när du arbetar med Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenario: En konfiguration med specialtecken kan inte tas bort från portalen

#### <a name="issue"></a>Problem

När du försöker ta bort en DSC-konfiguration från portalen kan du se följande fel:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Orsak

Det här felet är ett tillfälligt fel som ska matchas.

#### <a name="resolution"></a>Lösning

* Använd cmdleten ”Remove-AzAutomationDscConfiguration” Az för att ta bort konfigurationen.
* I dokumentationen för denna cmdlet har inte uppdaterats ännu.  Fram till dess, finns i dokumentationen för AzureRM-modulen.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenario: Det gick inte att registrera Dsc-agenten

#### <a name="issue"></a>Problem

När du försöker köra `Set-DscLocalConfigurationManager` eller en annan DSC-cmdlet som du får felet:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Orsak

Det här felet orsakas normalt av en brandvägg, datorn är bakom en proxyserver eller andra nätverksfel.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn har åtkomst till rätt slutpunkterna för Azure Automation DSC och försök igen. En lista över portar och adresser som behövs finns i [nätverksplanering](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Scenario: Noden är i ett misslyckat tillstånd med ett ”kunde inte hittas”-fel

#### <a name="issue"></a>Problem

Noden har en rapport med **misslyckades** status och som innehåller felet:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligen när noden har tilldelats ett namn (till exempel ABC) i stället för en nodkonfigurationsnamn (till exempel ABC. Webbserver).

#### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar nod med ”nodkonfigurationsnamn” och inte ”Konfigurationsnamnet”.
* Du kan tilldela en nodkonfiguration till en nod med hjälp av Azure portal eller med en PowerShell-cmdlet.

  * Om du vill tilldela en nodkonfiguration till en nod med hjälp av Azure portal, öppna den **DSC-noder** , och sedan väljer en nod och klickar på **tilldela nodkonfiguration** knappen.  
  * Om du vill tilldela en nodkonfiguration till en nod med hjälp av PowerShell-cmdleten, använda **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Scenario: Inga nodkonfigurationer (MOF-filer) skapas när en konfiguration kompileras

#### <a name="issue"></a>Problem

Dina DSC-Kompileringsjobb pausar med fel:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Orsak

När följande uttryck i **nod** nyckelord i DSC-konfigurationen utvärderas till `$null`, och inga nodkonfigurationer produceras.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar problemet på:

* Se till att uttrycket bredvid den **noden** nyckelord i konfigurationen definitionen är inte utvärderar till $null.
* Om du skickar ConfigurationData vid kompilering konfigurationen, se till att du passerar de förväntade värdena som konfigurationen kräver från [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenario: DSC-nod-rapporten blir fastnat tillståndet ”pågår”

#### <a name="issue"></a>Problem

DSC-agenten utdata:

```error
No instance found with given property values
```

#### <a name="cause"></a>Orsak

Du har uppgraderat din version av WMF och har skadad WMI.

#### <a name="resolution"></a>Lösning

Åtgärda problemet genom att följa instruktionerna i den [DSC kända problem och begränsningar](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artikeln.

### <a name="issue-using-credential"></a>Scenario: Det går inte att använda en autentiseringsuppgift i en DSC-konfiguration

#### <a name="issue"></a>Problem

Dina DSC-Kompileringsjobb pausades med fel:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Orsak

Du har använt en autentiseringsuppgift i en konfiguration, men inte anger rätt **ConfigurationData** att ställa in **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration.

#### <a name="resolution"></a>Lösning

* Se till att skicka in rätt **ConfigurationData** att ställa in **PSDscAllowPlainTextPassword** till true för varje nodkonfiguration som nämns i konfigurationen. Mer information finns i [tillgångar i Azure Automation DSC](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Scenario: Onboarding från dsc-tillägget ”fel vid bearbetningen av tillägget” fel

#### <a name="issue"></a>Problem

När onboarding med hjälp av DSC-tillägg, ett fel uppstår med felet:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligen när noden har tilldelats ett nodnamn konfiguration som inte finns i tjänsten.

#### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar nod med en nodkonfigurationsnamn som exakt matchar namnet i tjänsten.
* Du kan välja att inte inkludera namnet på noden konfigurationen, vilket leder onboarding noden men inte tilldela en nodkonfiguration

### <a name="failure-linux-temp-noexec"></a>Scenario: Tillämpa en konfiguration i Linux, uppstår ett fel med ett allmänt fel

#### <a name="issue"></a>Problem

När du använder en konfiguration i Linux, inträffar ett fel med felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Orsak

Kunder har identifierat att den aktuella versionen av DSC kommer inte att tillämpa konfigurationer om/tmp-plats har angetts till noexec.

#### <a name="resolution"></a>Lösning

* Ta bort alternativet noexec från/tmp-platsen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
