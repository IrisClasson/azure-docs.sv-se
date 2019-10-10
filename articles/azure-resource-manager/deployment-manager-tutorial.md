---
title: Använda Azure Deployment Manager med Resource Manager-mallar | Microsoft Docs
description: Använda Resource Manager-mallar med Azure Deployment Manager för att distribuera Azure-resurser.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 97d9aa1ed9440011fdaab3aa8eb9d3942b5a8acf
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170369"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Självstudier: Använda Azure Deployment Manager med Resource Manager-mallar (offentlig förhandsversion)

Lär dig hur du använder [Azure Deployment Manager](./deployment-manager-overview.md) för att distribuera dina program i flera regioner. Om du föredrar en snabbare metod skapar [snabb starten av Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) de konfigurationer som krävs i din prenumeration och anpassar artefakterna för att distribuera ett program över flera regioner. Snabb starten utför samma uppgifter som i den här självstudien.

För att använda Deployment Manager måste du skapa två mallar:

* **En topologimall**: Beskriver de Azure-resurser som utgör dina program och var de ska distribueras.
* **En distributionsmall**: Beskriver stegen för att distribuera dina program.

> [!IMPORTANT]
> Om din prenumeration har marker ATS för Kanarie för att testa nya Azure-funktioner kan du bara använda Azure Deployment Manager för att distribuera till Kanarie regionerna. 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förstå scenariot
> * Ladda ned filerna för självstudiekursen
> * Förbereda artefakterna
> * Skapa en användardefinierad hanterad identitet
> * Skapa mallen för tjänsttopologin
> * Skapa distributionsmallen
> * Distribuera mallarna
> * Verifiera distributionen
> * Distribuera den senare versionen
> * Rensa resurser

Ytterligare resurser:

* [Azure Deployment Manager REST API referens](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Självstudie: Använd hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Viss erfarenhet av att utveckla [Azure Resource Manager-mallar](./resource-group-overview.md).
* Azure PowerShell. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Deployment Manager-cmdlets. För att installera dessa cmdlets i förhandsversionen behöver du den senaste versionen av PowerShellGet. Information om hur du skaffar den senaste versionen finns i [Installera PowerShellGet](/powershell/gallery/installing-psget). Stäng PowerShell-fönstret när du har installerat PowerShellGet. Öppna ett nytt upphöjt PowerShell-fönster och använd följande kommando:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

* [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Azure Storage Explorer krävs inte, men det gör saker enklare.

## <a name="understand-the-scenario"></a>Förstå scenariot

Mallen för tjänsttopologin beskriver de Azure-resurser som utgör din tjänst och var de ska distribueras. Definitionen av tjänsttopologin har följande hierarki:

* Tjänsttopologi
  * Services
    * Tjänstenheter

Följande diagram visar tjänsttopologin som används i den här självstudien:

![Diagram över scenario i självstudiekurs om Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Det finns två tjänster som allokeras i regionen USA, västra och USA, östra.  Varje tjänst har två tjänstenheter – ett webbprogram på klientsidan och ett lagringskonto för serverdelen. Definitionerna av tjänstenheterna innehåller länkar till mall- och parameterfilerna för att skapa webbprogrammen och lagringskontona.

## <a name="download-the-tutorial-files"></a>Ladda ned filerna för självstudiekursen

1. Ladda ned [mallarna och artefakterna](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) som används i den här självstudien.
2. Packa upp filerna till dator på din plats.

Det finns två mappar under rotmappen:

* **ADMTemplates**: Innehåller Deployment Manager-mallarna, som innehåller:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: Innehåller både mallartefakterna och de binära artefakterna. Se [Förbereda artefakterna](#prepare-the-artifacts).

Observera att det finns två uppsättningar med mallar.  Den ena uppsättningen är Deployment Manager-mallarna som används för att distribuera tjänsttopologin och distributionen. Den andra uppsättningen anropas från tjänstenheterna för att skapa webbtjänster och lagringskonton.

## <a name="prepare-the-artifacts"></a>Förbereda artefakterna

Mappen ArtifactStore från nedladdningen innehåller två mappar:

![Diagram över artefaktkällan i självstudiekurs om Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Mappen **templates**: Innehåller mallartefakterna. **1.0.0.0** och **1.0.0.1** representerar de två versionerna av de binära artefakterna. Det finns en mapp för varje tjänst (tjänsten för USA, östra och tjänsten för USA, västra) i varje version. Varje tjänst har ett par med mall- och parameterfiler för att skapa ett lagringskonto, och ett annat par för att skapa ett webbprogram. Webbprogrammallen anropar ett komprimerat paket, som innehåller filerna för webbprogrammet. Den komprimerade filen är en binär artefakt som lagras i binaries-mappen.
* Mappen **binaries**: Innehåller de binära artefakterna. **1.0.0.0** och **1.0.0.1** representerar de två versionerna av de binära artefakterna. I varje version finns det en ZIP-fil för att skapa webbprogrammet på platsen USA, västra och en andra ZIP-fil för att skapa webbprogrammet på platsen USA, östra.

De två versionerna (1.0.0.0 och 1.0.0.1) är avsedda för [ändringsdistributionen](#deploy-the-revision). Även om både mallartefakterna och de binära artefakterna har två versioner, skiljer sig endast de binära artefakterna åt i de två versionerna. I praktiken uppdateras binära artefakter oftare jämfört med mallartefakter.

1. Öppna **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** i ett redigeringsprogram. Det är en enkel mall för att skapa ett lagringskonto.
2. Öppna **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Azure Deployment Manager-självstudie om att skapa en webbprogrammall](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Mallen anropar ett distributionspaket, som innehåller filerna för webbprogrammet. I den här självstudien innehåller det komprimerade paketet bara en index. html-fil.
3. Öppna **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Azure Deployment Manager-självstudie om att skapa containerRoot och parametrar för en webbprogrammall](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Värdet för deployPackageUri är sökvägen till distributionspaketet. Parametern innehåller en **$containerRoot**-variabel. Värdet för $containerRoot anges i [distributionsmallen](#create-the-rollout-template) genom att artefaktkällans SAS-plats, artefaktrot och deployPackageUri sammanfogas.
4. Öppna **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    HTML-koden visar platsen och versionsinformationen. Den binära filen i mappen 1.0.0.1 visar ”Version 1.0.0.1”. När du har distribuerat tjänsten kan du bläddra till dessa sidor.
5. Granska andra artefaktfiler. Det hjälper dig att förstå scenariot bättre.

Mallartefakter används av tjänsttopologimallen och binära artefakter används av distributionsmallen. Både topologimallen och distributionsmallen definierar en Azure-resurs för en artefaktkälla, som är en resurs som används för att peka Resource Manager på mallen och de binära artefakterna som används i distributionen. För att förenkla självstudien används ett lagringskonto för att lagra både mallartefakterna och de binära artefakterna. Båda artefaktkällorna peka på samma lagringskonto.

1. Skapa ett Azure-lagringskonto. Anvisningar finns i [Snabbstart: Ladda upp, ladda ned och lista blobar med hjälp av Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Skapa en blobcontainer i lagringskontot.
3. Kopiera de två mapparna (binärfiler och mallar) och innehållet i de två mapparna till blobcontainern. [Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) stöder dra och släpp-funktionen.
4. Hämta SAS-platsen för containern med hjälp av följande anvisningar:

    1. Gå till blobcontainern från Azure Storage Explorer.
    2. Högerklicka på blobcontainern i den vänstra rutan och välj sedan **Hämta signatur för delad åtkomst**.
    3. Konfigurera **starttid** och **förfallotid**.
    4. Välj **Skapa**.
    5. Skapa en kopia av URL:en. Den här URL:en krävs för att fylla i ett fält i de två parameterfilerna, [filen med topologiparametrar](#topology-parameters-file) och [filen med distributionsparametrar](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Skapa den användartilldelade hanterade identiteten

Senare i självstudien ska du distribuera en distribution. En användartilldelad hanterad identitet behövs för att utföra distributionsåtgärderna (till exempel för att distribuera webbprogrammen och lagringskontot). Den här identiteten måste beviljas åtkomst till Azure-prenumerationen som du distribuerar tjänsten till samt ha tillräcklig behörighet för att slutföra artefaktdistributionen.

Du måste skapa en användartilldelad hanterad identitet och konfigurera åtkomstkontrollen för din prenumeration.

> [!IMPORTANT]
> Den användartilldelade hanterade identiteten måste finnas på samma plats som [distributionen](#create-the-rollout-template). För närvarande kan Deployment Manager-resurser, inklusive distributionsresursen, endast skapas i regionen USA, centrala eller USA, östra 2. Detta gäller dock bara för Deployment Manager-resurser (till exempel tjänsttopologi, tjänster, tjänstenheter, distribution och steg). Dina målresurser kan distribueras till alla Azure-regioner som stöds. I den självstudien distribueras till exempel Deployment Manager-resurser till USA, centrala, men tjänsterna distribueras till USA, östra och USA, västra. Den här begränsningen tas bort framöver.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Skapa en [användartilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. Från portalen väljer du **Prenumerationer** på den vänstra menyn och väljer sedan din prenumeration.
4. Välj **Åtkomstkontroll (IAM)** och sedan **Lägg till rolltilldelning**.
5. Ange eller välj följande värden:

    ![Azure Deployment Manager-självstudie om åtkomstkontroll med användartilldelade hanterade identiteter](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Roll**: Ge tillräcklig behörighet för att slutföra artefaktdistributionen (webbprogrammen och lagringskontona). Välj **Deltagare** i den här självstudien. I praktiken vill du begränsa behörigheterna till de minsta nödvändiga.
    * **Tilldelad åtkomst till**: Välj **Användartilldelad hanterad identitet**.
    * Välj den användartilldelade hanterade identitet som du skapade tidigare i självstudien.
6. Välj **Spara**.

## <a name="create-the-service-topology-template"></a>Skapa mallen för tjänsttopologin

Öppna **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Parametrarna

Mallen innehåller följande parametrar:

![Azure Deployment Manager-självstudie om parametrar för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

* **namePrefix**: Det här prefixet används för att skapa namnen för Deployment Manager-resurserna. Om du till exempel använder prefixet ”jdoe” är tjänsttopologins namn **jdoe**ServiceTopology.  Resursnamnen definieras i variabelavsnittet i den här mallen.
* **azureResourcelocation**: För att förenkla självstudien delar alla resurser den här platsen om inget annat anges. För närvarande kan Azure Deployment Manager-resurser endast skapas i regionen **USA, centrala** eller **USA, östra 2**.
* **artifactSourceSASLocation**: SAS-URI:n till blobcontainern där filerna för tjänstenheterna och parametrarna lagras för distribution.  Se [Förbereda artefakterna](#prepare-the-artifacts).
* **templateArtifactRoot**: Offset-sökvägen från blobcontainern där mallarna och parametrarna lagras. Standardvärdet är **templates/1.0.0.0**. Ändra inte det här värdet såvida du inte vill ändra mappstrukturen som beskrivs i [Förbereda artefakterna](#prepare-the-artifacts). Relativa sökvägar används i den här självstudien.  Den fullständiga sökvägen skapas genom att **artifactSourceSASLocation**, **templateArtifactRoot**, och **templateArtifactSourceRelativePath** (eller **parametersArtifactSourceRelativePath**) sammanfogas.
* **targetSubscriptionID**: Prenumerations-ID:t som Deployment Manager-resurserna kommer att distribueras och faktureras till. Använd ditt prenumerations-ID i den här självstudien.

### <a name="the-variables"></a>Variablerna

Variabelavsnittet definierar namnen på resurserna, Azure-platserna för de två tjänsterna: **Service WUS** och **Service EUS**, samt artefaktsökvägarna:

![Azure Deployment Manager-självstudie om variabler för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Jämför artefaktsökvägarna med mappstrukturen som du laddade upp till lagringskontot. Observera att artefaktsökvägarna är relativa sökvägar. Den fullständiga sökvägen skapas genom att **artifactSourceSASLocation**, **templateArtifactRoot**, och **templateArtifactSourceRelativePath** (eller **parametersArtifactSourceRelativePath**) sammanfogas.

### <a name="the-resources"></a>Resurserna

Det finns två resurser som definieras på rotnivå: *en artefaktkälla* och *en tjänsttopologi*.

Definitionen av artefaktkällan är:

![Azure Deployment Manager-självstudie om artefaktkällan för resurser för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Följande skärmbild visar endast vissa delar av definitionerna av tjänsttopologin, tjänsterna och tjänstenheterna:

![Azure Deployment Manager-självstudie om tjänsttopologin för resurser för topologimallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** används för att associera resursen för artefaktkällan med resursen för tjänsttopologin.
* **dependsOn**: Alla resurser för tjänsttopologin är beroende av resursen för artefaktkällan.
* **artifacts** pekar på mallartefakterna.  Relativa sökvägar används här. Den fullständiga sökvägen skapas genom att artifactSourceSASLocation (definieras i artefaktkällan), artifactRoot (definieras i artefaktkällan) och templateArtifactSourceRelativePath (eller parametersArtifactSourceRelativePath) sammanfogas.

### <a name="topology-parameters-file"></a>Topologiparameterfil

Du skapar en parameterfil som används med topologimallen.

1. Öppna **\ADMTemplates\CreateADMServiceTopology.Parameters** i Visual Studio Code eller i valfritt redigeringsprogram.
2. Fyll parametervärdena:

    * **namePrefix**: Ange en sträng med 4–5 tecken. Det här prefixet används för att skapa unika Azure-resursnamn.
    * **azureResourceLocation**: Om du inte är bekant med Azure-platser använder du **centralus** i den här självstudien.
    * **artifactSourceSASLocation**: Ange SAS-URI:n till rotkatalogen (blobcontainern) där filerna för tjänstenheterna och parametrarna lagras för distribution.  Se [Förbereda artefakterna](#prepare-the-artifacts).
    * **templateArtifactRoot**: Såvida du inte ändrar mappstrukturen för artefakterna använder du **templates/1.0.0.0** i den här självstudien.
    * **targetScriptionID**: Ange ditt prenumerations-ID för Azure.

> [!IMPORTANT]
> Topologimallen och distributionsmallen delar vissa vanliga parametrar. Dessa parametrar måste ha samma värden. Dessa parametrar är: **namePrefix**, **azureResourceLocation** och **artifactSourceSASLocation** (båda artefaktkällorna delar samma lagringskonto i den här självstudien).

## <a name="create-the-rollout-template"></a>Skapa distributionsmallen

Öppna **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Parametrarna

Mallen innehåller följande parametrar:

![Azure Deployment Manager-självstudie om parametrar för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **namePrefix**: Det här prefixet används för att skapa namnen för Deployment Manager-resurserna. Om du till exempel använder prefixet ”jdoe” är distributionsnamnet **jdoe**Rollout.  Namnen definieras i variabelavsnittet i mallen.
* **azureResourcelocation**: För att förenkla självstudien delar alla Deployment Manager-resurser den här platsen om inget annat anges. För närvarande kan Azure Deployment Manager-resurser endast skapas i regionen **USA, centrala** eller **USA, östra 2**.
* **artifactSourceSASLocation**: SAS-URI:n till rotkatalogen (blobcontainern) där filerna för tjänstenheterna och parametrarna lagras för distribution.  Se [Förbereda artefakterna](#prepare-the-artifacts).
* **binaryArtifactRoot**: Standardvärdet är **binaries/1.0.0.0**. Ändra inte det här värdet såvida du inte vill ändra mappstrukturen som beskrivs i [Förbereda artefakterna](#prepare-the-artifacts). Relativa sökvägar används i den här självstudien.  Den fullständiga sökvägen skapas genom att **artifactSourceSASLocation**, **binaryArtifactRoot**, och **deployPackageUri** som anges i CreateWebApplicationParameters.json sammanfogas.  Se [Förbereda artefakterna](#prepare-the-artifacts).
* **managedIdentityID**: Den användartilldelade hanterade identiteten som utför distributionsåtgärderna. Se [Skapa den användartilldelade hanterade identiteten](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Variablerna

Namnen på resurserna definieras i variabelavsnittet. Kontrollera att namnet på tjänsttopologin, tjänstnamnen och namnen på tjänstenheterna matchar namnen som definieras i [topologimallen](#create-the-service-topology-template).

![Azure Deployment Manager-självstudie om variabler för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Resurserna

Tre resurser definieras på rotnivå: en artefaktkälla, ett steg och en distribution.

Artefaktkällans definition är identisk med den som anges i topologimallen.  Mer information finns i [Skapa mallen för tjänsttopologin](#create-the-service-topology-template).

Följande skärmbild visar definitionen av wait-steget:

![Azure Deployment Manager-självstudie om wait-steget i resurserna för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

För varaktigheten används [ISO 8601-standarden](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (versaler krävs) är ett exempel på en 1-minuters väntan.

I följande skärmbild visas endast vissa delar av distributionsdefinitionen:

![Azure Deployment Manager-självstudie om distribution av resurser för distributionsmallen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: Distributionsresursen är beroende av resursen för artefaktkällan och de steg som har definierats.
* **artifactSourceId**: Används för att associera resursen för artefaktkällan med distributionsresursen.
* **targetServiceTopologyId**: Används för att associera resursen för tjänsttopologin med distributionsresursen.
* **deploymentTargetId**: Det här är ID:t för resursen för tjänstenheter för tjänsttopologiresursen.
* **preDeploymentSteps** och **postDeploymentSteps**: Innehåller distributionsstegen. I mallen anropas ett wait-steg.
* **dependsOnStepGroups**: Konfigurera beroendena mellan steggrupperna.

### <a name="rollout-parameters-file"></a>Fil för distributionsparametrar

Du skapar en parameterfil som används med distributionsmallen.

1. Öppna **\ADMTemplates\CreateADMRollout.Parameters** i Visual Studio Code eller valfritt redigeringsprogram.
2. Fyll parametervärdena:

    * **namePrefix**: Ange en sträng med 4–5 tecken. Det här prefixet används för att skapa unika Azure-resursnamn.
    * **azureResourceLocation**: För närvarande kan Deployment Manager-resurser endast skapas i regionen **USA,centrala** eller **USA, östra 2**.
    * **artifactSourceSASLocation**: Ange SAS-URI:n till rotkatalogen (blobcontainern) där filerna för tjänstenheterna och parametrarna lagras för distribution.  Se [Förbereda artefakterna](#prepare-the-artifacts).
    * **binaryArtifactRoot**: Såvida du inte ändrar mappstrukturen för artefakterna använder du **binaries/1.0.0.0** i den här självstudien.
    * **managedIdentityID**: Ange den användartilldelade hanterade identiteten. Se [Skapa den användartilldelade hanterade identiteten](#create-the-user-assigned-managed-identity). Syntax:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Topologimallen och distributionsmallen delar vissa vanliga parametrar. Dessa parametrar måste ha samma värden. Dessa parametrar är: **namePrefix**, **azureResourceLocation** och **artifactSourceSASLocation** (båda artefaktkällorna delar samma lagringskonto i den här självstudien).

## <a name="deploy-the-templates"></a>Distribuera mallarna

Azure PowerShell kan användas för att distribuera mallarna.

1. Kör skriptet för att distribuera tjänsttopologin.

    ```azurepowershell
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"

    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    > [!NOTE]
    > `New-AzResourceGroupDeployment` är ett asynkront anrop. Meddelandet lyckades innebär att distributionen har startats. För att verifiera distributionen, se steg 2 och steg 4 i den här proceduren.

2. Kontrollera att tjänsttopologin och de angivna resurserna har skapats på Azure-portalen:

    ![Azure Deployment Manager-självstudie om resurser för en distribuerad tjänsttopologi](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    **Visa dolda typer** måste väljas för att resurserna ska visas.

3. <a id="deploy-the-rollout-template"></a>Distribuera distributionsmallen:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Kontrollera distributionsförloppet med hjälp av följande PowerShell-skript:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Du måste installera PowerShell-cmdlets för Deployment Manager innan du kan köra denna cmdlet. Se Förutsättningar. -Verbose-växeln kan användas för att se hela utdata.

    Följande exempel visar körningsstatusen:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    När distributionen har distribuerats bör det finnas ytterligare två resursgrupper, en för varje tjänst.

## <a name="verify-the-deployment"></a>Verifiera distributionen

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Bläddra till de nyligen skapade webbprogrammen under de nya resursgrupperna som skapades i samband med distributionen.
3. Öppna webbprogrammet i en webbläsare. Kontrollera platsen och versionen för index.html-filen.

## <a name="deploy-the-revision"></a>Distribuera ändringen

När du har en ny version (1.0.0.1) av webbprogrammet kan du använda följande procedur för att distribuera om webbprogrammet.

1. Öppna CreateADMRollout.Parameters.json.
2. Uppdatera **binaryArtifactRoot** till **binaries/1.0.0.1**.
3. Distribuera om distributionen enligt anvisningarna i [Distribuera mallarna](#deploy-the-rollout-template).
4. Verifiera distributionen genom att följa anvisningarna i [Verifiera distributionen](#verify-the-deployment). Version 1.0.0.1 bör visas på webbsidan.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Använd fältet **Filtrera efter namn** för att visa resursgrupperna som skapats i den här självstudien. De bör vara 3–4:

    * **&lt;namePrefix>rg**: Innehåller Deployment Manager-resurserna.
    * **&lt;namePrefix>ServiceWUSrg**: Innehåller resurserna som definieras av ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: Innehåller resurserna som definieras av ServiceEUS.
    * Resursgruppen för den användardefinierade hanterade identiteten.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** från menyn längst upp.
5. Upprepa de två sista stegen för att ta bort andra resursgrupper som skapats av den här självstudien.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Azure Deployment Manager. Information om hur du integrerar hälso övervakning i Azure Deployment Manager finns i [Självstudier: använda hälso kontroll i Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
