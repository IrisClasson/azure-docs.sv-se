---
title: Konfigurera en Azure Migrate-apparat med ett skript
description: Lär dig hur du konfigurerar en Azure Migrate-apparat med ett skript
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 47b6b35e62d484b4d7a33f6a53796c59e01817fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102454"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurera en installation med ett skript

Följ den här artikeln för att skapa en [Azure Migrate-apparat](./migrate-appliance-architecture.md) för bedömning/migrering av virtuella VMware-datorer och virtuella Hyper-V-datorer. Du kan köra ett skript för att skapa en installation och kontrol lera att den kan ansluta till Azure. 

Du kan distribuera-installationen för VMware-och Hyper-V-datorer med hjälp av ett skript eller med hjälp av en mall som du hämtar från Azure Portal. Att använda ett skript är användbart om du inte kan skapa en virtuell dator med hjälp av den nedladdade mallen.

- Om du vill använda en mall följer du självstudierna för [VMware](tutorial-prepare-vmware.md) eller [Hyper-V](tutorial-prepare-hyper-v.md).
- Om du vill konfigurera en installation för fysiska servrar kan du bara använda ett skript. Följ [den här artikeln](how-to-set-up-appliance-physical.md).
- Följ [den här artikeln](deploy-appliance-script-government.md)om du vill konfigurera en installation i ett Azure Government moln.

## <a name="prerequisites"></a>Förutsättningar

Skriptet konfigurerar Azure Migrate-installationen på en befintlig fysisk eller virtuell dator.

- Datorn som fungerar som installations program måste uppfylla följande krav på maskin vara och operativ system:

Scenario | Krav
--- | ---
VMware | Windows Server 2016, med 32 GB minne, åtta virtuella processorer, runt 80 GB disk lagring
Hyper-V | Windows Server 2016 med 16 GB minne, åtta virtuella processorer, cirka 80 GB disk lagring
- Datorn behöver också en extern virtuell växel. Den kräver en statisk eller dynamisk IP-adress och till gång till Internet.
- Innan du distribuerar installationen bör du gå igenom detaljerade installations krav för [virtuella VMware-datorer](migrate-appliance.md#appliance---vmware), [virtuella Hyper-V-datorer](migrate-appliance.md#appliance---hyper-v).
- Kör inte skriptet på en befintlig Azure Migrate-apparat.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurera enheten för VMware

Om du vill konfigurera en installation för VMware laddar du ned den zippade filen med namnet [AzureMigrateInstaller.zip härifrån och](https://go.microsoft.com/fwlink/?linkid=2105112)extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Azure Migrate-projektet.


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Kontrol lera den senaste versionen och skriptet för det offentliga Azure-molnet:

    **Integritetsalgoritm** | **Ladda ned** | **SHA256**
    --- | --- | ---
    VMware (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0



### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den dator som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera enheten för Hyper-V

Om du vill konfigurera enheten för Hyper-V laddar du ned den zippade filen med namnet [AzureMigrateInstaller.zip härifrån och](https://go.microsoft.com/fwlink/?linkid=2105112)extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Azure Migrate-projektet.


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Kontrol lera den senaste versionen och skriptet för det offentliga Azure-molnet:

    **Scenario** | **Ladda ned** | **SHA256**
    --- | --- | ---
    Hyper-V (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2105112) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den dator som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="next-steps"></a>Nästa steg

När du har distribuerat enheten måste du konfigurera den för första gången och registrera den med Azure Migrate projektet.

- Konfigurera enheten för [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Konfigurera enheten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
