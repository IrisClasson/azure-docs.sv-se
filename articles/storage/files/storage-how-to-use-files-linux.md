---
title: Använda Azure Files med Linux | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresurs via SMB på Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e9363f88db4fa44879eb8f6a6a04e23563c5ba44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125739"
---
# <a name="use-azure-files-with-linux"></a>Använda Azure Files med Linux

[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Linux-distributioner som använder den [SMB kernel-klienten](https://wiki.samba.org/index.php/LinuxCIFS). Den här artikeln visar två sätt att montera en Azure-filresurs: på begäran med den `mount` kommandot och på Start genom att skapa en post i `/etc/fstab`.

> [!NOTE]  
> Operativsystemet måste stödja funktioner för kryptering av SMB 3.0 för att montera en Azure-filresurs utanför Azure-regionen den finns i, till exempel lokalt eller i en annan Azure-region.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Krav för att montera en Azure-filresurs med Linux och cifs-utils-paketet
<a id="smb-client-reqs"></a>

* **Ett befintligt Azure storage-konto och filresursen**: Du måste ha ett lagringskonto och en filresurs för att kunna slutföra den här artikeln. Om du inte redan har skapat en finns i våra snabbstarter om ämnet: [Skapa filresurser - CLI](storage-how-to-use-files-cli.md).

* **Ditt lagringskontonamn och nyckel** du behöver lagringskontonamn och nyckel för att kunna slutföra den här artikeln. Om du har skapat en med hjälp av CLI-Snabbstart som du bör redan ha dem, i annat fall Läs CLI-Snabbstart som var kopplad tidigare, för att lära dig hur du hämtar din lagringskontonyckel.

* **Välj en Linux-distribution som passar dina behov för montering.**  
      Azure Files kan monteras via SMB 2.1 och SMB 3.0. För anslutningar från klienter på lokala eller i andra Azure-regioner, måste du använda SMB 3.0; Azure Files avvisar SMB 2.1 (eller SMB 3.0 utan kryptering). Om du försöker komma åt Azure-filresursen från en virtuell dator i samma Azure-region, du kan komma åt din filresursen med SMB 2.1 om och bara om *säker överföring krävs* har inaktiverats för det lagringskonto som är värd för Azure-filresursen. Vi rekommenderar alltid kräva säker överföring och använder endast SMB 3.0 med kryptering.

    Stöd för SMB 3.0-kryptering har introducerades i Linux-kernel-version 4.11 och anpassats till äldre kernel-versioner för populära Linux-distributioner. Vid tidpunkten för publiceringen av det här dokumentet stöder de följande distributionerna från Azure-galleriet montering alternativ som angetts i tabellrubriker. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Minsta rekommenderade versioner med motsvarande mount-funktioner (SMB-version 2.1 eller SMB-version 3.0)

|   | SMB 2.1 <br>(Monterar på virtuella datorer i samma Azure-region) | SMB 3.0 <br>(Monterar från lokalt och över olika regioner) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

Om din Linux-distribution inte visas här kan kontrollera du den Linux-kernel-versionen med följande kommando:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**Cifs-utils-paketet har installerats.**  
    Cifs-utils-paketet kan installeras med Pakethanteraren på valfri annan Linux-distribution. 

    På **Ubuntu** och **Debian-baserad** -distributioner kan använda den `apt-get` Pakethanteraren:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    På **RHEL** och **CentOS**, använda den `yum` Pakethanteraren:

    ```bash
    sudo yum install cifs-utils
    ```

    På **openSUSE**, använda den `zypper` Pakethanteraren:

    ```bash
    sudo zypper install cifs-utils
    ```

    På andra distributioner, använder du lämplig Pakethanteraren eller [kompilera från källan](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Besluta om katalogfil/behörigheterna för den monterade resursen**: I exemplen nedan behörigheten `0777` är används för att ge Läs, Skriv och körbehörighet till alla användare. Du kan ersätta den med andra [chmod behörigheter](https://en.wikipedia.org/wiki/Chmod) efter behov, men det innebär att potentiellt begränsa åtkomsten. Om du använder andra behörigheter, bör du också använda uid och gid för att behålla åtkomst för lokala användare och grupper du väljer.

> [!NOTE]
> Om du inte uttryckligen anger katalog och ett filnamn behörigheten med dir_mode och file_mode standard som till 0755.

* **Se till att port 445 är öppen**: SMB kommunicerar via TCP-port 445. Kontrollera om din brandvägg blockerar TCP-port 445 från klientdatorn.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montera den Azure-filen resursen på begäran med `mount`

1. **[Installera cifs-utils-paketet för din Linux-distribution](#install-cifs-utils)** .

1. **Skapa en mapp för monteringspunkten**: Kan skapa en mapp för en monteringspunkt var som helst i filsystemet, men det är vanliga konventionen att skapa den här under en ny mapp. Till exempel följande kommando skapar en ny katalog, Ersätt **< storage_account_name >** och **< file_share_name >** med rätt information för din miljö:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Använd mount-kommando för att montera Azure-filresursen**: Kom ihåg att ersätta **< storage_account_name >** , **< share_name >** , **< smb_version >** , **< storage_account_key >** , och **< mount_point >** med rätt information för din miljö. Om din Linux-distribution stöder SMB 3.0 med kryptering (se [förstå SMB klientkrav](#smb-client-reqs) för mer information), Använd **3.0** för **< smb_version >** . Linux-distributioner som inte stöder SMB 3.0 med kryptering kan använda **2.1** för **< smb_version >** . En Azure-filresurs kan endast monteras utanför en Azure-region (inklusive lokala eller i en annan Azure-region) med SMB 3.0. Om du vill kan du kan ändra katalog- och filbehörigheter på monterade resursen men det betyder att begränsa åtkomsten.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> När du är klar med Azure-filresursen som du kan använda `sudo umount <mount_point>` att montera resursen.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Skapa en beständig monteringspunkt för Azure-filresursen med `/etc/fstab`

1. **[Installera cifs-utils-paketet för din Linux-distribution](#install-cifs-utils)** .

1. **Skapa en mapp för monteringspunkten**: Kan skapa en mapp för en monteringspunkt var som helst i filsystemet, men det är vanliga konventionen att skapa den här under en ny mapp. Observera den absoluta sökvägen till mappen där du skapar. Till exempel följande kommando skapar en ny katalog, Ersätt **< storage_account_name >** och **< file_share_name >** med rätt information för din miljö.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Skapa en fil med autentiseringsuppgifter för att lagra användarnamnet (lagringskontonamnet) och lösenordet (nyckeln till lagringskontot) för filresursen.** Ersätt **< storage_account_name >** och **< storage_account_key >** med rätt information för din miljö.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Ändra behörigheterna för filen autentiseringsuppgifter så att endast rot kan läsa eller ändra filen.** Ange behörigheter för filen så att endast rot kan komma åt är viktigt att eftersom lagringskontonyckeln är i stort sett ett överordnad administratörslösenord för storage-konto, så att användare med lägre behörighet inte kan hämta nyckeln till lagringskontot.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Använd följande kommando för att lägga till följande rad för att `/etc/fstab`** : Kom ihåg att ersätta **< storage_account_name >** , **< share_name >** , **< smb_version >** , och **< mount_point >** med rätt information för din miljö. Om din Linux-distribution stöder SMB 3.0 med kryptering (se [förstå SMB klientkrav](#smb-client-reqs) för mer information), Använd **3.0** för **< smb_version >** . Linux-distributioner som inte stöder SMB 3.0 med kryptering kan använda **2.1** för **< smb_version >** . En Azure-filresurs kan endast monteras utanför en Azure-region (inklusive lokala eller i en annan Azure-region) med SMB 3.0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Du kan använda `sudo mount -a` att montera Azure-filresursen när du har redigerat `/etc/fstab` i stället för att starta om.

## <a name="feedback"></a>Feedback

Linux-användare som vi vill höra från dig!

Azure-filer för Linux användargrupp innehåller ett forum som du kan dela feedback som du utvärderar och börja använda fillagring i Linux. E-post [Azure filer Linux-användare](mailto:azurefileslinuxusers@microsoft.com) att ansluta till den användargruppen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files finns på följande länkar:

* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning](storage-troubleshoot-linux-file-connection-problems.md)
