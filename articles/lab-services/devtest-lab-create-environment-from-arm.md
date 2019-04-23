---
title: Skapa miljöer för flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar | Microsoft Docs
description: Lär dig att skapa miljöer för flera virtuella datorer och PaaS-resurser i Azure DevTest Labs från en Azure Resource Manager-mall
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: 96e3a24b0c9f9ab21652ffcd1b29deeb512581e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796941"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Skapa miljöer för flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar

Den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040) kan du enkelt [lägga till en virtuell dator i taget till ett labb](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Men om miljön innehåller flera virtuella datorer kan måste varje virtuell dator individuellt skapas. För scenarier, till exempel en webbapp för flera nivåer eller en SharePoint-servergrupp krävs en mekanism för att tillåta för att skapa flera virtuella datorer i ett enda steg. Med hjälp av Azure Resource Manager-mallar kan du nu definierar infrastruktur och konfiguration av din Azure-lösning och upprepade gånger distribuera flera virtuella datorer i ett konsekvent tillstånd. Den här funktionen ger följande fördelar:

- Azure Resource Manager-mallar har lästs in direkt från centrallagret för källkontroll (GitHub eller Azure DevOps Services Git).
- När du konfigurerat dina användare kan skapa en miljö genom att välja en Azure Resource Manager-mall från Azure portal, precis som med andra typer av [VM-databaser](./devtest-lab-comparing-vm-base-image-types.md).
- Azure PaaS-resurser kan etableras i en miljö från en Azure Resource Manager-mall förutom virtuella IaaS-datorer.
- Kostnaden för miljöer kan spåras i labbet, utöver enskilda virtuella datorer som skapats av andra typer av baser.
- PaaS-resurser skapas och visas i Kostnadsuppföljning; dock gäller inte automatisk avstängningen till PaaS-resurser.

Mer information om många [fördelarna med att använda Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) för att distribuera, uppdatera eller ta bort alla dina labbresurser i en enda åtgärd.

> [!NOTE]
> När du använder Resource Manager-mall som utgångspunkt för att skapa mer labb virtuella datorer, finns det några skillnader att tänka på om du skapar flera virtuella datorer eller enskild för virtuella datorer. [Använda Azure Resource Manager-mall för en virtuell dators](devtest-lab-use-resource-manager-template.md) förklarar skillnaderna i större detalj.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="devtest-labs-public-environments"></a>DevTest Labs offentliga miljöer
Azure DevTest Labs har en [offentliga lagringsplatsen för Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att behöva ansluta till en extern källa för GitHub själv. Den här lagringsplatsen innehåller ofta använda mallar som Azure Web Apps, Service Fabric-kluster och utveckling SharePoint-servergruppen miljö. Den här funktionen liknar den offentliga databasen artefakter som ingår för varje labb som du skapar. Miljö-databasen kan du snabbt komma igång med färdiga miljömallar med minsta indataparametrar för att ge dig en smidig upplevelse för komma igång för PaaS-resurser inom labs. Mer information finns i [konfigurera och använda offentliga miljöer i DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Konfigurera en egen mall-databaser
Som en av de bästa metoderna med infrastruktur som kod och konfiguration som kod, ska miljömallar hanteras i källkontrollen. Azure DevTest Labs följer den här övningen och läser in alla Azure Resource Manager-mallar direkt från dina GitHub- eller Azure DevOps Services Git-lagringsplatser. Resource Manager-mallar kan därför användas över hela lanseringen-livscykel, från testmiljö till produktionsmiljön.

Se vilka mallar som skapats av DevTest Labs-teamet i den [offentliga GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Environments). I den här offentliga databasen, kan du visa mallar som delas av andra att du kan använda direkt eller anpassa dem efter dina behov. När du har skapat mallen kan du lagra den i den här lagringsplatsen att dela den med andra. Du kan också ställa in din egen Git-lagringsplats med mallar som kan användas för att konfigurera miljöer i molnet. 

Det finns några regler för att följa för att organisera dina Azure Resource Manager-mallar i en databas:

- Master mallfilen måste ha namnet `azuredeploy.json`. 

    ![Nyckeln Azure Resource Manager-mallfiler](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Om du vill använda parametervärden som definierats i en parameterfil parameterfilen måste ha namnet `azuredeploy.parameters.json`.
- Du kan använda parametrarna `_artifactsLocation` och `_artifactsLocationSasToken` så att DevTest Labs för automatisk hantering av kapslade mallar för att konstruera parametersLink URI-värdet. Mer information finns i [hur Azure DevTest Labs underlättar kapslade resurshanteraren malldistributioner för testmiljöer](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Metadata kan definieras för att ange Mallens visningsnamn och beskrivning. Dessa metadata måste finnas i en fil med namnet `metadata.json`. Metadatafilen följande exempel visar hur du kan ange namn och beskrivning: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

Följande steg beskriver hur du lägger till en lagringsplats till ditt labb med hjälp av Azure portal. 

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. Listan över labbar, Välj önskade labbet.   
1. På testmiljön **översikt** väljer **konfiguration och principer**.

    ![Konfiguration och principer](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Från den **konfiguration och principer** inställningslista, väljer **databaser**. Den **databaser** fönstret visar en lista över databaser som har lagts till labbet. En databas med namnet `Public Repo` genereras automatiskt för alla labb och ansluter till den [DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab) som innehåller flera VM-artefakter för din användning.

    ![Offentlig repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Välj **Lägg till +** att lägga till din lagringsplats för Azure Resource Manager-mall.
1. När andra **databaser** öppnas, ange nödvändig information på följande sätt:
    - **Namn på** – ange namnet på lagringsplatsen som används i laboratoriet.
    - **URL för Git-klonen** – ange URL: en för GIT HTTPS klonade från GitHub eller Azure DevOps-tjänsterna.  
    - **Gren** -ange grennamnet att komma åt din Malldefinitioner för Azure Resource Manager-. 
    - **Personlig åtkomsttoken** -personliga åtkomsttoken som används för att få säker åtkomst till databasen. Välj för att få din token från Azure DevOps-tjänsterna,  **&lt;dittnamn >> min profil > Säkerhet > offentlig åtkomst-token**. Välj din avatar följt genom att välja för att få din token från GitHub, **Inställningar > offentlig åtkomst-token**. 
    - **Mappsökvägar** -med någon av de två inmatningsfält, ange sökvägen som börjar med ett snedstreck - / - och är i förhållande till din Git-klon URI till antingen artefakt definitionerna (första inmatningsfält) eller definitionerna för Azure Resource Manager-mall .   
    
        ![Offentlig repo](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. När alla obligatoriska fält har angetts och skicka verifieringen, välja **spara**.

Nästa avsnitt vägleder dig genom att skapa miljöer från en Azure Resource Manager-mall.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Skapa en miljö från en Resource Manager-mall med Azure portal

När en lagringsplats för Azure Resource Manager-mallen har konfigurerats i labbet, kan labbanvändare skapa en miljö med Azure-portalen med följande steg:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. Listan över labbar, Välj önskade labbet.   
1. I den testmiljön rutan, väljer **Lägg till +**.
1. Den **väljer bas** fönstret visar Källavbildningen du kan använda med Azure Resource Manager-mallar som visas först. Välj den önskade Azure Resource Manager-mallen.

    ![Välja en bas](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. På den **Lägg till** fönstret, ange den **miljönamn** värde. Miljönamnet är det som visas för användarna i laboratoriet. Återstående indatafält har definierats i Azure Resource Manager-mallen. Om standardvärden har definierats i mallen eller `azuredeploy.parameter.json` filen finns, standardvärden visas i dessa indatafält. För parametrar av typen *säker sträng*, du kan använda hemligheterna som lagras i Azure key vault. Läs om att spara hemligheter i key vault och använder dem när du skapar labbresurser i [Store hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

    ![Fönstret Lägg till](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Det finns flera parametervärden som – även om angetts - visas som tomma värden. Därför om användare tilldelar dessa värden till parametrar i en Azure Resource Manager-mall, visar labb inte värdena. I stället visas tom inmatningsfält där labbanvändare måste ange ett värde när du skapar miljön.
    > 
    > - GEN UNIKA
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Välj **Lägg till** att skapa miljön. Miljön startar etablering direkt med status visas i den **Mina virtuella datorer** lista. En ny resursgrupp skapas automatiskt av labbet för att etablera alla resurser som definierats i Azure Resource Manager-mallen.
1. När miljön har skapats väljer du miljön i den **Mina virtuella datorer** listan för att öppna fönstret resource group och bläddra igenom alla de resurser som etablerades i miljön.
    
    ![Min lista över virtuella datorer](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Du kan också expandera miljön för att visa bara listan över virtuella datorer som har etablerats i miljön.
    
    ![Min lista över virtuella datorer](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Klicka på någon av miljöer för att visa tillgängliga åtgärder - t.ex att använda artefakter, koppla datadiskar, ändra automatisk avstängning med mera.

    ![Miljö-åtgärder](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>Automatisera distribution av miljöer
Azure DevTest Labs ger dig möjlighet att använda en [Azure Resource Management Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) att skapa en miljö med en uppsättning resurser i laboratoriet. De här miljöerna kan innehålla alla Azure-resurser som kan skapas med Resource Manager-mallar. DevTest Labs-miljöer kan du lätt distribuera komplexa infrastrukturer på ett konsekvent sätt inom labbet. För närvarande är möjligt att lägga till en miljö i ett labb med hjälp av Azure portal när du skapar den en gång, men i en utveckling eller testning fall där flera skapande sker en automatisk distribution som gör att en bättre upplevelse.

Slutför följande stegen i den [konfigurera en egen mall databaser](#configure-your-own-template-repositories) avsnittet innan du fortsätter: 

1. Skapa Resource Manager-mallen som definierar de resurser som håller på att skapas. 
2. Konfigurera Resource Manager-mallen i Git en lagringsplats. 
3. Anslut Git-lagringsplatsen till labbet. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>PowerShell-skript för att distribuera Resource Manager-mall
Spara PowerShell-skript i nästa avsnitt på hårddisken (till exempel: deployenv.ps1) och kör skriptet när du har angett värden för SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (mapp) EnvironmentName på Git-lagringsplats.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>Exempelskript
Här är exempelskriptet för att skapa en miljö i labbet. Kommentarerna i skriptet hjälpa dig att bättre förstå skriptet. 

```powershell
#Requires -Module Az.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzAccount

# Select the subscription that has the lab.  
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzResource command. 
New-AzResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

Du kan också använda Azure CLI för att distribuera resurser med Resource Manager-mallar. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Endast användare med lab ägarbehörighet kan skapa virtuella datorer från en Resource Manager-mall med hjälp av Azure PowerShell. Om du vill automatisera skapa en virtuell dator med en Resource Manager-mall och du bara har användarbehörigheter, du kan använda den [ **az lab vm skapa** i CLI](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Allmänna begränsningar 

Överväg följande begränsningar när du använder Resource Manager-mall i DevTest Labs:

- Alla Resource Manager-mall som du skapar kan inte referera till befintliga resurser. Det kan bara referera till nya resurser. Dessutom, om du har en befintlig Resource Manager-mall som du normalt distribuerar utanför DevTest Labs och den innehåller referenser till befintliga resurser, kan den inte användas i laboratoriet.

   Det enda undantaget är att du **kan** refererar till ett befintligt virtuellt nätverk. 

- Att går inte skapa formler från lab virtuella datorer som har skapats från en Resource Manager-mall. 

- Att går inte skapa anpassade avbildningar från lab virtuella datorer som har skapats från en Resource Manager-mall. 

- De flesta principer utvärderas inte när du distribuerar Resource Manager-mallar.

   Du kan till exempel ha en lab princip som anger att en användare kan bara skapa fem virtuella datorer. En användare kan dock distribuera en Resource Manager-mall som skapar dussintals virtuella datorer. Principer som inte utvärderas omfattar:

   - Antalet virtuella datorer per användare
   - Antalet virtuella datorer i premium per labb användare
   - Antal premium-diskar per labb användare


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Konfigurera miljön resource group åtkomstbehörigheter för labbanvändare

Labbanvändare kan distribuera en Resource Manager-mall. Men som standard de har behörighet för läsare, vilket innebär att de inte kan ändra resurser i en resursgrupp i miljön. De kan exempelvis stoppa och starta sina resurser.

Följ stegen nedan för att ge användarna lab deltagare åtkomsträttigheter. Sedan, när de distribuerar en Resource Manager-mall de kommer att kunna redigera resurser i den miljön. 


1. På ditt labb **översikt** väljer **konfiguration och principer**.
1. Välj **Lab inställningar**.
1. I fönstret Lab inställningar väljer **deltagare** bevilja behörighet att skriva till labbanvändare.

    ![Konfigurera labb användarbehörigheter](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
* När en virtuell dator har skapats kan du ansluta till den virtuella datorn genom att välja **Connect** fönstret för hantering av den virtuella datorn.
* Visa och hantera resurser i en miljö genom att välja miljön i den **Mina virtuella datorer** lista i labbet. 
* Utforska den [Azure Resource Manager-mallar från Azure Quickstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates).
