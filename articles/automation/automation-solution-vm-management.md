---
title: Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning
description: Denna lösning för hantering av virtuell dator startar och stoppar Azure Resource Manager virtuella datorer enligt ett schema och övervakar proaktivt från Azure Monitor-loggar.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4e1ad106b928c41bd6940d7c3713b5fb34afe3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389114"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning i Azure Automation

Starta/stoppa virtuella datorer under kontorstid lösning startar och stoppar Azure virtuella datorer med hjälp av användardefinierade scheman, ger insikter via Azure Monitor-loggar och skickar valfri e-postmeddelanden med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md). Den stöder både Azure Resource Manager och klassiska virtuella datorer i de flesta fall.

> [!NOTE]
> Starta/Stoppa VM under kontorstid lösning har testats med Azure-moduler som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara Automation-kontot som används för att köra Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning. Du kan fortfarande använda nyare versioner av Azure-modulen i din andra Automation-konton, enligt beskrivningen i [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md)

Den här lösningen ger en decentraliserad prisvärda automatiseringsalternativet för användare som vill optimera kostnaderna för virtuella datorer. Med den här lösningen kan du:

- Schemalägga virtuella datorer att starta och stoppa.
- Schemalägga virtuella datorer att starta och stoppa i stigande ordning med hjälp av Azure-taggar (stöds inte för klassiska virtuella datorer).
- Autostop virtuella datorer baserat på låg CPU-belastning.

Följande är begränsningar i den aktuella lösningen:

- Den här lösningen hanterar virtuella datorer i valfri region, men kan endast användas i samma prenumeration som Azure Automation-konto.
- Den här lösningen är tillgängligt i Azure och AzureGov för alla regioner som stöder en Log Analytics-arbetsyta, ett Azure Automation-konto och aviseringar. AzureGov regioner stöder för närvarande inte e-postfunktioner.

> [!NOTE]
> Om du använder lösningen för klassiska virtuella datorer kan kommer sedan alla dina virtuella datorer att behandlas sekventiellt per molntjänst. Virtuella datorer är fortfarande bearbetas parallellt över olika molntjänster.
>
> Azure Cloud Solution Provider (Azure CSP)-prenumerationer stöder endast Azure Resource Manager-modellen, icke - Azure Resource Manager-tjänster är inte tillgängliga i programmet. När lösningen Starta/Stoppa körs kan du få fel eftersom den innehåller cmdletar för att hantera klassiska resurser. Läs mer om CSP i [tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). Om du använder en CSP-prenumeration kan du ändra den [ **External_EnableClassicVMs** ](#variables) variabeln **FALSKT** efter distributionen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Runbooks för den här lösningen fungerar med en [kör som-konto](automation-create-runas-account.md). Kör som-kontot är den lämpligaste autentiseringsmetoden eftersom den använder certifikatautentisering istället för ett lösenord som kan upphöra att gälla eller ändras ofta.

Det rekommenderas att använda ett separat Automation-konto för att starta/stoppa VM-lösning. Detta beror ofta Azure modulversioner uppgraderas och deras parametrar kan ändras. Lösningen Starta/Stoppa VM uppgraderas inte på samma takt så inte fungerar med nyare versioner av de cmdletar som används. Vi rekommenderar att testa modulen uppdateringar i ett test Automation-konto innan du importerar dem i en produktionsmiljö Automation-konto.

### <a name="permissions-needed-to-deploy"></a>Behörigheter som krävs för att distribuera

Det finns vissa behörigheter som en användare måste ha för att distribuera den Starta/stoppa virtuella datorer utanför timmar lösning. Dessa behörigheter finns olika om du använder en Automation-kontot och Log Analytics-arbetsyta som skapats i förväg och skapa nya filer under distributionen. Om du är deltagare för prenumerationen och en Global administratör i din Azure Active Directory-klient, behöver du inte konfigurera följande behörigheter. Om du inte har dessa rättigheter eller måste du konfigurera en anpassad roll, ser du de behörigheter som krävs nedan.

#### <a name="pre-existing-automation-account-and-log-analytics-account"></a>Befintliga Automation-kontot och Log Analytics-konto

Att distribuera den Starta/stoppa virtuella datorer utanför timmar lösningen till ett Automation-kontot och Log Analytics-lösningen distribueras användaren kräver följande behörigheter på den **resursgrupp**. Läs mer om administratörsroller i [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

| Behörighet | Scope|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Resursgrupp |
| Microsoft.Automation/automationAccounts/variables/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/schedules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/runbooks/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/connections/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/certificates/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/modules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/modules/read | Resursgrupp |
| Microsoft.automation/automationAccounts/jobSchedules/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/jobs/write | Resursgrupp |
| Microsoft.Automation/automationAccounts/jobs/read | Resursgrupp |
| Microsoft.OperationsManagement/solutions/write | Resursgrupp |
| Microsoft.OperationalInsights/workspaces/* | Resursgrupp |
| Microsoft.Insights/diagnosticSettings/write | Resursgrupp |
| Microsoft.Insights/ActionGroups/Write | Resursgrupp |
| Microsoft.Insights/ActionGroups/read | Resursgrupp |
| Microsoft.Resources/subscriptions/resourceGroups/read | Resursgrupp |
| Microsoft.Resources/deployments/* | Resursgrupp |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nytt Automation-konto och en ny Log Analytics-arbetsyta

Om du vill distribuera Starta/stoppa virtuella datorer under arbetstid, måste lösningen till en ny Automation-kontot och Log Analytics-arbetsyta användaren-lösningen distribueras de behörigheter som definierats i föregående avsnitt samt följande behörigheter:

- Delad administratör på prenumerationen – det här krävs bara att skapa det klassiska kör som-konto
- Vara en del av den [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **programutvecklare** roll. Mer information om hur du konfigurerar kör som-konton finns i [behörigheter för att konfigurera kör som-konton](manage-runas-account.md#permissions).
- Deltagare för prenumerationen eller följande behörigheter.

| Behörighet |Scope|
| --- | --- |
| Microsoft.Authorization/Operations/read | Prenumeration|
| Microsoft.Authorization/permissions/read |Prenumeration|
| Microsoft.Authorization/roleAssignments/read | Prenumeration |
| Microsoft.Authorization/roleAssignments/write | Prenumeration |
| Microsoft.Authorization/roleAssignments/delete | Prenumeration |
| Microsoft.Automation/automationAccounts/connections/read | Resursgrupp |
| Microsoft.Automation/automationAccounts/certificates/read | Resursgrupp |
| Microsoft.Automation/automationAccounts/write | Resursgrupp |
| Microsoft.OperationalInsights/workspaces/write | Resursgrupp |

## <a name="deploy-the-solution"></a>Distribuera lösningen

Utför följande steg för att lägga till Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning i Automation-konto och sedan konfigurera variabler för att anpassa lösningen.

1. Välj ett Automation-konto **Starta/Stoppa VM** under **relaterade resurser**. Härifrån kan du klicka på **Läs mer om att aktivera lösningen**. Om du redan har en Starta/Stoppa VM-lösning som har distribuerats kan du välja den genom att klicka på **hantera lösningen** och söka efter dem i listan.

   ![Aktivera från automation-konto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Du kan också skapa den var som helst i Azure-portalen genom att klicka på **skapa en resurs**. Marketplace-sidan skriver du ett nyckelord som **starta** eller **Starta/Stoppa**. När du börjar skriva filtreras listan baserat på det du skriver. Du kan också Skriv en eller flera nyckelord från det fullständiga namnet på lösningen och tryck sedan på RETUR. Välj **Starta/Stoppa VM under kontorstid** från sökresultaten.

2. I den **Starta/Stoppa VM under kontorstid** för den valda lösningen, granska informationen i sammanfattningen och klicka sedan på **skapa**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. Den **lägga till lösning** visas. Du uppmanas att konfigurera lösningen innan du kan importera den till din Automation-prenumeration.

   ![Lösning för virtuell dator lägger du till sidan](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. På den **lägga till lösning** väljer **arbetsytan**. Välj en Log Analytics-arbetsyta som är länkad till samma Azure-prenumeration som Automation-kontot. Om du inte har en arbetsyta, väljer **Skapa ny arbetsyta**. På den **Log Analytics-arbetsyta** utför följande steg:
   - Ange ett namn för den nya **Log Analytics-arbetsyta**, till exempel ”ContosoLAWorkspace”.
   - Välj en **prenumeration** att länka till genom att välja från listrutan om standardvalet inte är lämpligt.
   - För **resursgrupp**, du kan skapa en ny resursgrupp eller välj en befintlig.
   - Välj en **Plats**. För närvarande endast tillgängliga regionerna är **Australien, sydöstra**, **centrala**, **centrala Indien**, **USA, östra**, **Östra japan**, **Sydostasien**, **Storbritannien, södra**, **Västeuropa**, och **USA, västra 2**.
   - Välj en **Prisnivå**. Välj den **Per GB (fristående)** alternativet. Azure Monitor-loggar har uppdaterat [priser](https://azure.microsoft.com/pricing/details/log-analytics/) och Per GB-nivån är det enda alternativet.

   > [!NOTE]
   > När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
   >
   > En lista över stöds mappningspar finns i [regionsmappning för Automation-kontot och Log Analytics-arbetsytan](how-to/region-mappings.md).

5. När du har angett informationen som krävs på den **Log Analytics-arbetsyta** klickar du på **skapa**. Du kan spåra förloppet under **meddelanden** från menyn som tillbaka till den **lägga till lösning** sidan när du är klar.
6. På den **lägga till lösning** väljer **Automation-konto**. Om du skapar en ny Log Analytics-arbetsyta kan du skapa ett nytt Automation-konto som ska associeras med den eller välja ett befintligt Automation-konto som inte är redan länkad till en Log Analytics-arbetsyta. Välj ett befintligt Automation-konto eller klicka på **skapa ett Automation-konto**, och på den **Lägg till Automation-konto** anger du följande information:
   - I fältet **namn** anger du namnet på Automation-kontot.

     Alla andra alternativ fylls i automatiskt baserat på Log Analytics-arbetsyta har valts. Dessa alternativ kan inte ändras. Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen. När du klickar på **OK**, verifieras konfigurationsalternativen och Automation-kontot har skapats. Du kan spåra förloppet under **Meddelanden** på menyn.

7. Slutligen på den **lägga till lösning** väljer **Configuration**. Den **parametrar** visas.

   ![Sidan parametrar för lösningen](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Här kan uppmanas du att:
   - Ange den **rikta ResourceGroup namn**. Dessa värden är Resursgruppsnamn som innehåller virtuella datorer som hanteras av den här lösningen. Du kan ange flera namn och skilja dem åt med semikolon (värden inte är skiftlägeskänsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Det här värdet lagras i den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler.
   - Ange den **VM uteslutningslista (sträng)** . Det här värdet är namnet på en eller flera virtuella datorer från målresursgruppen. Du kan ange flera namn och skilja dem åt med semikolon (värden inte är skiftlägeskänsliga). Användning av jokertecken stöds. Det här värdet lagras i den **External_ExcludeVMNames** variabeln.
   - Välj en **schema**. Det här värdet är ett återkommande datum och tid för att starta och stoppa virtuella datorer i target-resursgrupper. Som standard konfigureras schemat i 30 minuter från nu. Det går inte att välja en annan region. Om du vill konfigurera schemat för din specifika tidszon när du har konfigurerat lösningen, se [ändra schemat för start och avstängning](#modify-the-startup-and-shutdown-schedules).
   - Att ta emot **e-postmeddelanden** från en åtgärdsgrupp godkänner du standardvärdet för **Ja** och ange en giltig e-postadress. Om du väljer **nr** men besluta vid ett senare tillfälle att du vill ta emot e-postmeddelanden, kan du uppdatera den [åtgärdsgrupp](../azure-monitor/platform/action-groups.md) som skapas med giltiga e-postadresser avgränsade med kommatecken. Du måste också aktivera följande regler för avisering:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Standardvärdet för **Målresursnamn** är en **&ast;** . Detta riktar sig mot alla virtuella datorer i en prenumeration. Om du inte vill att lösningen att fokusera på alla virtuella datorer i din prenumeration som det här värdet behöver uppdateras till en lista över resursgruppnamn innan du kan aktivera scheman.

8. När du har konfigurerat de ursprungliga inställningarna som krävs för lösningen, klickar du på **OK** att Stäng den **parametrar** och välj **skapa**. När alla inställningar verifieras har lösningen distribuerats till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **meddelanden** på menyn.

> [!NOTE]
> Om du har en prenumeration på Azure Cloud Solution Provider (Azure CSP), när distributionen är klar, i ditt Automation-konto kan du gå till **variabler** under **delade resurser** och ange den [ **External_EnableClassicVMs** ](#variables) variabeln **FALSKT**. Detta stoppar lösningen från letar du efter resurser för klassiska VM.

## <a name="scenarios"></a>Scenarier

Lösningen innehåller tre olika scenarier. De här scenarierna är:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: Starta/stoppa virtuella datorer enligt ett schema

Det här scenariot är standardkonfigurationen för första gången du distribuerar lösningen. Du kan till exempel konfigurera den för att stoppa alla virtuella datorer i hela prenumerationen när du stänger arbete på kvällen och starta dem på morgonen när du är på kontoret. När du konfigurerar scheman **schemalagd StartVM** och **schemalagd StopVM** under distributionen av de startar och stoppa riktade virtuella datorer. Konfigurera den här lösningen för att bara stoppa virtuella datorer stöds, se [ändra scheman för start och avstängning](#modify-the-startup-and-shutdown-schedules) att lära dig hur du konfigurerar ett anpassat schema.

> [!NOTE]
> Tidszonen är din aktuella tidszon när du anger parametern schema time. Men är den lagrad i UTC-format i Azure Automation. Du behöver inte göra några tidszonskonverteringen som hanteras under distributionen.

Du styra vilka virtuella datorer finns i omfattningen genom att konfigurera följande variabler: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, och **External_ExcludeVMNames**.

Du kan aktivera inriktning på åtgärd mot en prenumeration och resursgrupp eller riktar in sig på en viss lista med virtuella datorer, men inte båda.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start och stopp åtgärder mot en grupp för prenumeration och resursgrupp

1. Konfigurera den **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange de virtuella måldatorerna.
2. Aktivera och uppdatera den **schemalagd StartVM** och **schemalagd StopVM** scheman.
3. Kör den **ScheduledStartStop_Parent** runbook med parametern åtgärd inställd **starta** och parametern WHATIF inställd **SANT** att förhandsgranska dina ändringar.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta instruktionen start och stopp av VM-lista

1. Kör den **ScheduledStartStop_Parent** runbook med parametern åtgärd inställd **starta**, Lägg till en kommaavgränsad lista över virtuella datorer i den *VMList* parameter och ange sedan den WHATIF parameter **SANT**. Förhandsgranska dina ändringar.
1. Konfigurera den **External_ExcludeVMNames** parameter med en kommaavgränsad lista över virtuella datorer (VM1, VM2 VM3).
1. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. Det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Värdet för **Målresursnamn** lagras som värde för båda **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames**. För mer detaljrikedom, kan du ändra var och en av dessa variabler och ange olika resursgrupper. Startåtgärden, använda **External_Start_ResourceGroupNames**, och stoppåtgärder, använda **External_Stop_ResourceGroupNames**. Virtuella datorer läggs automatiskt till i början och stoppa scheman.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: Starta/stoppa virtuella datorer i ordning med hjälp av taggar

I en miljö med minst två eller flera komponenter på flera virtuella datorer som stöd för en distribuerad arbetsbelastning, är det viktigt att stödja sekvensen där komponenter startas och stoppas i ordning. Du kan göra det här scenariot genom att utföra följande steg:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start och stopp åtgärder mot en grupp för prenumeration och resursgrupp

1. Lägg till en **sequencestart** och en **sequencestop** taggen med ett positivt heltalsvärde för virtuella datorer som omfattas i **External_Start_ResourceGroupNames** och  **External_Stop_ResourceGroupNames** variabler. Starta och stoppa åtgärder utförs i stigande ordning. Läs hur du tagga en virtuell dator i [tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en virtuell Linux-dator i Azure](../virtual-machines/linux/tag.md).
1. Ändra scheman **Sequenced StartVM** och **Sequenced StopVM** datum och tid som uppfyller dina krav och aktivera schemat.
1. Kör den **SequencedStartStop_Parent** runbook med parametern åtgärd inställd **starta** och parametern WHATIF inställd **SANT** att förhandsgranska dina ändringar.
1. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot virtuella produktionsdatorer. När klar, manuellt köra runbook med parametern inställd **FALSKT**, eller låt Automation schemat **Sequenced StartVM** och **Sequenced StopVM** kör automatiskt efter din ett förutbestämt schema.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta instruktionen start och stopp av VM-lista

1. Lägg till en **sequencestart** och en **sequencestop** taggen med ett positivt heltalsvärde till virtuella datorer som du planerar att lägga till den **VMList** parametern.
1. Kör den **SequencedStartStop_Parent** runbook med parametern åtgärd inställd **starta**, Lägg till en kommaavgränsad lista över virtuella datorer i den *VMList* parameter och ange sedan den WHATIF parameter **SANT**. Förhandsgranska dina ändringar.
1. Konfigurera den **External_ExcludeVMNames** parameter med en kommaavgränsad lista över virtuella datorer (VM1, VM2 VM3).
1. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. Det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).
1. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot virtuella produktionsdatorer. När klar, manuellt köra i övervakning-och-diagnostik/övervakning-åtgärd-groupsrunbook med parametern inställd **FALSKT**, eller låt Automation schemat **Sequenced StartVM** och **Sequenced StopVM** kör automatiskt efter din ett förutbestämt schema.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenario 3: Starta/Stoppa automatiskt baserat på CPU-användning

Den här lösningen hjälper dig att hantera kostnaden för att köra virtuella datorer i din prenumeration genom att utvärdera Azure virtuella datorer som inte används under perioder med låg belastning, till exempel efter timmar och automatiskt stänger av dem om processorbelastningen är mindre än x %.

Som standard är lösningen förinställd på att utvärdera procent CPU-mått för att se om genomsnittsanvändningen är 5 procent eller mindre. Det här scenariot styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Du kan aktivera inriktning på åtgärd mot en prenumeration och resursgrupp eller riktar in sig på en viss lista med virtuella datorer, men inte båda.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Rikta stop-åtgärd mot en grupp för prenumeration och resursgrupp

1. Konfigurera den **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange de virtuella måldatorerna.
1. Aktivera och uppdatera den **Schedule_AutoStop_CreateAlert_Parent** schema.
1. Kör den **AutoStop_CreateAlert_Parent** runbook med parametern åtgärd inställd **starta** och parametern WHATIF inställd **SANT** att förhandsgranska dina ändringar.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta instruktionen start och stopp av VM-lista

1. Kör den **AutoStop_CreateAlert_Parent** runbook med parametern åtgärd inställd **starta**, Lägg till en kommaavgränsad lista över virtuella datorer i den *VMList* parameter och ange sedan den WHATIF parameter **SANT**. Förhandsgranska dina ändringar.
1. Konfigurera den **External_ExcludeVMNames** parameter med en kommaavgränsad lista över virtuella datorer (VM1, VM2 VM3).
1. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. Det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).

Nu när du har ett schema för att stoppa virtuella datorer baserat på CPU-belastning som du behöver aktivera något av följande scheman startas om.

- Target startåtgärd av prenumeration och resursgrupp. Hittar du i [Scenario 1](#scenario-1-startstop-vms-on-a-schedule) för testning och aktiverar **schemalagd StartVM** scheman.
- Target startåtgärd av prenumeration, resursgrupp och tagg. Hittar du i [Scenario 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) för testning och aktiverar **Sequenced StartVM** scheman.

## <a name="solution-components"></a>Lösningskomponenter

Den här lösningen innehåller förkonfigurerade runbooks, scheman och integrering med Azure Monitor-loggar så att du kan anpassa Start och stopp av dina virtuella datorer som passar dina affärsbehov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de runbooks som distribueras till ditt Automation-konto av den här lösningen. Gör ändringar i runbook-koden. I stället skriva egna runbooks för nya funktioner.

> [!IMPORTANT]
> Inte direkt köra valfri runbook med ”underordnad” i slutet av namnet.

Alla överordnade runbooks är den _WhatIf_ parametern. När värdet **SANT**, _WhatIf_ stöder med information om beteendet exakta runbook ska vidta när kör utan den _WhatIf_ parametern och för att kontrollera rätt virtuella datorer som ska mål. Utför dess definierade åtgärder endast i en runbook när den _WhatIf_ parametern är inställd på **FALSKT**.

|Runbook | Parametrar | Beskrivning|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropa från den överordnade runbooken. Denna runbook skapar aviseringar på basis av per resurs för AutoStop scenariot.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: SANT eller FALSKT  | Skapar eller uppdaterar Azure Varningsregler på virtuella datorer i målgrupperna för prenumeration eller resursgrupp. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2 vm3_.<br> *WhatIf* verifierar runbook-logik utan att köra.|
|AutoStop_Disable | Ingen | Inaktiverar AutoStop aviseringar och standardschemat.|
|AutoStop_StopVM_Child | WebHookData | Anropa från den överordnade runbooken. Varningsregler anropa denna runbook för att stoppa den virtuella datorn.|
|Bootstrap_Main | Ingen | Används en gång för att ställa in startkonfigurationer, till exempel webhookURI, som normalt inte nås från Azure Resource Manager. Denna runbook tas bort automatiskt vid distributionen.|
|ScheduledStartStop_Child | VMName <br> Åtgärd: Starta eller stoppa <br> ResourceGroupName | Anropa från den överordnade runbooken. Kör en starta eller stoppa åtgärd för schemalagda Stopp.|
|ScheduledStartStop_Parent | Åtgärd: Starta eller stoppa <br>VMList <br> WhatIf: SANT eller FALSKT | Den här inställningen påverkar alla virtuella datorer i prenumerationen. Redigera den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** ska bara köras på dessa mål resursgrupper. Du kan också utesluta specifika virtuella datorer genom att uppdatera den **External_ExcludeVMNames** variabeln.<br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2 vm3_.<br> _WhatIf_ verifierar runbook-logik utan att köra.|
|SequencedStartStop_Parent | Åtgärd: Starta eller stoppa <br> WhatIf: SANT eller FALSKT<br>VMList| Skapa taggar med namnet **sequencestart** och **sequencestop** på varje virtuell dator som du vill att aktivitetssekvensen Starta/Stoppa aktiviteten. Dessa taggnamn är skiftlägeskänsliga. Taggens värde ska vara ett positivt heltal (1, 2, 3) som motsvarar den ordning som du vill starta eller stoppa. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel _vm1, vm2 vm3_. <br> _WhatIf_ verifierar runbook-logik utan att köra. <br> **Obs!** Virtuella datorer måste vara inom resursgrupper som har definierats som External_Start_ResourceGroupNames och External_Stop_ResourceGroupNames External_ExcludeVMNames i Azure Automation-variabler. De måste ha lämpliga taggar för åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas de variabler som skapats i ditt Automation-konto. Endast ändra variabler som föregås av prefixet **externa**. Ändra variablerna prefixet **internt** orsakar oönskade effekter.

|Variabel | Beskrivning|
|---------|------------|
|External_AutoStop_Condition | Villkorsstyrd operator som krävs för att konfigurera villkoret innan du utlöser en avisering. Godkända värden är **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, och **LessThanOrEqual**.|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om CPU-procent är över tröskeln.|
|External_AutoStop_MetricName | Namnet på prestandamått som är Azure varningsregeln konfigureras.|
|External_AutoStop_Threshold | Tröskelvärdet för Azure varningsregeln har angett i variabeln _External_AutoStop_MetricName_. Procentandel värdena kan variera mellan 1 och 100.|
|External_AutoStop_TimeAggregationOperator | Operatorn tid-aggregering, som tillämpas på den valda fönsterstorleken för att utvärdera villkoret. Godkända värden är **genomsnittliga**, **minsta**, **maximala**, **totala**, och **senaste**.|
|External_AutoStop_TimeWindow | Fönsterstorleken då Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar indata i timespan-format. Möjliga värden är från 5 minuter till 6 timmar.|
|External_EnableClassicVMs| Anger om klassiska virtuella datorer som omfattas av lösningen. Standardvärdet är sant. Detta bör anges till falskt för CSP-prenumerationer.|
|External_ExcludeVMNames | Ange namn på virtuella datorer som ska undantas, att avgränsa namnen med kommatecken utan blanksteg. Det här är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer till den här kommaseparerad lista med kan virtuella datorer som är inställda som ska undantas vara oavsiktligt startas eller stoppas.|
|External_Start_ResourceGroupNames | Anger en eller flera resursgrupper, att avgränsa värden med hjälp av ett kommatecken, avsedda för start-åtgärder.|
|External_Stop_ResourceGroupNames | Anger en eller flera resursgrupper, att avgränsa värden med hjälp av ett kommatecken, avsedda för stop-åtgärder.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_WebhookUri | Anger Webhook URI kallas för AutoStop scenariot.|
|Internal_AzureSubscriptionId | Anger Azure prenumerations-ID.|
|Internal_ResourceGroupName | Anger Automation-konto resursgruppens namn.|

I samtliga scenarier den **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, och **External_ExcludeVMNames** variabler krävs för virtuella datorer, med undantag för att tillhandahålla en kommaavgränsad lista över virtuella datorer för den **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, och  **ScheduledStartStop_Parent** runbooks. Det vill säga måste dina virtuella datorer finnas i target-resursgrupper för start och stoppa åtgärder ska utföras. Den logik fungerar liknande Azure policy, eftersom du kan fokusera på den prenumeration eller resursgrupp enhetsgrupp och åtgärder som ärvs av nyligen skapade virtuella datorer. Den här metoden innebär att du slipper upprätthålla ett separat schema för varje virtuell dator och hantera startar och stoppar i skala.

### <a name="schedules"></a>Scheman

I följande tabell visas var och en av standard-scheman som skapats i ditt Automation-konto. Du kan ändra dem eller skapa egna anpassade scheman. Som standard alla scheman inaktiveras undantag för **Scheduled_StartVM** och **Scheduled_StopVM**.

Du bör inte aktivera alla scheman, eftersom detta kan orsaka överlappande Schemaläggningsåtgärder. Det är bäst att avgöra vilka optimeringar som du vill utföra och ändra. Se i exempelscenarierna i översiktsavsnittet för ytterligare förklaring.

|Namn på schema | Frekvens | Beskrivning|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var åttonde timme | Kör AutoStop_CreateAlert_Parent runbook var åttonde timme, vilket i sin tur avbryter VM-baserade värdena i External_Start_ResourceGroupNames och External_Stop_ResourceGroupNames External_ExcludeVMNames i Azure Automation-variabler. Du kan också ange en kommaavgränsad lista över virtuella datorer med hjälp av parametern VMList.|
|Scheduled_StopVM | Användardefinierade, varje dag | Kör Scheduled_Parent runbook med en parameter av _stoppa_ varje dag vid den angivna tidpunkten. Stoppas automatiskt alla virtuella datorer som uppfyller de regler som definieras av tillgången variabler. Aktivera relaterade schema **schemalagd StartVM**.|
|Scheduled_StartVM | Användardefinierade, varje dag | Kör Scheduled_Parent runbook med en parameter av _starta_ varje dag vid den angivna tidpunkten. Startar automatiskt alla virtuella datorer som uppfyller de regler som definieras av lämplig variablerna. Aktivera relaterade schema **schemalagd StopVM**.|
|Sequenced-StopVM | 01:00:00 (UTC), varje fredag | Kör Sequenced_Parent runbook med en parameter av _stoppa_ varje fredag vid den angivna tidpunkten. Sekventiellt (stigande) stoppas alla virtuella datorer med en tagg för **SequenceStop** definieras av lämplig variablerna. Mer information om taggvärden och tillgången variabler finns i avsnittet om Runbooks. Aktivera relaterade schema **Sequenced StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC) varje måndag | Kör Sequenced_Parent runbook med en parameter av _starta_ varje måndag vid den angivna tidpunkten. Sekventiellt (fallande) börjar alla virtuella datorer med en tagg för **SequenceStart** definieras av lämplig variablerna. Mer information om taggvärden och tillgången variabler finns i avsnittet om Runbooks. Aktivera relaterade schema **Sequenced StopVM**.|

## <a name="azure-monitor-logs-records"></a>Azure Monitor loggar poster

Automation skapar två typer av poster i Log Analytics-arbetsyta: jobb-loggar och jobbets strömmar.

### <a name="job-logs"></a>Jobbloggar

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Category | Klassificering av typ av data. För Automation är värdet JobLogs.|
|CorrelationId | GUID som är Korrelations-ID för runbook-jobbet.|
|JobId | GUID som är ID för runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet Job.|
|resourceId | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Status för runbookjobbet. Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
|resultDescription | Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager|
|StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
|SubscriptionId | Anger prenumerations-ID för jobbet.
|Tid | Datum och tid då runbook-jobbet körs.|

### <a name="job-streams"></a>Arbetsflöden

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Category | Klassificering av typ av data. För Automation är värdet JobStreams.|
|JobId | GUID som är ID för runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet Job.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|resourceId | Anger resurs-ID i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Resultatet av runbook-jobbet då händelsen skapades. Ett möjligt värde är:<br>- Ärendeposter|
|resultDescription | Innehåller utdataströmmen från runbook.|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager.|
|StreamType | Typ av jobbström. Möjliga värden:<br>-Förlopp<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
|Tid | Datum och tid då runbook-jobbet körs.|

När du utför en loggsökning som returnerar poster kategori av **JobLogs** eller **JobStreams**, kan du välja den **JobLogs** eller **JobStreams**vy som visar en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen.

|Fråga | Beskrivning|
|----------|----------|
|Hitta jobb för runbook ScheduledStartStop_Parent att har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för runbook SequencedStartStop_Parent att har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Visa lösningen

För att komma åt lösningen går du till ditt Automation-konto, Välj **arbetsytan** under **relaterade resurser**. På sidan log analytics väljer **lösningar** under **Allmänt**. På den **lösningar** väljer lösningen **Start stoppa VM [workspace]** i listan.

Om du väljer lösningen visas den **Start stoppa VM [workspace]** lösning sidan. Här kan du granska viktig information som den **StartStopVM** panelen. Den här panelen visar ett antal och en grafisk representation av runbook-jobb för lösningen som har startat och har slutförts som i din Log Analytics-arbetsyta.

![Automation uppdateringshantering lösning sidan](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Härifrån kan kan du utföra ytterligare analys av jobbposterna genom att klicka på panelen ringdiagram. Lösningens instrumentpanel Visar historik över och fördefinierade loggsökningsfrågor. Växla till log analytics avancerad portal för att söka baserat på dina sökfrågor.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Om du vill ändra e-postaviseringar när lösningen har distribuerats, ändra åtgärdsgrupp som skapades under distributionen.  

> [!NOTE]
> Prenumerationer i Azure Government-molnet stöder inte e-postfunktionen för den här lösningen.

I Azure-portalen går du till övervakaren -> åtgärdsgrupper. Välj åtgärdsgrupp som heter **StartStop_VM_Notication**.

![Automation uppdateringshantering lösning sidan](media/automation-solution-vm-management/azure-monitor.png)

På den **StartStop_VM_Notification** klickar du på **redigera information** under **information**. Då öppnas det **e-post/SMS/Push/röst** sidan. Uppdatera e-postadress och klicka på **OK** att spara dina ändringar.

![Automation uppdateringshantering lösning sidan](media/automation-solution-vm-management/change-email.png)

Alternativt kan du lägga till fler åtgärder för åtgärdsgruppen vill veta mer om åtgärdsgrupper, se [åtgärdsgrupper](../azure-monitor/platform/action-groups.md)

Följande är ett exempel e-postmeddelande som skickas när lösningen stängs av virtuella datorer.

![Automation uppdateringshantering lösning sidan](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Lägg till eller undanta virtuella datorer

Lösningen ger möjlighet att lägga till virtuella datorer för att vara mål för lösningen eller uttryckligen utelämna datorer från lösningen.

### <a name="add-a-vm"></a>Lägg till en virtuell dator

Det finns ett par alternativ som du kan använda för att se till att en virtuell dator ingår i lösningen Starta/Stoppa när den körs.

* Var och en av överordnat [runbooks](#runbooks) av lösningen har en **VMList** parametern. Du kan skicka en kommaavgränsad lista över namn på virtuella datorer till den här parametern när schemaläggning av lämplig överordnad runbook för din situation och dessa virtuella datorer kommer att inkluderas när lösningen körs.

* Om du vill välja flera virtuella datorer, ange den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** med namnen på resursen som innehåller de virtuella datorerna som du vill starta eller stoppa. Du kan också ange ett värde `*`, har den lösning som körs mot alla resursgrupper i prenumerationen.

### <a name="exclude-a-vm"></a>Undanta en virtuell dator

Om du vill exkludera en virtuell dator från lösningen kan du lägga till den till den **External_ExcludeVMNames** variabeln. Den här variabeln är en kommaavgränsad lista med specifika virtuella datorer ska undantas från lösningen Starta/Stoppa. Den här listan är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer till den här kommaseparerad lista med kan virtuella datorer som är inställda som ska undantas vara oavsiktligt startas eller stoppas.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra scheman för start och avstängning

Hantera scheman för start och avstängning i den här lösningen följer samma steg som beskrivs i [schemaläggning av en runbook i Azure Automation](automation-schedules.md). Det måste finnas en separat schema för att starta och stoppa virtuella datorer.

Konfigurera lösningen så att bara stoppa virtuella datorer vid en viss tidpunkt stöds. I det här scenariot du helt enkelt skapa en **stoppa** schema och ingen motsvarande **starta** schemalagda. Om du vill göra det måste du:

1. Se till att du har lagt till resursgrupper för de virtuella datorerna ska stängas av om den **External_Stop_ResourceGroupNames** variabeln.
2. Skapa ett eget schema för den tid som du vill stänga av de virtuella datorerna.
3. Navigera till den **ScheduledStartStop_Parent** runbook och klicka på **schema**. På så sätt kan du välja det schema som du skapade i föregående steg.
4. Välj **parametrar och körinställningar** och ange parametern åtgärd för att ”stoppa”.
5. Spara ändringarna genom att klicka på **OK**.

## <a name="update-the-solution"></a>Uppdatera lösningen

Om du har distribuerat en tidigare version av den här lösningen kan du måste först ta bort den från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-solution) och följ sedan stegen ovan för att [distribuera lösningen](#deploy-the-solution).

## <a name="remove-the-solution"></a>Ta bort lösningen

Om du inte längre behöver använda lösningen kan du ta bort den från Automation-konto. Tar bort lösningen endast tar bort runbooks. Scheman eller variabler som skapades när lösningen har lagts till tas inte bort. Resurserna som du måste ta bort manuellt om du inte använder dem med andra runbooks.

Utför följande steg för att ta bort lösningen:

1. Från ditt Automation-konto under **relaterade resurser**väljer **länkade arbetsytan**.
1. Välj **går du till arbetsytan**.
1. Under **Allmänt**väljer **lösningar**. 
1. På den **lösningar** väljer lösningen **Start stoppa VM [Workspace]** . På den **VMManagementSolution [Workspace]** sida från menyn och välj **ta bort**.<br><br> ![Ta bort VM-Mgmt-lösning](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. I den **ta bort lösningen** och bekräfta att du vill ta bort lösningen.
1. Även om informationen har verifierats och lösningen har tagits bort, du kan spåra förloppet under **meddelanden** på menyn. Du kommer tillbaka till den **lösningar** sidan när processen för att ta bort lösningen har startat.

Automation-kontot och Log Analytics-arbetsyta raderas inte som en del av den här processen. Om du inte vill behålla Log Analytics-arbetsytan måste du ta bort den manuellt. Detta kan åstadkommas från Azure portal:

1. Azure hemskärmen i portalen, väljer du **Log Analytics-arbetsytor**.
1. På den **Log Analytics-arbetsytor** väljer arbetsytan.
1. Välj **ta bort** från menyn på inställningssidan för arbetsytan.

Om du inte vill behålla komponenterna för Azure Automation-konto du manuellt ta bort var och en. Lista med runbooks, variabler och scheman som skapats av lösningen finns i den [lösningskomponenter](#solution-components).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du konstruerar olika sökfrågor och granskar jobbloggarna i Automation med Azure Monitor-loggar finns [Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md).
- Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
- Läs mer om Azure Monitor-loggar och datakällsamling i [insamling av Azure storage-data i Azure Monitor loggar översikt](../azure-monitor/platform/collect-azure-metrics-logs.md).
