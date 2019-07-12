---
title: Azure Disk Encryption med Azure AD – krav (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5fa8e54a6a665b1bad91a87ca8e58f873df1ae8a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672323"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Krav för Azure Disk Encryption (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar behovet av att tillhandahålla en parameter för Azure AD-program att aktivera VM-diskkryptering. Med den nya versionen kan krävs du inte längre för att ange autentiseringsuppgifter för Azure AD under steget Aktivera kryptering. Alla nya virtuella datorer måste vara krypterat utan parametrarna Azure AD-program med hjälp av den nya versionen. Instruktioner för att aktivera diskkryptering för virtuell dator med hjälp av den nya versionen finns [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md). Virtuella datorer som redan har krypterats med Azure AD-program parametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntax.**

 Den här artikeln krävs för Azure Disk Encryption, förklarar objekt som måste vara uppfyllda innan du kan använda Azure Disk Encryption. Tillsammans med allmänna krav Azure Disk Encryption är integrerad med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) och använder en Azure AD-program för autentisering för att hantera krypteringsnycklar i nyckelvalvet. Du kan också använda [Azure PowerShell](/powershell/azure/overview) eller [Azure CLI](/cli/azure/) att ställa in eller konfigurera Key Vault och Azure AD-programmet.

Innan du aktiverar Azure Disk Encryption på virtuella Azure IaaS-datorer för de scenarier som stöds som beskrivs i den [översikt över Azure Disk Encryption](azure-security-disk-encryption-overview.md) artikel, se till att ha kraven på plats. 

> [!WARNING]
> - Vissa rekommendationerna kan öka data, nätverk eller Resursanvändning för beräkning, vilket resulterar i ytterligare kostnader för licens eller prenumeration. Du måste ha en giltig aktiv Azure-prenumeration att skapa resurser i Azure i regionerna som stöds.
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-app](azure-security-disk-encryption-prerequisites-aad.md) för att kryptera den här virtuella datorn, måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) på den här krypterade virtuella datorn eftersom det är inte ett scenario som stöds, betydelse växla från AAD-programmet för det här krypterade virtuella datorer stöds inte ännu. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-operating-systems"></a>Operativsystem som stöds

### <a name="windows"></a>Windows

- Windows-klient: Windows 8 och senare.
- Windows Server: Windows Server 2008 R2 och senare.  
 
> [!NOTE] 
> Windows Server 2008 R2 kräver .NET Framework 4.5 installeras för kryptering; installera den från Windows Update med valfri uppdatering Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core och Windows Server 2016 Core kräver bdehdcfg komponenten installeras på den virtuella datorn för kryptering.

### <a name="linux"></a>Linux 

Azure Disk Encryption stöds för en delmängd av den [Azure-godkända Linux-distributioner](../virtual-machines/linux/endorsed-distros.md), vilket är en delmängd av alla Linux-servern möjliga distributioner till sig själv.

![Venn-Diagram Linux server-distributioner som har stöd för Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Linux-server-distributioner som inte godkänts av Azure stöder inte Azure Disk Encryption och endast följande distributioner och versioner av dessa som godkänts, stöd för Azure Disk Encryption:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 18.04| OS- och disk |
| Ubuntu | 16.04| OS- och disk |
| Ubuntu | 14.04.5</br>[med Azure justerade kernel uppdateras till 4.15 eller senare](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | OS- och disk |
| RHEL | 7.6 | OS- och disk (Se kommentaren nedan) |
| RHEL | 7.5 | OS- och disk (Se kommentaren nedan) |
| RHEL | 7.4 | OS- och disk (Se kommentaren nedan) |
| RHEL | 7.3 | OS- och disk (Se kommentaren nedan) |
| RHEL | 7.2 | OS- och disk (Se kommentaren nedan) |
| RHEL | 6.8 | Datadisk (Se kommentaren nedan) |
| RHEL | 6.7 | Datadisk (Se kommentaren nedan) |
| CentOS | 7.6 | OS- och disk |
| CentOS | 7.5 | OS- och disk |
| CentOS | 7.4 | OS- och disk |
| CentOS | 7.3 | OS- och disk |
| CentOS | 7.2n | OS- och disk |
| CentOS | 6.8 | Datadisk |
| openSUSE | 42.3 | Datadisk |
| SLES | 12-SP4 | Datadisk |
| SLES | 12-SP3 | Datadisk |

> [!NOTE]
> Ny ADE implementeringen har stöd för RHEL-OS- och datadisk för RHEL7 betala per användning-avbildningar. ADE stöds för närvarande inte för RHEL Bring-Your-Own-prenumeration (BYOS)-avbildningar. Se [Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md) för mer information.

- Azure Disk Encryption kräver att dina nyckelvalv och virtuella datorer finns i samma Azure-region och prenumeration. Konfigurera resurserna i olika områden orsakar ett fel i Azure Disk Encryption-funktionen aktiveras.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Ytterligare krav för Linux IaaS-datorer 

- Azure Disk Encryption kräver dm-crypt och vfat moduler är finns på systemet. Ta bort eller inaktivera vfat från standardavbildningen förhindrar att systemet från att läsa den viktiga volymen och hämta den nyckel som behövs för att låsa upp diskarna på efterföljande omstarter. System-härdningssteg som tar bort modulen vfat från systemet är inte kompatibla med Azure Disk Encryption. 
- Innan du aktiverar kryptering måste på diskar som ska krypteras anges korrekt på/etc/fstab. Använd ett beständigt block enhetsnamn för den här posten som enhetens namn i formatet ”/ dev/sdX” det går inte att förlita sig på som ska associeras med samma disk mellan omstarter, särskilt när kryptering används. Mer information om detta finns här: [Felsöka ändringar av enhetsnamn Linux VM](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Kontrollera att/etc/fstab-inställningarna har konfigurerats korrekt för montering. Kör mount - ett kommando för att konfigurera de här inställningarna, eller starta om den virtuella datorn och utlösa återmontering på så sätt. När detta är slutfört kan du kontrollera resultatet av kommandot lsblk att verifiera att enheten fortfarande är ansluten. 
  - Om filen/etc/fstab inte montera enheten korrekt innan du aktiverar kryptering, Azure Disk Encryption inte montera den korrekt.
  - Azure Disk Encryption-processen flyttar mount-information från/etc/fstab och i sin egen konfigurationsfilen som en del av krypteringsprocessen. Inte vara alarmed att se posten saknas i/etc/fstab när data diskkryptering har slutförts.
  - Innan du börjar kryptering, måste du stoppa alla tjänster och processer som kan skriva till monterad datadiskar och inaktivera dem, så att de inte startar om automatiskt efter en omstart. Dessa kan filerna är öppna på de här partitionerna förhindrar metoden kryptering för att montera om dem, orsakar fel för krypteringen. 
  - Efter omstart tar det tid för Azure Disk Encryption-processen för att montera de nyligen krypterade diskarna. De kommer inte blir tillgängliga omedelbart efter en omstart. Processen behöver tid att börja låsa upp och sedan montera de krypterade enheterna innan är tillgängliga för andra processer att få åtkomst till. Den här processen kan ta mer än en minut efter omstart beroende på system-egenskaper.

Ett exempel på kommandon som kan användas för att montera datadiskarna och skapa de nödvändiga/etc/fstab poster finns i [linjer 244-248 i den här skriptfilen](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Nätverk och en Grupprincip

**Om du vill aktivera Azure Disk Encryption-funktionen med hjälp av äldre AAD måste frågesträngparameterns syntax, IaaS-datorer uppfylla följande slutpunkt configuration nätverkskraven:** 
  - Om du vill hämta en token för att ansluta till ditt nyckelvalv, IaaS VM måste vara kan ansluta till en Azure Active Directory-slutpunkt \[login.microsoftonline.com\].
  - Om du vill skriva dem till ditt nyckelvalv kunna IaaS VM ansluta till slutpunkten för nyckelvalvet.
  - IaaS VM måste kunna ansluta till en Azure storage-slutpunkt som är värd för lagringsplatsen Azure-tillägget och ett Azure storage-konto som är värd för VHD-filer.
  -  Om din säkerhetsprincip begränsar åtkomst från virtuella Azure-datorer till Internet, kan du matcha den föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brandvägg](../key-vault/key-vault-access-behind-firewall.md).
  - På Windows, om TLS 1.0 har inaktiverats explicit och .NET-versionen har inte uppdaterats till 4.6 eller senare, kommer följande registerändring aktivera ADE att välja den nya TLS-versionen:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Grupprincip:**
 - Azure Disk Encryption-lösningen använder de externa nyckelskyddet för BitLocker för Windows virtuella IaaS-datorer. Domänanslutna virtuella datorer, inte skicka någon grupprinciper som tillämpar TPM-skydd. Läs om hur en grupprincip för ”Tillåt BitLocker utan en kompatibel TPM” [gruppolicy referens för BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  BitLocker-principen på domänanslutna virtuella datorer med anpassade Grupprincip måste innehålla följande inställning: [Konfigurera Användarlagring av BitLocker-återställningsinformation > Tillåt 256-bitars återställningsnyckel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption misslyckas när anpassade grupprincipinställningarna för BitLocker är inte kompatibla. Tillämpa den nya principen på datorer som inte har rätt principinställningen tvinga den nya principen för att uppdatera (gpupdate.exe/Force) och sedan omstart kan krävas.  


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) tillhandahåller en uppsättning cmdletar som använder den [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modellen för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](../cloud-shell/overview.md), eller så kan du installera den på den lokala datorn med hjälp av anvisningarna nedan för att använda den i PowerShell-sessioner. Om du redan har installerat lokalt kan du kontrollera att du använder den senaste versionen av Azure PowerShell för att konfigurera Azure Disk Encryption.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Installera Azure PowerShell för användning på den lokala datorn (valfritt):  
1. [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). 

2. Installera den [Azure Active Directory PowerShell-modulen](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Kontrollera de installerade versionerna av moduler.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Logga in på Azure med hjälp av den [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Anslut till Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Granska [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps) och [AzureAD](/powershell/module/azuread), om det behövs.

## <a name="bkmk_CLI"></a> Azure CLI

Den [Azure CLI 2.0](/cli/azure) är ett kommandoradsverktyg för att hantera Azure-resurser. CLI är utformat för att fråga efter data, stöd för långvariga åtgärder som icke-blockerande processer och göra skript enkelt flexibelt. Du kan använda det i webbläsaren med [Azure Cloud Shell](../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner.

1. [Installera Azure CLI](/cli/azure/install-azure-cli) för användning på den lokala datorn (valfritt):

2. Kontrollera den installerade versionen.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Logga in på Azure med [az-inloggning](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Granska [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) om det behövs. 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Nödvändiga arbetsflöde för Key Vault och Azure AD-app

Om du redan är bekant med Key Vault och Azure AD-krav för Azure Disk Encryption kan du använda den [PowerShell-skript för Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Mer information om hur du använder skriptet krav finns i den [kryptera en virtuell dator-Snabbstart](quick-encrypt-vm-powershell.md) och [Azure Disk Encryption bilaga](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Skapa ett nyckelvalv. 
2. Konfigurera en Azure AD-program och tjänstens huvudnamn.
3. Ange nyckelvalvets åtkomstprincip för Azure AD-app.
4. Ange nyckelvalv avancerade åtkomstprinciper.
 
## <a name="bkmk_KeyVault"></a> Skapa ett nyckelvalv 
Azure Disk Encryption är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Du kan skapa ett nyckelvalv eller Använd en befintlig för Azure Disk Encryption. Läs mer om nyckelvalv [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md) och [säkra ditt nyckelvalv](../key-vault/key-vault-secure-your-key-vault.md). Du kan använda en Resource Manager-mall, Azure PowerShell eller Azure CLI för att skapa ett nyckelvalv. 


>[!WARNING]
>För att se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 


### <a name="bkmk_KVPSH"></a> Skapa ett nyckelvalv med PowerShell

Du kan skapa ett nyckelvalv med Azure PowerShell med hjälp av den [New AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet. Ytterligare cmdlets för Key Vault finns [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Skapa en ny resursgrupp, om det behövs, med [New AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Använd om du vill visa en lista över data center platser [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Skapa ett nytt nyckelvalv med [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Obs den **valv namn**, **resursgruppnamn**, **resurs-ID**, **Valvets URI**, och **objekt-ID** som returneras för senare användning när du krypterar diskarna. 


### <a name="bkmk_KVCLI"></a> Skapa ett nyckelvalv med Azure CLI
Du kan hantera ditt nyckelvalv med Azure CLI med hjälp av den [az keyvault](/cli/azure/keyvault#commands) kommandon. Du kan skapa ett nyckelvalv med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create).

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Skapa en ny resursgrupp, om det behövs, med [az gruppen skapa](/cli/azure/group#az-group-create). Använd om du vill visa en lista över platser [az konto list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Skapa ett nytt nyckelvalv med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Obs den **Valvnamnet** (namn), **Resursgruppsnamn**, **resurs-ID** (ID), **Valvets URI**, och **objekt-ID** som returneras för användning senare. 

### <a name="bkmk_KVRM"></a> Skapa ett nyckelvalv med en Resource Manager-mall

Du kan skapa ett nyckelvalv med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.
2. Välj den prenumeration, resursgrupp, plats för resursgruppen, Key Vault namn, objekt-ID, juridiska villkor och avtal och klickar sedan på **köp**. 


## <a name="bkmk_ADapp"></a> Ställa in en Azure AD-app och tjänstens huvudnamn 
När du behöver kryptering är aktiverat på en virtuell dator som körs i Azure, Azure Disk Encryption genererar och skriver dem till ditt nyckelvalv. Hantera krypteringsnycklar i ditt nyckelvalv kräver Azure AD-autentisering. Skapa ett Azure AD-program för detta ändamål. Du kan använda antingen autentisering med klient-hemlighet för autentisering, eller [klientautentisering certifikatbaserad Azure AD](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Ställa in en Azure AD-app och tjänstens huvudnamn med Azure PowerShell 
För att köra följande kommandon, hämta och använda den [Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2). 

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Använd den [New AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell-cmdlet för att skapa ett Azure AD-program. MyApplicationHomePage och MyApplicationUri kan vara alla värden som du vill.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication.ApplicationId är Azure AD-ClientID och $aadClientSecret är den klienthemlighet som du ska använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemlighet på rätt sätt. Kör `$azureAdApplication.ApplicationId` visar ApplicationID.


### <a name="bkmk_ADappCLI"></a> Ställa in en Azure AD-app och tjänstens huvudnamn med Azure CLI

Du kan hantera din tjänstens huvudnamn med Azure CLI med hjälp av den [az ad sp](/cli/azure/ad/sp) kommandon. Mer information finns i [och skapa en Azure-tjänstens huvudnamn](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Skapa ett nytt huvudnamn för tjänsten.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  AppId returnerade är Azure AD-ClientID som används i andra kommandon. Det är också det SPN som du ska använda för az keyvault set-policy. Lösenordet är den klienthemlighet som du ska använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemlighet på rätt sätt.
 
### <a name="bkmk_ADappRM"></a> Konfigurera en Azure AD-app och tjänsten huvudnamn genom Azure-portalen
Använd stegen från den [Använd portalen för att skapa en Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md) artikeln för att skapa ett Azure AD-program. Varje steg nedan leder dig direkt till artikelavsnittet för att slutföra. 

1. [Kontrollera behörigheter som krävs](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Du kan använda ett namn och inloggnings-URL som du vill ha när du skapar programmet.
3. [Hämta program-ID och autentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Autentiseringsnyckeln är klienthemligheten och används som AadClientSecret för Set-AzVMDiskEncryptionExtension. 
        - Nyckeln används av programmet som en autentiseringsuppgift för att logga in på Azure AD. Den här hemligheten kallas nycklar i Azure-portalen, men har ingen relation till nyckelvalv. Skydda den här hemligheten på rätt sätt. 
     - Program-ID används senare som AadClientId för Set-AzVMDiskEncryptionExtension och som ServicePrincipalName för Set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Ange nyckelvalvets åtkomstprincip för Azure AD-app
Om du vill skriva kryptering hemligheter angivna Key Vault, måste Azure Disk Encryption klient-ID och Klienthemlighet för Azure Active Directory-program som har behörighet att skriva hemligheter i nyckelvalvet. 

> [!NOTE]
> Azure Disk Encryption måste du konfigurera följande principer för åtkomst till ditt Azure AD-klientprogram: _WrapKey_ och _ange_ behörigheter.

### <a name="bkmk_KVAPPSH"></a> Ange nyckelvalvets åtkomstprincip för Azure AD-app med Azure PowerShell
Azure AD-program behöver behörighet att komma åt nycklar eller hemligheter i valvet. Använd den [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet för att bevilja behörighet för programmet med klient-ID (som skapades när programmet registrerades) som den _– ServicePrincipalName_ parametervärdet. Mer information finns i bloggposten [Azure Key Vault - steg-för-steg](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Ange nyckelvalvets åtkomstprincip för AD-program med PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> Ange nyckelvalvets åtkomstprincip för Azure AD-app med Azure CLI
Använd [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) att ställa in åtkomstprincipen. Mer information finns i [hantera Key Vault med CLI 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Ge tjänstens huvudnamn som du skapade via Azure CLI-åtkomst att hämta hemligheter och radbyte nycklar med följande kommando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Ange nyckelvalvets åtkomstprincip för Azure AD-app med portalen

1. Öppna resursgruppen med ditt nyckelvalv.
2. Välj ditt nyckelvalv, gå till **åtkomstprinciper**, klicka sedan på **Lägg till ny**.
3. Under **väljer huvudnamn**, Sök efter Azure AD-program som du har skapat och markera den. 
4. För **Nyckelbehörigheter**, kontrollera **Wrap Key** under **kryptografiska åtgärder**.
5. För **hemliga behörigheter**, kontrollera **ange** under **hemlighet hanteringsåtgärder**.
6. Klicka på **OK** att spara åtkomstprincipen. 

![Azure Key Vault kryptografiska åtgärder - Wrap Key](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Ange behörigheter för Azure Key Vault-hemlighet-](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Ställ in avancerade åtkomstprinciper för nyckelvalvet
Azure-plattformen behöver åtkomst till krypteringsnycklar och hemligheter i ditt nyckelvalv och gör dem tillgängliga för den virtuella datorn för start och dekryptera volymerna. Aktivera diskkryptering på nyckelvalvet eller distributioner kommer att misslyckas.  

### <a name="bkmk_KVperPSH"></a> Ställ in avancerade åtkomstprinciper med Azure PowerShell för nyckelvalvet
 Använd key vault PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) vill aktivera diskkryptering för key vault.

  - **Aktivera Key Vault för diskkryptering:** EnabledForDiskEncryption krävs för Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution, om det behövs:** Gör det möjligt för Microsoft.Compute-resursprovidern att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet är refererad i resursskapande, till exempel när du skapar en virtuell dator.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Aktivera Key Vault för malldistribution, om det behövs:** Gör det möjligt för Azure Resource Manager för att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet är refereras till i en för malldistribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Ställ in avancerade åtkomstprinciper med hjälp av Azure CLI för nyckelvalvet
Använd [az keyvault update](/cli/azure/keyvault#az-keyvault-update) vill aktivera diskkryptering för key vault. 

 - **Aktivera Key Vault för diskkryptering:** Aktiverad-för-disk-kryptering krävs. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution, om det behövs:** Tillåta att virtuella datorer att hämta certifikat som lagras som hemligheter från valvet.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault för malldistribution, om det behövs:** Tillåt Resource Manager för att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Ställ in avancerade åtkomstprinciper via Azure portal för nyckelvalvet

1. Välj din keyvault, gå till **åtkomstprinciper**, och **Klicka om du vill visa avancerade åtkomstprinciper**.
2. Markera rutan **ge åtkomst till Azure Disk Encryption för volymkryptering**.
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution av** och/eller **Aktivera åtkomst till Azure Resource Manager för malldistribution**, om det behövs. 
4. Klicka på **Spara**.

![Azure-nyckelvalv avancerade åtkomstprinciper](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Konfigurera en nyckelkrypteringsnyckel (valfritt)
Om du vill använda en krypteringsnyckel nyckel (KEK) för ett extra lager av säkerhet för krypteringsnycklar, lägger du till en KEK till ditt nyckelvalv. Använd den [Lägg till AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet för att skapa en nyckelkrypteringsnyckel i nyckelvalvet. Du kan också importera en KEK från din lokala nyckelhantering HSM. Mer information finns i [dokumentation om Key Vault](../key-vault/key-vault-hsm-protected-keys.md). När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. 

* Använd en RSA-nyckeltyp vid generering av nycklar. Azure Disk Encryption stöder ännu inte med Elliptic Curve nycklar.

* Din hemlighet i nyckelvalvet och KEK URL: er måste vara en ny version. Azure tillämpar den här begränsningen för versionshantering. Giltigt hemlighet och KEK URL: er finns i följande exempel:

  * Exempel på en giltig hemlig URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig KEK-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption stöder inte att ange portnummer som en del av key vault-hemligheter och KEK URL: er. Exempel på URL: er inte stöds och stöds key vault finns i följande exempel:

  * Oacceptabel key vault-Webbadress  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Godkända key vault-Webbadress:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Konfigurera en nyckelkrypteringsnyckel med Azure PowerShell 
Innan du använder PowerShell-skriptet, bör du känna till kraven för Azure Disk Encryption att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö. Det här skriptet skapar alla krav för Azure Disk Encryption och krypterar en befintlig IaaS VM, wrapping disk-krypteringsnyckeln med hjälp av en nyckel krypteringsnyckel. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Certifikatbaserad autentisering (valfritt)
Om du vill använda autentisering med certifikat, kan du ladda upp en till ditt nyckelvalv och distribuera den till klienten. Innan du använder PowerShell-skriptet, bör du känna till kraven för Azure Disk Encryption att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Certifikatbaserad autentisering och en KEK (valfritt)

Om du vill använda autentisering med certifikat och omsluter krypteringsnyckeln med en KEK, kan du använda den som en exempel-skriptet nedan. Innan du använder PowerShell-skriptet, bör du känna till alla tidigare Azure Disk Encryption-krav för att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö.

> [!IMPORTANT]
> Azure AD-certifikatbaserad autentisering stöds för närvarande inte på virtuella Linux-datorer.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux-aad.md)
