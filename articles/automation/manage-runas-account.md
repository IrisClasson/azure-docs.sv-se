---
title: Hantera Azure Automation kör som-konton
description: Den här artikeln beskriver hur du hanterar ditt kör som-konton med PowerShell eller från portalen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 49b8554f6064f036d4305cf7a5c1450c2f18c48d
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798483"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Hantera Azure Automation kör som-konton

Kör som-konton i Azure Automation används för autentisering för att hantera resurser i Azure med Azure-cmdlets.

När du skapar ett kör som-konto, skapar en ny tjänsthuvudanvändare i Azure Active Directory och deltagarrollen tilldelas den här användaren på prenumerationsnivån. Du kan använda för runbooks som använder Hybrid Runbook Worker på Azure virtual machines, [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället för Kör som-konton att autentisera till Azure-resurser.

Det finns två typer av kör som-konton:

* **Azure kör som-konto** – det här kontot används för att hantera [Resource Manager-distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md) resurser.
  * Skapar ett Azure AD-program med ett självsignerat certifikat och ett tjänstobjektskonto för programmet i Azure AD och rollen Deltagare tilldelas för kontot i din aktuella prenumeration. Du kan ändra den här inställningen till Ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
  * Skapar en Automation-certifikattillgång med namnet *AzureRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller certifikatets privata nyckel som används av Azure AD-programmet.
  * Skapar en Automation-anslutningstillgång med namnet *AzureRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller applicationId, tenantId, subscriptionId och certifikatets tumavtryck.

* **Azure klassiska kör som-konto** – det här kontot används för att hantera [klassiska distributionsmodellen](../azure-resource-manager/resource-manager-deployment-model.md) resurser.
  * Skapar ett hanteringscertifikat i prenumerationen
  * Skapar en Automation-certifikattillgång med namnet *AzureClassicRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.
  * Skapar en Automation-anslutningstillgång med namnet *AzureClassicRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller prenumerationsnamnet, subscriptionId och certifikattillgångens namn.
  * Måste vara en medadministratör för prenumerationen för att skapa eller förnya
  
  > [!NOTE]
  > Azure Cloud Solution Provider (Azure CSP)-prenumerationer stöder endast Azure Resource Manager-modellen, icke - Azure Resource Manager-tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration inte Azure klassiska kör som-kontot skapas. Azure kör som-kontot skapades fortfarande. Läs mer om CSP-prenumerationer i [tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > Tjänstens huvudnamn för ett kör som-kontot har inte behörighet att läsa Azure Active Directory som standard. Om du vill lägga till behörigheter att läsa eller hantera Azure Active directory, du måste du bevilja den behörigheten på tjänstens huvudnamn under **API-behörigheter**. Mer information finns i [Lägg till behörigheter för att få åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Behörigheter för att konfigurera kör som-konton

Om du vill skapa eller uppdatera en Kör som-konto, måste du ha specifika privilegier och behörigheter. En Global administratör i Azure Active Directory och ägare i en prenumeration kan utföra alla aktiviteter. I en situation där du har uppdelning av uppgifter, visas i följande tabell en lista över aktiviteterna, motsvarande cmdlet och behörigheter som krävs:

|Aktivitet|Cmdlet:  |Minsta möjliga behörigheter  |Där du kan ange behörigheter|
|---|---------|---------|---|
|Skapa Azure AD-program|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Developer Programroll<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory > App-registreringar |
|Lägg till autentiseringsuppgift för programmet.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Programadministratör eller GLOBAL administratör<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory > App-registreringar|
|Skapa och få en AD-tjänstens huvudnamn|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Programadministratör eller GLOBAL administratör<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory > App-registreringar|
|Tilldela eller hämta RBAC-rollen för det angivna huvudnamnet|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Du måste ha följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Eller vara en:</br></br>Administratör för användaråtkomst eller ägare        | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Start > prenumerationer > \<prenumerationsnamn\> -åtkomstkontroll (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Deltagare i resursgrupp         |Resursgruppen för Automation-konto|
|Skapa eller ta bort en automationsanslutning|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Deltagare i resursgrupp |Resursgruppen för Automation-konto|

<sup>1</sup> användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) om Azure AD-klient **användare kan registrera program** alternativet i **användarinställningar**sidan är inställd på **Ja**. Om inställningen appregistreringar anges till **nr**, användaren som utför den här åtgärden måste vara det som definieras i tabellen ovan.

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i den **Global administratör** rollen på prenumerationen, läggs som gäst. I så fall kan du få en `You do not have permissions to create…` varning på den **Lägg till Automation-konto** sidan. Användare som har lagts till i den **Global administratör** rollen först kan tas bort från prenumerationens Active Directory-instans och läggs till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

## <a name="permissions-classic"></a>Behörigheter för att konfigurera klassiska kör som-konton

Om du vill konfigurera eller förnya klassiskt kör som-konton, måste du ha den **medadministratör** roll på prenumerationsnivån. Läs mer om klassisk behörigheter i [Azure klassiska prenumerationsadministratörer](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Skapa ett kör som-konto i portalen

I det här avsnittet utför du följande steg för att uppdatera ditt Azure Automation-konto i Azure Portal. Du skapar kör som-konton och klassiska kör som-konton var för sig. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.  

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Automation-konton**.
3. På sidan **Automation-konton** väljer du ditt Automation-konto i listan med Automation-konton.
4. I vänster fönster väljer du **Kör som-konton** under avsnittet **Kontoinställningar**.  
5. Beroende på vilket konto du behöver väljer du antingen **Azures Kör som-konto** eller **Azures klassiska Kör som-konto**. När du har gjort ditt val visas antingen fönstret **Lägg till Azures Kör som-konto** eller **Lägg till Azures klassiska Kör som-konto**. Efter att du läst översiktsinformationen klickar du på **Skapa** för att fortsätta att skapa Kör som-kontot.  
6. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll som meddelar att kontot skapats visas också. Den här processen kan ta ett par minuter att slutföra.  

## <a name="create-run-as-account-using-powershell"></a>Skapa kör som-konto med hjälp av PowerShell

## <a name="prerequisites"></a>Förutsättningar

Följande lista innehåller kraven för att skapa ett kör som-konto i PowerShell:

* Windows 10 eller Windows Server 2016 med Azure Resource Manager-moduler med version 3.4.1 och senare. PowerShell-skriptet stöds inte i tidigare versioner av Windows.
* Azure PowerShell 1.0 och senare. Information om PowerShell 1.0-versionen finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Ett Automation-konto, som refereras till som värdet för *-AutomationAccountName*- och *-ApplicationDisplayName*-parametrarna.
* Behörigheter som motsvarar det som listas i [krävs behörighet för att konfigurera kör som-konton](#permissions)

Att hämta värden för *SubscriptionID*, *ResourceGroup*, och *AutomationAccountName*, som är obligatoriska parametrar för skriptet, gör du följande:

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Automation-konton**.
1. På sidan Automation-konto väljer du ditt Automation-konto och sedan under **Kontoinställningar** väljer du **Egenskaper**.  
1. Obs den **prenumerations-ID**, **namn**, och **resursgrupp** värden på den **egenskaper** sidan.

   ![Sidan Automation-konto ”egenskaper”](media/manage-runas-account/automation-account-properties.png)

Det här PowerShell-skriptet har stöd för följande konfigurationer:

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto genom att använda ett certifikat utfärdat av en företagscertifikatutfärdare (CA).
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet.

>[!NOTE]
> Om du väljer något av alternativen för att skapa ett klassiskt Kör som-konto laddar du upp det offentliga certifikatet (filnamnstillägget .cer) när skriptet har körts till hanteringsarkivet för den prenumeration som Automation-kontot skapades i.

1. Spara följande skript på datorn. I det här exemplet sparar du det med filnamnet *New-RunAsAccount.ps1*.

   Skriptet använder flera Azure Resource Manager-cmdletar för att skapa resurser. Föregående [behörigheter](#permissions) tabell visas cmdletarna och deras behörigheter som krävs.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen biblioteket objekt, om du inte ser **Connect-AzureRMAccount**, du kan använda **Add-AzureRmAccount**, eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation Konto.

1. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
1. Från det upphöjda kommandoradsgränssnittet går du till mappen som innehåller det skript som du skapade i steg 1.  
1. Kör skriptet genom att använda de parametervärden för konfigurationen som du behöver.

    **Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen för prenumerationsadministratörer och som är medadministratör för prenumerationen.

Notera följande när skriptet har körts:

* Om du skapade ett klassiskt Kör som-konto med ett självsignerat offentligt certifikat (CER-fil) skapas och sparas det av skriptet i mappen för tillfälliga filer på datorn under användarprofilen *%USERPROFILE%\AppData\Local\Temp*, som du använde för att köra PowerShell-sessionen.

* Om du skapade ett klassiskt Kör som-konto med ett offentligt företagscertifikat (CER-fil) använder du det här certifikatet. Följ anvisningarna för [laddar upp ett hanterings-API-certifikat till Azure-portalen](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto

Det här avsnittet beskriver hur du tar bort och sedan återskapar ett Kör som-konto eller ett klassiskt Kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort ett Kör som-konto eller ett klassiskt Kör som-konto kan du återskapa det på Azure Portal.

1. Öppna ditt Automation-konto på Azure Portal.

2. På sidan **Automation-konto** väljer du **Kör som-konton**.

3. På egenskapssidan för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort. Klicka på **Ta bort** i rutan **Egenskaper** för det valda kontot.

   ![Ta bort Kör som-konto](media/manage-runas-account/automation-account-delete-runas.png)

1. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

1. När kontot har tagits bort måste du återskapa det på egenskapssidan för **Kör som-konton** genom att välja alternativet Skapa för **Kör som-konto i Azure**.

   ![Återskapa Kör som-kontot för Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Förnya självsignerade certifikat

Någon gång innan kör som-kontot upphör att gälla, måste du förnya certifikatet. Om du tror att ditt Kör som-konto har komprometterats kan du ta bort och återskapa det. Det här avsnittet beskriver hur du utför dessa åtgärder.

Det självsignerade certifikatet som du skapade för Kör som-kontot går ut ett år efter det datum då det skapades. Du kan förnya det när som helst innan det upphör att gälla. När du förnyar det behålls det aktuella giltiga certifikatet för att säkerställa att alla runbooks som köas upp eller som körs aktivt, och som autentiserar med Kör som-kontot inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

> [!NOTE]
> Om du har konfigurerat ditt Kör som-konto för Automation att använda ett certifikat som utfärdats av din företagscertifikatutfärdare och du använder det här alternativet, så ersätts företagscertifikatet av ett självsignerat certifikat.

Du förnyar certifikatet genom att göra följande:

1. Öppna ditt Automation-konto på Azure Portal.

1. Välj **kör som-konton** under **kontoinställningar**.

    ![Egenskapsrutan för Automation-konto](media/manage-runas-account/automation-account-properties-pane.png)

1. På egenskapssidan för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill förnya certifikatet för.

1. I rutan **Egenskaper** för det valda kontot klickar du på **Förnya certifikat**.

    ![Förnya certifikat för Kör som-konto](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="limiting-run-as-account-permissions"></a>Begränsar behörigheterna kör som-konto

Om du vill styra inriktning för automation mot resurser i Azure, kan du köra den [uppdatering AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) skript i PowerShell-galleriet för att ändra dina befintliga kör som-konto-tjänstens huvudnamn Skapa och använda en anpassad rolldefinition. Den här rollen har behörighet till alla resurser förutom [Key Vault](https://docs.microsoft.com/azure/key-vault/). 

> [!IMPORTANT]
> Efter att ha kört den `Update-AutomationRunAsAccountRoleAssignments.ps1` skript och runbooks som har åtkomst till KeyVault genom att använda RunAs-konton fungerar inte längre. Du bör granska runbooks i ditt konto för anrop till Azure KeyVault.
>
> Att aktivera åtkomst till KeyVault från Azure Automation-runbooks skulle du behöva [Lägg till Kör som-konto i Keyvaults behörigheter](#add-permissions-to-key-vault).

Om du vill begränsa vad RunAs tjänstens huvudnamn kan göra ytterligare kan du lägga till andra typer av resurser till den `NotActions` på den anpassa rolldefinitionen. I följande exempel begränsar åtkomsten till `Microsoft.Compute`. Om du lägger till den till den **NotActions** av rolldefinitionen, den här rollen kan inte komma åt alla resurser för beräkning. Läs mer om rolldefinitioner i [förstå rolldefinitioner för Azure-resurser](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Att fastställa om tjänstens huvudnamn som används av ditt kör som-konto finns i den **deltagare** eller en anpassad rolldefinition går du till ditt Automation-konto och under **kontoinställningar**väljer **kör som konton** > **Azure kör som-konto**. Under **rollen** hittar du den rolldefinition som används. 

[![](media/manage-runas-account/verify-role.png "Verifiera rollen kör som-konto")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Du kan använda för att avgöra den rolldefinition som används av Automation kör som-konton för flera prenumerationer och Automation-konton, den [Kontrollera AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) skript i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägg till behörigheter till Key Vault

Om du vill tillåta Azure Automation att hantera Key Vault och kör som-konto tjänsten huvudnamn använder en anpassad rolldefinition måste du vidta ytterligare åtgärder för att tillåta det här beteendet:

* Bevilja behörighet till Key Vault
* Konfigurera principer för åtkomst

Du kan använda den [utöka AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) skript i PowerShell-galleriet för att ge dina behörigheter för Kör som-konto till KeyVault eller besök [bevilja program åtkomst till ett nyckelvalv ](../key-vault/key-vault-group-permissions-for-apps.md) för mer information om inställningar för behörigheter för KeyVault.

## <a name="misconfiguration"></a>Felaktig konfiguration

Vissa konfigurationsobjekt som krävs för att Kör som-kontot eller det klassiska Kör som-kontot ska fungera kanske har tagits bort eller kanske inte skapades korrekt under den ursprungliga konfigurationen. Exempel på dessa objekt är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagarrollen
* Huvudnamn för tjänsten eller program i Azure AD

I den föregående instansen och andra instanser av felaktiga konfigurationer identifierar Automation-kontot ändringarna och visar statusen *Ofullständig* på egenskapssidan för **Kör som-konton** för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/manage-runas-account/automation-account-runas-incomplete-config.png)

När du väljer Kör som-kontot visas följande felmeddelande i rutan **Egenskaper** för kontot:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="next-steps"></a>Nästa steg

* Läs mer om tjänstens huvudnamn, [programobjekt och tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md).
* Mer information om certifikat och Azure-tjänster finns i [Certifikatöversikt för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
