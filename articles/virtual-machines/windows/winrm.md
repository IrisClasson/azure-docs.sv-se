---
title: Konfigurera WinRM-åtkomst för en virtuell Azure-dator | Microsoft Docs
description: Konfigurera WinRM-åtkomst för användning med en virtuell Azure-dator som skapats i distributions modellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: f7f57a43697a9376062bdd3baa2d5f7333bf4a7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100163"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Konfigurera WinRM-åtkomst för Virtual Machines i Azure Resource Manager

Här är de steg du måste vidta för att konfigurera en virtuell dator med WinRM-anslutning

1. Skapa en Key Vault-lösning
2. Skapa ett självsignerat certifikat
3. Ladda upp ditt självsignerade certifikat till Key Vault
4. Hämta URL: en för ditt självsignerade certifikat i Key Vault
5. Referera till din självsignerade certifikat-URL när du skapar en virtuell dator

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>Steg 1: Skapa en Key Vault-lösning
Du kan använda kommandot nedan för att skapa Key Vault

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Steg 2: Skapa ett självsignerat certifikat
Du kan skapa ett självsignerat certifikat med hjälp av det här PowerShell-skriptet

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Steg 3: Ladda upp ditt självsignerade certifikat till Key Vault
Innan du laddar upp certifikatet till Key Vault som skapades i steg 1, måste det konverteras till ett format som Microsoft. Compute Resource-providern kommer att förstå. Med PowerShell-skriptet nedan kan du göra det

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Steg 4: Hämta URL: en för ditt självsignerade certifikat i Key Vault
Providern Microsoft. Compute Resource måste ha en URL till hemligheten i Key Vault när den virtuella datorn har skapats. Detta gör att Microsoft. Compute Resource-providern kan hämta hemligheten och skapa motsvarande certifikat på den virtuella datorn.

> [!NOTE]
> URL: en för hemligheten måste även innehålla-versionen. En exempel-URL ser ut som under\/https:/contosovault.Vault.Azure.net:443/Secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Mallar
Du kan hämta länken till URL: en i mallen med hjälp av koden nedan

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Du kan hämta den här URL: en med hjälp av PowerShell-kommandot nedan

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Steg 5: Referera till din självsignerade certifikat-URL när du skapar en virtuell dator
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager mallar
När du skapar en virtuell dator med hjälp av mallar, hämtas certifikatet i avsnittet hemligheter och winRM enligt nedan:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

En exempel mall för ovanstående finns här på [201-VM-WinRM-nyckel valv-Windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Du hittar käll koden för den här mallen på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Steg 6: Ansluter till den virtuella datorn
Innan du kan ansluta till den virtuella datorn måste du kontrol lera att datorn har kon figurer ATS för WinRM-fjärrhantering. Starta PowerShell som administratör och kör kommandot nedan för att kontrol lera att du har konfigurerat.

    Enable-PSRemoting -Force

> [!NOTE]
> Du kan behöva kontrol lera att WinRM-tjänsten körs om ovanstående inte fungerar. Du kan göra det med hjälp av`Get-Service WinRM`
> 
> 

När installationen är färdig kan du ansluta till den virtuella datorn med kommandot nedan

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
