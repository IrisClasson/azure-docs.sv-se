---
title: Med hjälp av MySQL-databaser på Azure Stack | Microsoft Docs
description: Lär dig hur du kan distribuera MySQL-databaser som en tjänst på Azure Stack och snabba steg för att distribuera MySQL-Server-resursprovideradaptern.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: ca924494231f422d6edc8990ef851c9ef454cd1d
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120010"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Distribuera MySQL-resursprovider i Azure Stack

Använd MySQL Server-resursprovidern för att exponera MySQL-databaser som en Azure Stack-tjänst. MySQL-resursprovider körs som en tjänst på en Windows Server 2016 Server Core-dator (VM).

> [!IMPORTANT]
> Endast resursprovidern stöds för att skapa objekt på servrar som värd SQL eller MySQL. Objekt som har skapats på en värdserver som inte skapats med resursprovidern kan resultera i ett felaktigt tillstånd.

## <a name="prerequisites"></a>Förutsättningar

Det finns flera förutsättningar som måste vara uppfyllda innan du kan distribuera Azure Stack MySQL-resursprovider. Slutför stegen i den här artikeln på en dator som har åtkomst till privilegierad slutpunkt virtuell dator som uppfyller dina krav.

* Om du inte redan gjort det, [registrera Azure Stack](./azure-stack-registration.md) med Azure så att du kan ladda ned Azure marketplace-objekt.
* Du måste installera Azure- och Azure Stack PowerShell-moduler på systemet där du kör installationen. Systemet måste vara en Windows 10 eller Windows Server 2016-avbildning med den senaste versionen av .NET-runtime. Se [installera PowerShell för Azure Stack](./azure-stack-powershell-install.md).
* Lägg till nödvändiga Windows Server core VM på Azure Stack Marketplace genom att ladda ned den **Windows Server 2016 Datacenter - Server Core** bild.

* Ladda ned MySQL-resursprovidern binära och kör sedan Self-Extractor för att extrahera innehållet till en tillfällig katalog.

  >[!NOTE]
  >Om du vill distribuera MySQL-providern på ett system som inte har tillgång till Internet, kopiera den [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) filen till en lokal sökväg. Ange en sökväg namn med hjälp av den **DependencyFilesLocalPath** parametern.

* Resursprovidern har en minsta motsvarande Azure Stack skapa.

  |Lägsta version av Azure Stack|MySQL RP-version|
  |-----|-----|
  |Version 1811 (1.1811.0.101)|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Version 1808 (1.1808.0.97)|[MySQL RP version 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Version 1804 (1.0.180513.1)|[MySQL RP version 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Kontrollera att datacenter integration krav är uppfyllda:

    |Krav|Referens|
    |-----|-----|
    |Villkorlig vidarebefordran av DNS är korrekt.|[Integrering med Azure Stack datacenter - DNS](azure-stack-integrate-dns.md)|
    |Ingående portar för resursprovider är öppna.|[Azure Stack datacenter-integrering – publicera slutpunkter](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI-certifikatämne och alternativt namn har angetts korrekt.|[Azure Stack obligatoriska PKI distributionskraven](azure-stack-pki-certs.md#mandatory-certificates)[Azure Stack distributionskraven PaaS-certifikat](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certifikat

_För integrerade system installationer endast_. Du måste ange SQL PaaS PKI-certifikat som beskrivs i den valfria PaaS certifikat i [Azure Stack PKI distributionskrav](./azure-stack-pki-certs.md#optional-paas-certificates). Placera den .pfx-fil på den plats som den **DependencyFilesLocalPath** parametern. Ange inte ett certifikat för ASDK system.

## <a name="deploy-the-resource-provider"></a>Distribuera resursprovidern

När du har allt som krävs installerat kan köra den **DeployMySqlProvider.ps1** skript för att distribuera MYSQL-resursprovider. Skriptet DeployMySqlProvider.ps1 extraheras som en del av MySQL resource provider binärfilen som du hämtade för din version av Azure Stack.

Öppna en ny upphöjd PowerShell-fönster (inte PowerShell ISE) för att distribuera MySQL-resursprovidern, och ändra till den katalog där du extraherade de binära filerna för MySQL resource provider. Vi rekommenderar att du använder ett nytt PowerShell-fönster för att undvika potentiella problem som orsakas av PowerShell-moduler som redan har lästs in.

Kör den **DeployMySqlProvider.ps1** skript som utför följande aktiviteter:

* Överför certifikaten och andra artefakter till ett lagringskonto på Azure Stack.
* Publicerar gallery-paket så att du kan distribuera MySQL-databaser med galleriet.
* Publicerar ett gallery-paket för distribution av värdservrar.
* Distribuerar en virtuell dator med Windows Server 2016 core avbildningen du hämtade och installerar sedan MySQL-resursprovider.
* Registrerar en lokal DNS-post som mappar till dina VM-resursprovidern.
* Registrerar din resursprovidern med den lokala Azure Resource Manager för operatorn-konto.

> [!NOTE]
> När MySQL resource provider distributionen startar, **system.local.mysqladapter** resursgruppen har skapats. Det kan ta upp till 75 minuter att slutföra de distributioner som krävs för att den här resursgruppen.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parametrar

Du kan ange dessa parametrar från kommandoraden. Om du inte, eller om någon parameter-valideringen misslyckas, uppmanas du att ange de obligatoriska parametrarna.

| Parameternamn | Beskrivning | Kommentar eller standardvärde |
| --- | --- | --- |
| **CloudAdminCredential** | Autentiseringsuppgifter för molnadministratören behövs för att komma åt den privilegierade slutpunkten. | _Krävs_ |
| **AzCredential** | Autentiseringsuppgifter för Azure Stack-tjänstadministratörskonto. Använd samma autentiseringsuppgifter som du använde för att distribuera Azure Stack. | _Krävs_ |
| **VMLocalCredential** | Autentiseringsuppgifterna för det lokala administratörskontot för MySQL-resursprovider VM. | _Krävs_ |
| **PrivilegedEndpoint** | IP-adressen eller DNS-namnet på den privilegierade slutpunkten. |  _Krävs_ |
| **AzureEnvironment** | Azure-miljön för admin kontot som du använde för att distribuera Azure Stack. Krävs endast för Azure AD-distributioner. Miljö som stöds är **AzureCloud**, **azureusgovernment eller**, eller om du använder en Kina Azure AD, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | För integrerade system, måste din .pfx-certifikatfil placeras i den här katalogen. Frånkopplade enviroments hämta [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) till den här katalogen. Du kan också kopiera här ett Windows Update MSU-paket. | _Valfritt_ (_obligatoriska_ för integrerade system eller frånkopplade miljöer) |
| **DefaultSSLCertificatePassword** | Lösenordet för PFX-certifikat. | _Krävs_ |
| **MaxRetryCount** | Antal gånger som du vill försöka utföra varje åtgärd om det uppstår ett fel.| 2 |
| **RetryDuration** | Timeout-intervall mellan försök i sekunder. | 120 |
| **Avinstallera** | Tar bort resursprovidern och alla associerade resurser (se nedan). | Nej |
| **DebugMode** | Förhindrar automatisk rensning vid fel. | Nej |
| **AcceptLicense** | Hoppar över uppmaningen om att acceptera licensen GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Distribuera MySQL-resursprovidern med ett anpassat skript

Du kan anpassa följande skript för att minimera ingen manuell konfiguration när du distribuerar resursprovidern måste. Ändra standard-kontoinformation och lösenord som behövs för din Azure Stack-distribution.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Uppdatera webbläsaren för att kontrollera att du kan se de senaste uppdateringarna när installationsskriptet resource provider är klar.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Kontrollera distributionen med hjälp av Azure Stack-portalen

1. Logga in på administrationsportalen för som tjänstadministratör.
2. Välj **resursgrupper**
3. Välj den **system.\< plats\>.mysqladapter** resursgrupp.
4. På sidan Sammanfattning för resursgruppen översikt bör det finnas inga misslyckade distributioner.
5. Välj slutligen **virtuella datorer** på administrationsportalen för att verifiera att MySQL-resursprovider VM har skapats och körs.

## <a name="next-steps"></a>Nästa steg

[Lägg till värdservrar](azure-stack-mysql-resource-provider-hosting-servers.md)
