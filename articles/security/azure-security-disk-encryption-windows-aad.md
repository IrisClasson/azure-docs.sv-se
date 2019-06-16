---
title: Azure Disk Encryption med Azure AD App Windows IaaS-datorer (tidigare version)
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för Windows virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: fa9b970ee9319af061ceab99844b0497253881ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117937"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms-previous-release"></a>Aktivera Azure Disk Encryption för Windows virtuella IaaS-datorer (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar behovet av att tillhandahålla en parameter för Azure AD-program att aktivera VM-diskkryptering. Med den nya versionen kan krävs du inte längre för att ange autentiseringsuppgifter för Azure AD under steget Aktivera kryptering. Alla nya virtuella datorer måste vara krypterat utan parametrarna Azure AD-program med hjälp av den nya versionen. Instruktioner för att aktivera diskkryptering för virtuell dator med hjälp av den nya versionen finns [Azure Disk Encryption för Windows virtuella datorer](azure-security-disk-encryption-windows.md). Virtuella datorer som redan har krypterats med Azure AD-program parametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntax.**


Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj för Windows virtuella IaaS-datorer. Innan du kan använda diskkryptering, den [som krävs för Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites-aad.md) måste slutföras. 

Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopierar innan diskar krypteras. Säkerhetskopior Se till att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. Du kan använda cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-app](azure-security-disk-encryption-prerequisites-aad.md) för att kryptera den här virtuella datorn, måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) på den här krypterade virtuella datorn eftersom det är inte ett scenario som stöds, betydelse växla från AAD-programmet för det här krypterade virtuella datorer stöds inte ännu.
> - För att se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Aktivera kryptering på den nya IaaS-datorer som skapats från Marketplace
Du kan aktivera diskkryptering på nya virtuella IaaS Windows-dator från Marketplace i Azure med hjälp av Resource Manager-mall. Mallen skapar en ny krypterade Windows virtuell dator med Windows Server 2012 galleriet bilden.

1. På den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), klickar du på **distribuera till Azure**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **köp** att distribuera en ny IaaS-VM där kryptering är aktiverat.

3. När du har distribuerat mallen Kontrollera krypteringsstatus VM med önskad metod:
     - Kontrollera med Azure CLI med hjälp av den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Kontrollera med Azure PowerShell med hjälp av den [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Välj den virtuella datorn, och klicka sedan på **diskar** under den **inställningar** rubrik för att kontrollera status för datakryptering i portalen. I diagrammet under **kryptering**, ser du om den är aktiverad. 
           ![Azure portal – Disk kryptering aktiverat](./media/azure-security-disk-encryption/disk-encryption-fig2.png) i följande tabell visas Resource Manager-mallens parametrar för nya virtuella datorer från Marketplace-scenario med hjälp av Azure AD-klient-ID:

| Parameter | Beskrivning | 
| --- | --- |
| adminUserName | Administratörsanvändarnamn för den virtuella datorn. |
| adminPassword | Administratörslösenord för den virtuella datorn. |
| newStorageAccountName | Namnet på lagringskontot för att lagra OS- och virtuella hårddiskar. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G-serien. |
| virtualNetworkName | Namnet på det virtuella nätverk som VM NIC ska tillhöra. |
| subnetName | Namnet på undernätet i det virtuella nätverket som VM NIC ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| KeyVaultURL | URL för nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` eller Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln (valfritt). </br> </br>KeyEncryptionKeyURL är en valfri parameter. Du kan använda din egen KEK att ytterligare skydda krypteringsnyckeln för tjänstdata (hemlig lösenfras) i ditt nyckelvalv. |
| keyVaultResourceGroup | Resursgrupp för nyckelvalvet. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |


## <a name="bkmk_RunningWinVM"></a> Aktivera kryptering på befintliga eller som kör Windows virtuella IaaS-datorer
I det här scenariot kan du aktivera kryptering med hjälp av en mall, PowerShell-cmdletar eller CLI-kommandon. I följande avsnitt beskrivs i detalj hur du aktiverar Azure Disk Encryption. 

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Aktivera kryptering på befintliga eller köra virtuella datorer med Azure PowerShell 
Använd den [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure. Information om hur du aktiverar kryptering med Azure Disk Encryption med PowerShell-cmdlets finns i blogginläggen [utforska Azure Disk Encryption med Azure PowerShell – del 1](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och [utforska Azure Disk Encryption med Azure PowerShell – del 2](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:** Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckelvalv du omsluter disk encryption hemligheter som genererades vid aktivering av kryptering. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera statusen för kryptering av en IaaS-VM, använda den [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera kryptering, använda den [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Aktivera kryptering på befintliga eller köra virtuella datorer med Azure CLI
Använd den [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) kommando för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure.

- **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera statusen för kryptering av en IaaS-VM, använda den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Det här kommandot kan utföras mot virtuella datorer med hanterade diskar baserat förrän en säkerhetskopia har gjorts och den här parametern har angetts. 
  >
  >Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 

### <a name="bkmk_RunningWinVMwRM"> </a>Med hjälp av Resource Manager-mall
Du kan aktivera diskkryptering på befintliga eller kör IaaS Windows-datorer i Azure med hjälp av den [Resource Manager-mall för att kryptera en virtuell dator som körs Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **köp** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller som kör virtuella datorer som använder en Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommando `az keyvault list --resource-group "MySecureGroup"`|
|  KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. |
| SequenceVersion | Sekvens-versionen av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |


## <a name="bkmk_VHDpre"> </a>Nya virtuella IaaS-datorer skapas från kund-krypterad VHD och kryptering nycklar
Du kan aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs mer detaljerat Resource Manager-mall och CLI-kommandon. 

Följ instruktionerna i tillägget för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Förbereda en förkrypterade Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 



### <a name="bkmk_VHDprePSH"> </a> Kryptera virtuella datorer med förkrypterade virtuella hårddiskar med Azure PowerShell
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). I exemplet nedan visas några vanliga parametrar. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk
Du kan [lägga till en ny disk till en Windows virtuell dator med hjälp av PowerShell](../virtual-machines/windows/attach-disk-ps.md), eller [via Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för virtuella Windows-datorer, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. I vissa fall kan kan en nyligen tillagd datadisk vara krypterad automatiskt med Azure Disk Encryption-tillägget. Automatisk kryptering uppstår vanligen när den virtuella datorn startas om när den nya disken är online. Detta beror vanligtvis på att ”alla” har angetts för typ av volym när diskkryptering körde tidigare på den virtuella datorn. Om automatisk kryptering sker i en nyligen tillagd datadisk, rekommenderar vi använder cmdleten Set-AzVmDiskEncryptionExtension igen med ny sekvens. Om din nya datadisk är auto krypterad och du inte vill ska krypteras, dekryptera alla enheter först och sedan kryptera med en ny sekvens version att Operativsystemet för typ av volym. 
 

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Resursgrupp, virtuell dator, nyckelvalvet, AAD-appar och klienthemlighet bör redan har skapats som krav. Ersätt MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, min-AAD-klient-ID och min-AAD-client-secret med dina värden. Det här exemplet använder ”alla” för parametern - VolumeType som innehåller både OS- och datavolymer. Om du bara vill kryptera operativsystemvolymen använda ”OS” för parametern - VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:** Azure Disk Encryption kan du ange en befintlig nyckel i ditt nyckelvalv du omsluter disk encryption hemligheter som genererades vid aktivering av kryptering. När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. Det här exemplet använder ”alla” för parametern - VolumeType som innehåller både OS- och datavolymer. Om du bara vill kryptera operativsystemvolymen använda ”OS” för parametern - VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI
  Azure CLI-kommando får automatiskt en ny sekvens-version för dig när du kör kommandot för att aktivera kryptering. Godkända värden för parametern volym yype är alla, OS- och Data. Du kan behöva ändra volymtyp parametern till OS- eller om du krypterar bara en typ av disk för den virtuella datorn. I exemplen används ”alla” för parametern volymtyp. 

-  **Kryptera en aktiv virtuell dator med hjälp av en klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK du omsluter klienthemlighet:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Aktivera kryptering med hjälp av Azure AD-klienten certifikatbaserad autentisering.
Du kan använda autentisering med klientcertifikat med eller utan KEK. Skripten kräver att [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) har slutförts. Innan du använder PowerShell-skript, bör du redan har certifikatet laddas upp till nyckelvalvet och distribueras till den virtuella datorn. Om du använder KEK för, ska KEK redan finnas. Mer information finns i den [certifikatbaserad autentisering för Azure AD](azure-security-disk-encryption-prerequisites-aad.md#bkmk_Cert) i artikeln krav.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering med Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Aktivera kryptering med certifikatbaserad autentisering och en KEK med Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Inaktivera kryptering
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. 

- **Inaktivera disk encryption med Azure PowerShell:** Om du vill inaktivera kryptering, använda den [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Inaktivera kryptering med Azure CLI:** Inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **distribuera till Azure** från den [inaktiverar diskkryptering på Windows virtuell dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) mall.
    2. Välj prenumeration, resursgrupp, plats, VM, juridiska villkor och avtal.
    3.  Klicka på **köp** att inaktivera diskkryptering på en aktiv virtuell Windows-dator. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux-aad.md)
