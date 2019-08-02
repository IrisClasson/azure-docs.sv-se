---
title: Tillägg – Azure Disk Encryption för virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln är tillägget för Microsoft Azure Disk Encryption för Windows och Linux IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 7cbddc4b7af546396a1a5a4c86d349a96054a6f3
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726276"
---
# <a name="appendix-for-azure-disk-encryption"></a>Tillägg för Azure Disk Encryption 

Den här artikeln är ett tillägg till [Azure Disk Encryption för virtuella IaaS-datorer](azure-security-disk-encryption-overview.md). Se till att läsa Azure Disk Encryption för virtuella IaaS-datorer artiklar först för att förstå kontexten. Den här artikeln beskriver hur du förbereder förkrypterade virtuella hårddiskar och andra uppgifter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Ansluta till din prenumeration
Innan du börjar bör du granska den [krav](azure-security-disk-encryption-prerequisites.md) artikeln. När alla krav har uppfyllts kan du ansluta till din prenumeration genom att köra följande cmdlets:

### <a name="bkmk_ConnectPSH"></a> Ansluta till din prenumeration med PowerShell

1. Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

     ```powershell
     Connect-AzAccount 
     ```
2. Om du har flera prenumerationer och vill ange jag ska använda, skriver du följande för att visa prenumerationerna för ditt konto:
     
     ```powershell
     Get-AzSubscription
     ```
3. Om du vill ange den prenumeration som du vill använda, skriver du:
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Om du vill kontrollera att den prenumeration som har konfigurerats är korrekt, skriver du:
     
     ```powershell
     Get-AzSubscription
     ```
5. Om det behövs kan ansluta till Azure AD med [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. För att bekräfta Azure Disk Encryption-cmdletar har installerats, skriver du:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Granska [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps) och [AzureAD](/powershell/module/azuread), om det behövs.

### <a name="bkmk_ConnectCLI"></a> Ansluta till din prenumeration med Azure CLI

1. Logga in på Azure med [az-inloggning](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Om du vill välja en klient att logga in under Använd:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Om du har flera prenumerationer och vill ange en kan hämta din prenumerationslista med [az kontolista](/cli/azure/account#az-account-list) och ange med [az-kontogrupper](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Kontrollera den installerade versionen.
     
     ```azurecli
        az --version
     ``` 

5. Granska [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) om det behövs. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-exempelskript för Azure Disk Encryption 

- **Lista över alla krypterade virtuella datorer i din prenumeration**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Lista över alla disk encryption-hemligheter som används för att kryptera virtuella datorer i ett nyckelvalv** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Med hjälp av PowerShell-skript för Azure Disk Encryption krav
Om du redan är bekant med kraven för Azure Disk Encryption kan du använda den [PowerShell-skript för Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Ett exempel på hur du använder det här PowerShell-skript finns i den [kryptera en virtuell dator-Snabbstart](azure-disk-encryption-linux-powershell-quickstart.md). Du kan ta bort kommentarerna från en del av skriptet, med början på rad 211, att kryptera alla diskar för befintliga virtuella datorer i en befintlig resursgrupp. 

I följande tabell visas vilka parametrar som kan användas i PowerShell-skriptet: 


|Parameter|Beskrivning|Är obligatoriskt|
|------|------|------|
|$resourceGroupName| Namnet på resursgruppen som Nyckelvalvet tillhör.  En ny resursgrupp med det här namnet kommer att skapas om det inte finns.| True|
|$keyVaultName|Namnet på Nyckelvalvet där krypteringsnycklarna nycklar är placeras. Ett nytt valv med det här namnet kommer att skapas om det inte finns.| True|
|$location|Platsen för Nyckelvalvet. Kontrollera att Nyckelvalvet och virtuella datorer som ska krypteras finns på samma plats. Hämta en innehållsplatslista med `Get-AzLocation`.|True|
|$subscriptionId|Identifierare för Azure-prenumeration som ska användas.  Du kan hämta ditt prenumerations-ID med `Get-AzSubscription`.|True|
|$aadAppName|Namnet på Azure AD-programmet som ska användas för att skriva hemligheter KeyVault. Om det inte redan finns ett program med det namnet skapas ett nytt. Om den här appen finns redan, ange du aadClientSecret parameter till skriptet.|False|
|$aadClientSecret|Klienthemlighet för Azure AD-programmet som du skapade tidigare.|False|
|$keyEncryptionKeyName|Namnet på valfri nyckelkrypteringsnyckel i KeyVault. En ny nyckel med det här namnet kommer att skapas om det inte finns.|False|


## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Kryptera eller dekryptera virtuella datorer utan en Azure AD-app


- [Aktivera diskkryptering på befintliga eller som kör Windows virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Inaktivera diskkryptering på befintliga eller som kör Windows virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Aktivera diskkryptering på en befintlig eller körs IaaS Linux virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
  - [Inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.  

### <a name="encrypt-or-decrypt-virtual-machine-scale-sets"></a>Kryptera eller dekryptera skalnings uppsättningar för virtuella datorer

- [Aktivera diskkryptering på en som kör Linux VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Aktivera diskkryptering på en aktiv Windows VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Distribuera en virtuell dators skalnings uppsättning med virtuella Linux-datorer med en hopp och möjliggör kryptering på Linux-VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Distribuera en virtuell dators skalnings uppsättning med virtuella Windows-datorer med en hopp och möjliggör kryptering på Windows-VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Inaktivera diskkryptering på en som kör Linux VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Inaktivera diskkryptering på en aktiv Windows VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Kryptera eller dekryptera virtuella datorer med en Azure AD-app (tidigare version) 
 
- [Aktivera diskkryptering på befintliga eller som kör Windows virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Aktivera diskkryptering på en befintlig eller körs IaaS Linux virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Inaktivera diskkryptering på som kör Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer. 

- [Aktivera diskkryptering på nya virtuella IaaS Windows-dator från Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Den här mallen skapar en ny krypterade Windows virtuell dator som använder Windows Server 2012 galleriet bilden.

- [Skapa en ny krypterade Windows IaaS Managed Disk virtuell dator från galleriet bilden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Den här mallen skapar en ny krypterade Windows virtuell dator med hanterade diskar som använder galleriavbildning för Windows Server 2012.

- [Skapa en ny krypterade hanterad disk från en förkrypterade VHD-/ lagringsblob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Skapar en ny krypterade hanterad disk som en förkrypterade virtuell Hårddisk och dess motsvarande krypteringsinställningar

- [Aktivera diskkryptering på en Windows virtuell dator som körs med hjälp av en Azure AD Klientcertifikatets tumavtryck](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Förbered en förkrypterade Windows virtuell Hårddisk
Avsnitten som följer är nödvändiga för att förbereda en förkrypterade Windows virtuell Hårddisk för distribution som en krypterad virtuell Hårddisk i Azure IaaS. Använd informationen för att förbereda och starta en ny Windows VM (VHD) på Azure Site Recovery eller Azure. Mer information om hur du förbereder och ladda upp en virtuell Hårddisk finns i [överföra en generaliserad virtuell Hårddisk och använda den för att skapa nya virtuella datorer i Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Uppdatera grupprincipen för att tillåta utan TPM för OS-skydd
Konfigurera grupprincipinställningen BitLocker **BitLocker-diskkryptering**, som du hittar **lokal datorprincip** > **Datorkonfiguration**  >  **Administrationsmallar** > **Windows-komponenter**. Ändra inställningen till **operativsystemsenheter** > **kräver ytterligare autentisering vid start** > **Tillåt BitLocker utan en kompatibel TPM**, enligt följande bild:

![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installera komponenter för BitLocker-funktion
För Windows Server 2012 och senare, använder du följande kommando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

För Windows Server 2008 R2, använder du följande kommando:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Förbereda operativsystemvolymen för BitLocker med hjälp av `bdehdcfg`
Om du vill komprimera OS-partition och förbereda datorn för BitLocker, köra den [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) om det behövs:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Skydda systemvolymen med hjälp av BitLocker
Använd den [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) kommando för att aktivera kryptering på startvolymen med hjälp av en extern nyckelskydd. Placera även den externa nyckeln (.bek-fil) på extern enhet eller volym. Kryptering är aktiverat på system-/ startvolymen efter nästa omstart.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Förbered den virtuella datorn med en separat data/resurs VHD för att hämta den externa nyckeln med hjälp av BitLocker.

## <a name="bkmk_LinuxRunning"></a> Kryptera en OS-enhet på en som kör Linux VM

### <a name="prerequisites-for-os-disk-encryption"></a>Krav för OS-diskkryptering

* Den virtuella datorn måste använda en distribution som är kompatibel med disk kryptering för operativ system [som anges i Azure Disk Encryption operativ system som stöds: Linux](azure-security-disk-encryption-prerequisites.md#linux) 
* Den virtuella datorn måste skapas från Marketplace-avbildning i Azure Resource Manager.
* Azure virtuell dator med minst 4 GB RAM (rekommenderas är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Om du vill inaktivera SELinux, se ”4.4.2. Inaktivera SELinux ”i den [SELinux användar- och Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* Starta om den virtuella datorn på minst en gång när du har inaktiverat SELinux.

### <a name="steps"></a>Steg
1. Skapa en virtuell dator med någon av de distributioner som angavs tidigare.

   OS-diskkryptering stöds för 7.2 CentOS, via en särskild avbildning. Om du vill använda den här bilden anger du ”7.2n” som SKU: N när du skapar den virtuella datorn:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurera den virtuella datorn efter dina behov. Om du planerar att kryptera alla (OS + data) enheter finns i dataenheter måste vara angivna och monteras från/etc/fstab.

   > [!NOTE]
   > Använd UUID =... Ange dataenheter för i/etc/fstab istället för att ange blockera enhetens namn (till exempel/dev/sdb1). Ordningen på enheter ändras på den virtuella datorn under krypteringen. Om den virtuella datorn är beroende av en viss ordning av blockenheterna, misslyckas den att montera dem efter kryptering.

3. Logga ut från SSH-sessioner.

4. För att kryptera Operativsystemet, ange volumeType som **alla** eller **OS** när du aktiverar kryptering.

   > [!NOTE]
   > Alla Användarutrymmet processer som inte körs som `systemd` tjänster ska avslutas med en `SIGKILL`. Starta om den virtuella datorn. När du aktiverar OS-diskkryptering på en aktiv virtuell dator kan du planera på stilleståndstid på virtuella datorer.

5. Övervaka förloppet för kryptering med jämna mellanrum med hjälp av anvisningarna i den [nästa avsnitt](#monitoring-os-encryption-progress).

6. När get-AzVmDiskEncryptionStatus visar "VMRestartPending" startar du om den virtuella datorn antingen genom att logga in på den eller med hjälp av portalen, PowerShell eller CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Innan du startar om rekommenderar vi att du sparar [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) för den virtuella datorn.

## <a name="monitoring-os-encryption-progress"></a>Övervaka förloppet för OS-kryptering
Du kan övervaka förloppet för OS-kryptering på tre sätt:

* Använd den `Get-AzVmDiskEncryptionStatus` cmdlet och inspektera fältet ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  När den virtuella datorn når ”OS diskkryptering igång”, det tar cirka 40 till 50 minuter på en Premium-lagring säkerhetskopieras VM.

  Grund av [utfärda #388](https://github.com/Azure/WALinuxAgent/issues/388) i WALinuxAgent, `OsVolumeEncrypted` och `DataVolumesEncrypted` visas som `Unknown` i vissa distributioner. Med WALinuxAgent version 2.1.5 och senare, det här problemet löses automatiskt. Om du ser `Unknown` utdata och du kan kontrollera status för diskkryptering med hjälp av Azure Resource Explorer.

  Gå till [Azure Resource Explorer](https://resources.azure.com/), och expandera sedan den här hierarkin i panelen för val av vänster:

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  Rulla ned för att se krypteringsstatus för dina enheter i InstanceView.

  ![Instansvy för virtuell dator](./media/azure-security-disk-encryption/vm-instanceview.png)

* Titta på [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från tillägget ADE ska föregås `[AzureDiskEncryption]`.

* Logga in på den virtuella datorn via SSH och hämta tilläggets logg från:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Vi rekommenderar att du inte logga in på den virtuella datorn när OS-kryptering pågår. Kopiera loggarna bara när de två metoderna har misslyckats.

## <a name="bkmk_preLinux"></a> Förbereda en förkrypterade Linux-VHD
Inför förkrypterade virtuella hårddiskar kan variera beroende på vilken distribution. Exempel på förbereda [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE), och [CentOS 7](#bkmk_CentOS) är tillgängliga. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Konfigurera kryptering under installationen av distributionsplatsen genom att göra följande:

1. Välj **konfigurera krypterade volymer** när du partitionera diskarna.

   ![Ubuntu 16.04 konfigurera – konfigurera krypterade volymer](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenheten som inte får vara krypterade. Kryptera din rotenhet.

   ![Ubuntu 16.04-installation - Välj enheter att kryptera](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du laddade upp till nyckelvalvet.

   ![Ubuntu 16.04 konfigurera – ange lösenfras](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slut partitionering.

   ![Ubuntu 16.04 konfigurera – Slutför partitionering](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

   ![Ubuntu 16.04 konfigurera – ange lösenfras vid start](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av [instruktionerna](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Konfigurera krypteringen ska fungera med Azure genom att göra följande:

1. Skapa en fil under /usr/local/sbin/azure_crypt_key.sh, med innehållet i skriptet nedan. Uppmärksam på KeyFileName, eftersom det är namnet på lösenfras-filen som används av Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Ändra crypt-konfigurationen i */etc/crypttab*. Det bör se ut så här:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Om du redigerar *azure_crypt_key.sh* i Windows och du har kopierat det till Linux, kör `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Lägg till körrättigheter i skriptet:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Redigera */etc/initramfs-tools/modules* genom att lägga till rader:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Kör `update-initramfs -u -k all` att uppdatera initramfs att göra den `keyscript` träder i kraft.

7. Nu kan du avetablera den virtuella datorn.

   ![Ubuntu 16.04-installation - update-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och överföra en virtuell Hårddisk till Azure.

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. När du partitionera diskarna väljer **kryptera volymen grupp**, och sedan ange ett lösenord. Det här är det lösenord som du överföra till ditt nyckelvalv.

   ![openSUSE 13.2-installation - krypterar volym-grupp](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med ditt lösenord.

   ![openSUSE 13.2 konfigurera – ange lösenfras vid start](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Förbereda den virtuella datorn för att ladda upp till Azure genom att följa instruktionerna i [Förbered en SLES- eller openSUSE-dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:
1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Lägg till följande rad i början av filen /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Och ändra alla förekomster av:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   till:
   ```bash
    if [ 1 ]; then
   ```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och lägger till dem i ”# öppna LUKS enhet”:

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Kör `/usr/sbin/dracut -f -v` att uppdatera initrd.

6. Nu kan du avetablera den virtuella datorn och överföra en virtuell Hårddisk till Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. Välj **kryptera Mina data** när du partitionera diskar.

   ![CentOS 7 konfigurera - Installation-mål](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Se till att **Encrypt** har valts för rotpartitionen.

   ![CentOS 7 konfigurera - Välj kryptera för rotpartitionen](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du överföra till ditt nyckelvalv.

   ![CentOS 7-installation - ange lösenfras](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

   ![CentOS 7 konfigurera – ange lösenfras på Start](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av anvisningarna i ”CentOS 7.0 +” [Förbered en CentOS-baserad virtuell dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

6. Nu kan du avetablera den virtuella datorn och överföra en virtuell Hårddisk till Azure.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:

1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Lägg till följande rad i början av filen /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Och ändra alla förekomster av:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   till
   ```bash
    if [ 1 ]; then
   ```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och Lägg till nedanstående efter ”# öppna LUKS enhet”:
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Kör den ”/ usr/sbin/dracut - f - v” att uppdatera initrd.

![CentOS 7-installation - kör /usr/sbin/dracut -f - v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Ladda upp krypterade VHD till ett Azure storage-konto
När BitLocker-kryptering eller DM-Crypt kryptering har aktiverats, måste den lokala krypterade virtuella Hårddisken som ska överföras till ditt lagringskonto.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Ladda upp hemligheten för den förkrypterade virtuella datorn till ditt nyckelvalv
När du krypterar med hjälp av Azure AD-app (tidigare version), måste diskkryptering hemligheten som du fick tidigare laddas upp som en hemlighet i nyckelvalvet. Nyckelvalvet måste ha diskkryptering och behörigheter som har aktiverats för din Azure AD-klient.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Diskkrypteringshemlighet som inte är krypterade med en KEK
Använd [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)för att ställa in hemligheten i nyckel valvet. Om du har en Windows-dator kan filen bek kodas som en base64-sträng och sedan överförs till ditt nyckelvalv med hjälp av den `Set-AzKeyVaultSecret` cmdlet. För Linux, lösenfrasen kodas som en base64-sträng och sedan överförs till nyckelvalvet. Kontrollera dessutom att följande taggar är inställda när du skapar hemligheten i nyckelvalvet.

#### <a name="windows-bek-file"></a>BEK för Windows-fil
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Använd den `$secretUrl` i nästa steg för [koppla OS-disk utan att använda KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Diskkrypteringshemlighet som krypterats med en KEK
Innan du laddar upp hemligheten till nyckelvalvet kryptera du om du vill det med hjälp av en nyckelkrypteringsnyckel. Använd radbyte [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) att kryptera hemligheten med den viktiga krypteringsnyckeln först. Utdata från åtgärden radbyte är en base64-URL-kodad sträng som du kan sedan överföra som en hemlighet med hjälp av den [ `Set-AzKeyVaultSecret` ](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för [koppla OS-disken med hjälp av KEK](#bkmk_URLKEK).

##  <a name="bkmk_SecretURL"></a> Ange en hemlig URL när du kopplar en OS-disk

###  <a name="bkmk_URLnoKEK"></a>Utan att använda en KEK
När du kopplar OS-disken, måste du skicka `$secretUrl`. URL: en har genererats i avsnittet ”diskkryptering hemlighet som inte är krypterade med en KEK”.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Med hjälp av en KEK
När du ansluter OS-disken, skicka `$KeyEncryptionKey` och `$secretUrl`. URL: en har genererats i avsnittet ”diskkrypteringshemlighet krypterade med en KEK”.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
