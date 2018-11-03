---
title: Verifiera Azure Stack Public Key Infrastructure-certifikat för distribution av integrerade Azure Stack-system | Microsoft Docs
description: Beskriver hur du verifierar Azure Stack PKI-certifikat för integrerade Azure Stack-system. Beskriver med hjälp av verktyget Azure Stack-certifikat för installation.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 51c76c552ef3a79f48637598a12f7a7be1823996
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961134"
---
# <a name="validate-azure-stack-pki-certificates"></a>Verifiera Azure Stack PKI-certifikat

Verktyget Azure Stack-beredskap för installation som beskrivs i den här artikeln är tillgänglig [från PowerShell-galleriet](https://aka.ms/AzsReadinessChecker). Du kan använda verktyget för att kontrollera att den [genereras PKI-certifikat](azure-stack-get-pki-certs.md) är lämpliga för distributionen. Du bör verifiera certifikat genom att lämna tillräckligt med tid för att testa och återutfärda certifikat om det behövs.

Beredskap för installation utförs följande verifieringar för certifikat:

- **Läsa PFX**  
    Söker efter giltiga PFX-fil, rätt lösenord, och varnar om offentlig information inte skyddas av lösenordet. 
- **Signaturalgoritm**  
    Kontrollerar att signaturalgoritmen inte är SHA1.
- **Privat nyckel**  
    Kontrollerar att den privata nyckeln finns och exporteras med attributet lokala datorn. 
- **Kedja**  
    Kontrollerar certifikatkedjan är intakt, inklusive en kontroll för självsignerade certifikat.
- **DNS-namn**  
    Kontrollerar om SAN-nätverket innehåller DNS-namnen för varje slutpunkt eller om en stöder jokertecken finns.
- **Nyckelanvändning**  
    Kontrollerar om nyckelanvändningen innehåller en digital signatur och nyckelchiffrering och förbättrad nyckelanvändning innehåller serverautentisering och klientautentisering.
- **Nyckelstorlek**  
    Kontrollerar om nyckelstorleken är 2048 eller större.
- **Kedjans ordning**  
    Kontrollerar ordningen på de andra certifikat som verifierar att ordningen är korrekt.
- **Andra certifikat**  
    Se till att inga andra certifikat har paketerats i PFX än det relevanta lövcertifikatet och kedjan.
- **Ingen profil**  
    Kontrollerar att en ny användare kan läsa in PFX data utan en användarprofil som har lästs in, frihandsbilden beteendet för gMSA konton under behandlingen av certifikat.

> [!IMPORTANT]  
> PKI-certifikat är en PFX-fil och lösenord ska behandlas som känslig information.

## <a name="prerequisites"></a>Förutsättningar

Systemet måste uppfylla följande krav innan du verifierar PKI-certifikat för distribution av Azure Stack:

- Microsoft Azure Stack-beredskap för installation
- SSL-certifikat exporteras följa den [förberedelse instruktioner](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 eller Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Utföra certifikatsverifiering för core services

Följ dessa steg för att förbereda och kontrollera Azure Stack PKI-certifikat för distribution och hemliga rotation:

1. Installera **AzsReadinessChecker** från en PowerShell-kommandotolk (5.1 eller senare), genom att köra följande cmdlet:

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Skapa katalogstrukturen certifikat. I exemplet nedan kan du ändra `<c:\certificates>` till en ny sökväg för valfri.
    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal','Admin Extension Host','Public Extension Host'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS och Graph krävs om du använder AD FS som ID-system.
    
     - Placera ditt certifikat i lämplig katalogerna som skapades i föregående steg. Exempel:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. I PowerShell-fönstret Ändra värdena för **RegionName** och **FQDN** lämpliga i Azure Stack-miljön och kör sedan följande:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Kontrollera utdata och certifikat klara alla tester. Exempel:

````PowerShell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
````

### <a name="known-issues"></a>Kända problem

**Symtom**: tester hoppas över

**Orsak**: AzsReadinessChecker hoppar över vissa test om ett beroende som inte är uppfyllt:

 - Andra certifikat hoppas över om certifikatkedja misslyckas.

    ```PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Lösning**: följer du anvisningarna i verktyget i informationsavsnittet under varje uppsättning med tester för varje certifikat.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Utför plattform som en tjänst-certifikatverifiering

Följ dessa steg för att förbereda och kontrollera Azure Stack PKI-certifikat för plattform som en tjänst (PaaS)-certifikat, om SQL/MySQL eller App Services distributioner planeras.

1.  Installera **AzsReadinessChecker** från en PowerShell-kommandotolk (5.1 eller senare), genom att köra följande cmdlet:

    ```PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Skapa en kapslad hash-tabell som innehåller sökvägar och lösenord för att varje PaaS-certifikat som behöver verifiering. I PowerShell-fönstret som kör:

    ```PowerShell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Ändra värdena för **RegionName** och **FQDN** så att de matchar Azure Stack-miljön om du vill starta verifieringen. Kör sedan:

    ```PowerShell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Kontrollera att utdata och att alla certifikat klara alla tester.

    ```PowerShell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Med verifierade certifikat

När dina certifikat har verifierats av AzsReadinessChecker, är du redo att använda dem i Azure Stack-distribution eller Hemlig rotation av Azure Stack. 

 - För distribution på ett säkert sätt överföra dina certifikat till din distribution-tekniker så att de kan kopiera dem till distribution värden som anges i den [Azure Stack PKI-kravdokumentationen](azure-stack-pki-certs.md).
 - För hemliga rotation, kan du använda certifikaten för att uppdatera gamla certifikat för Azure Stack-miljön infrastruktur för offentliga slutpunkter genom att följa den [dokumentation om Azure Stack hemlighet Rotation](azure-stack-rotate-secrets.md).
 - För PaaS-tjänster, kan du använda certifikat för att installera SQL, MySQL och Resursprovidrar för App Services i Azure Stack genom att följa den [översikt över erbjudna tjänster i Azure Stack-dokumentationen](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Nästa steg

[Datacenter-identitetsintegrering](azure-stack-integrate-identity.md)
