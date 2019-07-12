---
title: Azure Disk Encryption för Linux | Microsoft Docs
description: Distribuerar Azure Disk Encryption för Linux till en virtuell dator med tillägg för virtuell dator.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: d544aae33faf60be00a2b4ea0a45f405efcedb39
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706146"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption för Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar undersystemet dm-crypt i Linux för att ge fullständig diskkryptering på [väljer Azure Linux-distributioner](https://aka.ms/adelinux).  Den här lösningen är integrerad med Azure Key Vault för att hantera diskkrypteringsnycklarna och hemligheterna.

## <a name="prerequisites"></a>Förutsättningar

En fullständig lista över krav, se [krävs för Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operativsystem

Azure Disk Encryption stöds för närvarande på väljer distributioner och versioner.  Se den [Azure Disk Encryption operativsystem som stöds: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) lista över Linux-distributioner som stöds.

### <a name="internet-connectivity"></a>Internetanslutning

Azure Disk Encryption för Linux kräver en Internetanslutning för åtkomst till Active Directory, Key Vault, lagring och hanteringsslutpunkter för paketet.  Mer information finns i [krävs för Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Tillägget scheman

Det finns två scheman för Azure Disk Encryption: v1.1, ett nyare, rekommenderas schema som inte använder Azure Active Directory (AAD)-egenskaper och v0.1, ett äldre schema som kräver AAD egenskaper. Du måste använda schemaversion som motsvarar det tillägget som du använder: schemat v1.1 för azurediskencryptionforlinux har lagts tilläggsversion 1.1, schemat v0.1 för azurediskencryptionforlinux har lagts tilläggsversion 0.1.
### <a name="schema-v11-no-aad-recommended"></a>Schemat v1.1: Ingen AAD (rekommenderas)

V1.1 schemat bör och kräver inte Azure Active Directory-egenskaper.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Schemat v0.1: med AAD 

0,1 schemat kräver `aadClientID` och antingen `aadClientSecret` eller `AADClientCertificate`.

Med hjälp av `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Med hjälp av `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | sträng |
| type | AzureDiskEncryptionForLinux | sträng |
| typeHandlerVersion | 0.1, 1.1 | int |
| (0,1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0,1 schema) AADClientSecret | password | sträng |
| (0,1 schema) AADClientCertificate | thumbprint | sträng |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-ordlista |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | sträng |
| KeyEncryptionKeyURL | url | sträng |
| (valfritt) KeyVaultURL | url | sträng |
| Passphrase | password | sträng | 
| SequenceVersion | uniqueidentifier | sträng |
| VolumeType | OS, Data, All | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på för malldistribution, se [Aktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Anvisningar finns i senast [Azure CLI-dokumentationen](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Felsökning, finns det [felsökningsguide för Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns i [virtuella datorer, tillägg och funktioner i Linux](features-linux.md).