---
title: Rollbaserad åtkomstkontroll i Azure Automation
description: Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Den här artikeln beskriver hur du konfigurerar RBAC i Azure Automation.
keywords: automation rbac, rollbaserad åtkomstkontroll, azure rbac
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b307a497e69bd6c2dcc7b415b2d94335459f7fd3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544996"
---
# <a name="role-based-access-control-in-azure-automation"></a>Rollbaserad åtkomstkontroll i Azure Automation

Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Med hjälp av [RBAC](../role-based-access-control/overview.md), du kan hålla isär uppgifter i ditt team och bevilja endast mängden åtkomst till användare, grupper och program som de behöver för att utföra sitt arbete. Rollbaserad åtkomst kan beviljas till användare som använder Azure Portal, Azure-kommandoradsverktygen eller Azure Management-API:er.

## <a name="roles-in-automation-accounts"></a>Roller i Automation-konton

I Azure Automation beviljas åtkomst genom att lämplig RBAC-roll tilldelas till användare, grupper och program i Automation-kontoomfånget. Följande är de inbyggda roller som stöds av ett Automation-konto:

| **Roll** | **Beskrivning** |
|:--- |:--- |
| Ägare |Ägarrollen ger tillgång till alla resurser och åtgärder i ett Automation-konto, inklusive att ge åtkomst till andra användare, grupper och program hanteringsåtkomst till Automation-kontot. |
| Deltagare |Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare |Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar. |
| Automation-operatör |Rollen Automation-operatör kan du visa runbook-namn och egenskaper att skapa och hantera jobb för alla runbooks i ett Automation-konto. Den här rollen är användbar om du vill skydda dina Automation-kontoresurser, t.ex. autentiseringstillgångar och runbooks, så att de inte kan visas eller ändras men fortfarande vill att medlemmar i din organisation ska kunna köra dessa runbooks. |
|Automation-jobboperator|Rollen Automation-Jobboperator kan du skapa och hantera jobb för alla runbooks i ett Automation-konto.|
|Automation Runbook-operator|Rollen Automation Runbook-operatör kan du visa namn och egenskaper för en runbook.|
| Log Analytics Contributor | Rollen Log Analytics Contributor kan du läsa alla övervakningsdata och redigera övervakningsinställningarna. Redigera övervakningsinställningarna omfattar att lägga till VM-tillägg till virtuella datorer, läsa lagringskontonycklar för att kunna konfigurera loggsamlingar från Azure storage, skapa och konfigurera automationskonton, lägga till lösningar och konfigurera Azure diagnostics på alla Azure-resurser.|
| Log Analytics Reader | Log Analytics Reader-rollen kan du visa och söka efter alla data samt visa övervakningsinställningar. Detta omfattar visning av konfigurationen av Azure diagnostics på alla Azure-resurser. |
| Övervakningsdeltagare | Övervakning av deltagarrollen kan du läsa alla övervakningsdata och uppdatera övervakningsinställningarna.|
| Övervakningsläsare | Med övervakning läsarrollen kan du läsa alla övervakningsdata. |
| Administratör för användaråtkomst |Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

## <a name="role-permissions"></a>Rollbehörigheter

I följande tabeller beskrivs de specifika behörigheter till varje roll. Detta kan inkludera åtgärder, som ger behörigheter och NotActions, vilket begränsar dem.

### <a name="owner"></a>Ägare

Ägare kan hantera allt, inklusive åtkomst. I följande tabell visas de behörigheter som beviljas för rollen:

|Åtgärder|Beskrivning|
|---|---|
|Microsoft.Automation/automationAccounts/|Skapa och hantera resurser av alla typer.|

### <a name="contributor"></a>Deltagare

Deltagare kan hantera allt utom åtkomst. I följande tabell visas de behörigheter som beviljas och nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Skapa och hantera resurser för alla typer av|
|**Inte åtgärder**||
|Microsoft.Authorization/*/Delete| Ta bort roller och rolltilldelningar.       |
|Microsoft.Authorization/*/Write     |  Skapa roller och rolltilldelningar.       |
|Microsoft.Authorization/elevateAccess/Action    | Nekar möjligheten att skapa en administratör för användaråtkomst.       |

### <a name="reader"></a>Läsare

En läsare kan visa alla resurser i ett Automation-konto men göra inte några ändringar.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Visa alla resurser i ett Automation-konto. |

### <a name="automation-operator"></a>Automation-operatör

En Automation-operatör kan skapa och hantera jobb och läsa runbook-namn och egenskaper för alla runbooks i ett Automation-konto.  Obs! Om du vill kontrollera operatorn åtkomst till enskilda runbooks och sedan inte den här rollen och i stället använda rollerna ”Automation-Jobboperator och Automation Runbook-operatör i kombination. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läsa auktorisering.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Läsa Hybrid Runbook Worker-resurser.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista jobb för runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som har pausats.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryta ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läsa Jobbströmmar och utdata.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Hämta utdata för ett jobb.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Hämta ett Azure Automation-Jobbschema.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Skapa ett Azure Automation-Jobbschema.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Hämta den arbetsyta som är länkad till automation-kontot.|
|Microsoft.Automation/automationAccounts/read|Skaffa ett Azure Automation-konto.|
|Microsoft.Automation/automationAccounts/runbooks/read|Hämta en Azure Automation-runbook.|
|Microsoft.Automation/automationAccounts/schedules/read|Hämta en Azure Automation-schematillgång.|
|Microsoft.Automation/automationAccounts/schedules/write|Skapa eller uppdatera en Azure Automation-schematillgång.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läsa roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      |Skapa och hantera distribution av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviseringsregler.        |
|Microsoft.Support/* |Skapa och hantera supportärenden.|

### <a name="automation-job-operator"></a>Automation-jobboperator

En Automation-Jobboperator roll beviljas på Automation-kontoomfånget. På så sätt kan operatören behörighet att skapa och hantera jobb för alla runbooks i kontot. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läsa auktorisering.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista jobb för runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som har pausats.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryta ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läsa Jobbströmmar och utdata.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett jobb pågår.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Läsa roller och rolltilldelningar.       |
|Microsoft.Resources/deployments/*      |Skapa och hantera distribution av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviseringsregler.        |
|Microsoft.Support/* |Skapa och hantera supportärenden.|

### <a name="automation-runbook-operator"></a>Automation Runbook-operator

En Automation Runbook-operatörsrollen beviljas definitionsområdet Runbook. En Runbook Automation-operatör kan visa runbookens namn och egenskaper.  Den här rollen som kombineras med rollen ”Automation-Jobboperator gör det möjligt för operatorn som ska också skapa och hantera jobb för runbook. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Lista över runbooks.        |
|Microsoft.Authorization/*/read      | Läsa auktorisering.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läsa roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      | Skapa och hantera distribution av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviseringsregler.        |
|Microsoft.Support/*      | Skapa och hantera supportärenden.        |

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

En Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningarna. Redigera övervakningsinställningarna omfattar att lägga till VM-tillägget för virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera loggsamlingar från Azure-lagring. Skapa och konfigurera automationskonton; lägga till lösningar och konfigurera Azure diagnostics på alla Azure-resurser. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.Automation/automationAccounts/*|Hantera automation-konton.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Skapa och hantera virtuella datorer, tillägg.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista klassiska lagringskontonycklar.|
|Microsoft.Compute/virtualMachines/extensions/*|Skapa och hantera klassiska virtuella datorer, tillägg.|
|Microsoft.Insights/alertRules/*|Läs/Skriv/ta bort aviseringsregler.|
|Microsoft.Insights/diagnosticSettings/*|Läs/Skriv/ta bort diagnostikinställningar.|
|Microsoft.OperationalInsights/*|Hantera Azure Monitor-loggar.|
|Microsoft.OperationsManagement/*|Hantera lösningar i arbetsytor.|
|Microsoft.Resources/deployments/*|Skapa och hantera distribution av resursgrupper.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Skapa och hantera distribution av resursgrupper.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lista nycklar för lagringskonto.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|

### <a name="log-analytics-reader"></a>Log Analytics Reader

En Log Analytics Reader kan visa och söka i alla övervakningsdata och dessutom visa övervakningsinställningar, även konfiguration av Azure-diagnostik visas på alla Azure-resurser. I följande tabell visas de behörigheter som beviljas eller nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Hantera frågor i Azure Monitor-loggar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Azure Monitor-loggdata.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|
|**Inte åtgärder**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Det går inte att läsa in delade åtkomstnycklar.|

### <a name="monitoring-contributor"></a>Övervakningsdeltagare

Övervaka deltagare kan läsa alla övervakningsdata och uppdatera inställningarna för övervakning. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.AlertsManagement/alerts/*|Hantera aviseringar.|
|Microsoft.AlertsManagement/alertsSummary/*|Hantera aviseringar instrumentpanelen.|
|Microsoft.Insights/AlertRules/*|Hantera Varningsregler.|
|Microsoft.Insights/components/*|Hantera Application Insights-komponenter.|
|Microsoft.Insights/DiagnosticSettings/*|Hantera diagnostikinställningar.|
|Microsoft.Insights/eventtypes/*|Lista över aktivitetslogghändelser (av hanteringshändelser) i en prenumeration. Den här behörigheten gäller för både program- och portalen åtkomst till aktivitetsloggen.|
|Microsoft.Insights/LogDefinitions/*|Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i aktivitetsloggen.|
|Microsoft.Insights/MetricDefinitions/*|Läs måttdefinitionerna (lista över tillgängliga typer av mått för en resurs).|
|Microsoft.Insights/Metrics/*|Läsa måtten för en resurs.|
|Microsoft.Insights/Register/Action|Registrera Microsoft.Insights-providern.|
|Microsoft.Insights/webtests/*|Hantera webbtester med Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Hantera lösningspaket för Azure Monitor-loggar.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Hantera Azure Monitor loggar sparade sökningar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Log Analytics-arbetsytor.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista över nycklar för Log Analytics-arbetsytan.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Hantera Azure Monitor-loggar insight lagringskonfigurationer.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|
|Microsoft.WorkloadMonitor/workloads/*|Hantera arbetsbelastningar.|

### <a name="monitoring-reader"></a>Övervakningsläsare

En övervakning läsare kan läsa alla övervakningsdata. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa resurser av alla typer utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Log Analytics-arbetsytor.|
|Microsoft.Support/*|Skapa och hantera supportärenden|

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

En administratör för användaråtkomst kan hantera användarnas åtkomst till Azure-resurser. I följande tabell visas de behörigheter som beviljas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|* / läsa|Läsa alla resurser|
|Microsoft.Authorization/*|Hantera auktorisering|
|Microsoft.Support/*|Skapa och hantera supportärenden|

## <a name="onboarding"></a>Publicering

Följande tabeller visar de minsta nödvändiga behörigheter som behövs för att konfigurera virtuella datorer för ändringsspårning eller uppdatera hanteringslösningar.

### <a name="onboarding-from-a-virtual-machine"></a>Onboarding från en virtuell dator

|**Åtgärd**  |**Permission**  |**Minsta omfång**  |
|---------|---------|---------|
|Skriva ny distribution      | Microsoft.Resources/deployments/*          |Prenumeration          |
|Skriva ny resursgrupp      | Microsoft.Resources/subscriptions/resourceGroups/write        | Prenumeration          |
|Skapa ny standard arbetsyta      | Microsoft.OperationalInsights/workspaces/write         | Resursgrupp         |
|Skapa nytt konto      |  Microsoft.Automation/automationAccounts/write        |Resursgrupp         |
|Länk-arbetsyta och konto      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Arbetsyta</br>Automation-konto
|Skapa lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Resursgrupp          |
|Skapa MMA-tillägg      | Microsoft.Compute/virtualMachines/write         | Virtuell dator         |
|Skapa sparad sökning      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Skapa scope-konfiguration      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Länk-lösning i scope-konfigurationen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösning         |
|Publiceringsstatus för Kontrollera – Läs arbetsyta      | Microsoft.OperationalInsights/workspaces/read         | Arbetsyta         |
|Kontrollera Onboarding - Läs länkad arbetsyta-egenskapen för konto     | Microsoft.Automation/automationAccounts/read      | Automation-konto        |
|Publiceringsstatus för Kontrollera – Läs lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Lösning         |
|Kontrollera Onboarding - Läs VM      | Microsoft.Compute/virtualMachines/read         | Virtuell dator         |
|Publiceringsstatus för Kontrollera – Läs konto      | Microsoft.Automation/automationAccounts/read  |  Automation-konto   |

### <a name="onboarding-from-automation-account"></a>Onboarding från Automation-konto

|**Åtgärd**  |**Permission** |**Minsta omfång**  |
|---------|---------|---------|
|Skapa ny distribution     | Microsoft.Resources/deployments/*        | Prenumeration         |
|Skapa ny resursgrupp     | Microsoft.Resources/subscriptions/resourceGroups/write         | Prenumeration        |
|AutomationOnboarding bladet – Skapa ny arbetsyta     |Microsoft.OperationalInsights/workspaces/write           | Resursgrupp        |
|AutomationOnboarding-bladet – Läs länkad arbetsyta     | Microsoft.Automation/automationAccounts/read        | Automation-konto       |
|AutomationOnboarding-bladet – Läs lösning     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Lösning        |
|AutomationOnboarding-bladet – läsa arbetsyta     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Arbetsyta        |
|Skapa länk för arbetsyta och konto     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skriva konto för lådan      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Skapa lösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Resursgrupp         |
|Skapa/redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skapa/redigera scope-config     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Länk-lösning i scope-konfigurationen      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Lösning         |
|**Steg 2 – publicera flera virtuella datorer**     |         |         |
|VMOnboarding bladet – skapa MMA-tillägg     | Microsoft.Compute/virtualMachines/write           | Virtuell dator        |
|Skapa / redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write           | Arbetsyta        |
|Skapa / redigera scope-config  | Microsoft.OperationalInsights/workspaces/write   | Arbetsyta|

## <a name="update-management"></a>Hantering av uppdateringar

Uppdateringshantering når för flera tjänster att tillhandahålla sin tjänst. I följande tabell visas de behörigheter som krävs för att hantera distributioner av management:

|**Resurs**  |**Roll**  |**Omfång**  |
|---------|---------|---------|
|Automation-konto     | Log Analytics Contributor       | Automation-konto        |
|Automation-konto    | Virtuell datordeltagare        | Resursgruppen för kontot        |
|Log Analytics-arbetsyta     | Log Analytics Contributor| Log Analytics-arbetsyta        |
|Log Analytics-arbetsyta |Log Analytics Reader| Prenumeration|
|Lösning     |Log Analytics Contributor         | Lösning|
|Virtuell dator     | Virtuell datordeltagare        | Virtuell dator        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurera RBAC för ditt Automation-konto

I följande avsnitt beskrivs hur du konfigurerar RBAC för ditt Automation-konto via den [portal](#configure-rbac-using-the-azure-portal) och [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurera RBAC med Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Automation-kontot från sidan Automation-konton.
2. Klicka på den **åtkomstkontroll (IAM)** kontroll i det övre vänstra hörnet. Då öppnas det **åtkomstkontroll (IAM)** sidan där du kan lägga till nya användare, grupper och program för att hantera ditt Automation-kontot och visa befintliga roller som kan konfigureras för Automation-kontot.
3. Klicka på den **rolltilldelningar** fliken.

   ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Lägga till en ny användare och tilldela en roll

1. Från den **åtkomstkontroll (IAM)** klickar du på **+ Lägg till rolltilldelning** att öppna den **Lägg till rolltilldelning** sida där du kan lägga till en användare, grupp eller program och tilldela en roll till dem.

2. Välj en roll i listan över tillgängliga roller. Du kan välja någon av de tillgängliga inbyggda rollerna som har stöd för ett Automation-konto eller en anpassad roll som du har definierat.

3. Skriv användarnamnet för användaren som du vill ge behörigheter till i den **Välj** fält. Välj användaren i listan och klicka på **spara**.

   ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu bör du se användaren på den **användare** sida med den valda rollen tilldelad

   ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)

   Du kan också tilldela en roll till användaren från sidan **Roller**.
4. Klicka på **roller** från den **åtkomstkontroll (IAM)** kan du öppna den **roller** sidan. Härifrån kan du visa namnet på rollen och antalet användare och grupper som har tilldelats till rollen.

    ![Tilldela en roll från sidan Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Rollbaserad åtkomstkontroll kan bara anges på Automation-kontoomfånget och inte på en resurs under Automation-kontot.

#### <a name="remove-a-user"></a>Ta bort en användare

Du kan ta bort åtkomstbehörigheten för en användare som inte hanterar Automation-kontot eller som inte längre arbetar i organisationen. Nedan följer stegen för att ta bort en användare:

1. Från den **åtkomstkontroll (IAM)** väljer användaren vill ta bort och klicka på **ta bort**.
2. Klicka på knappen **Ta bort** på sidan med tilldelningsinformation.
3. Bekräfta borttagningen genom att klicka på **Ja**.

   ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurera RBAC med hjälp av PowerShell

Du kan också konfigurera rollbaserad åtkomst till ett Automation-konto med hjälp av följande [Azure PowerShell-cmdlets](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) visar alla RBAC-roller som är tillgängliga i Azure Active Directory. Du kan använda det här kommandot tillsammans med egenskapen **Namn** för att visa en lista över alla de åtgärder som kan vidtas av en viss roll.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Följande är på Exempelutdata:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) visar Azure AD RBAC-rolltilldelningar i det specificerade omfånget. Utan parametrar returnerar detta kommando alla rolltilldelningar som skapats under prenumerationen. Använd parametern **ExpandPrincipalGroups** om du vill visa en lista med alla åtkomsttilldelningar för den angivna användaren och för de grupper som användaren är medlem i.
    **Exempel:** Använd följande kommando för att lista alla användare och deras roller i ett automation-konto.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är på Exempelutdata:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[Ny-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) att tilldela åtkomst till användare, grupper och program till ett visst omfång.
    **Exempel:** Använd följande kommando för att tilldela rollen ”Automation-operatör” för en användare i Automation-kontoomfånget.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är på Exempelutdata:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Använd [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) att ta bort åtkomst till en specifik användare, grupp eller program från ett visst omfång.
    **Exempel:** Använd följande kommando för att ta bort användaren från rollen ”Automation-operatör” i Automation-kontoomfånget.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

I föregående exempel ersätter **inloggnings-Id**, **prenumerations-Id**, **Resursgruppsnamn**, och **Automation-kontonamn** med din information om kontot. Välj **Ja** när du uppmanas att bekräfta innan du fortsätter att ta bort rolltilldelningen för användaren.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Användarupplevelse för Automation-operatörsrollen - Automation-konto

När en användare som har tilldelats rollen Automation-operatör i Automation-kontoomfånget visar det Automation-kontot som de är tilldelade till de bara kan visa en lista med runbooks, runbook-jobb och scheman som skapats i Automation-konto, men inte deras definition. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga runbook-jobbet. Användaren har inte åtkomst till andra Automation-resurser, till exempel konfigurationer, hybrid worker-grupper eller DSC-noder.

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurera RBAC för Runbooks

Azure Automation kan du tilldela RBAC till specifika runbooks. Om du vill göra detta kör följande skript för att lägga till en användare till en specifik runbook. Följande skript som kan vara körts av en Automation-kontoadministratör eller en Innehavaradministratör.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

En gång körde, har användaren logga in på Azure-portalen och visa **alla resurser**. I listan visas den Runbook som de har lagts till som en **Automation Runbook-operatör** för.

![Runbook RBAC i portalen](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Användarupplevelse för Automation-operatörsrollen - Runbook

När en användare som har tilldelats rollen Automation-operatör på Runbook-omfångsvyer en Runbook som de är tilldelade till, kan de bara starta runbooken och visa runbook-jobb.

![Endast har åtkomst till start](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du kan konfigurera RBAC på olika sätt med Azure Automation finns i [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Mer information om hur du kan starta en runbook på olika sätt finns i [Starta en runbook](automation-starting-a-runbook.md)
* Information om olika runbook-typer finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)

