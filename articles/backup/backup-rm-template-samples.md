---
title: Azure Resource Manager-mallar för Azure Backup
description: PowerShell-exempel för Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bed2c002cacdac1e47852e6fa3181885af6733d2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236789"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-mallar för Azure Backup

I följande tabell finns länkar till Azure Resource Manager-mallar du kan använda för Recovery Services-valv och funktioner i Azure Backup. Mer information om JSON-syntaxen och JSON-egenskaperna finns i [Microsoft.RecoveryServices-resurstyper](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Recovery Services-valv** | |
| [Skapa ett Recovery Services-valv](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Skapa ett Recovery Services-valv. Du kan använda valvet för Azure Backup och Azure Site Recovery. |
|**Säkerhetskopiera virtuella datorer**| |
| [Säkerhetskopiera virtuella Resource Manager-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Använd det befintliga Recovery Services-valvet och Backup-principen för säkerhetskopiering av virtuella Resource Manager-datorer i samma resursgrupp.|
| [Säkerhetskopiera virtuella IaaS-datorer till ett Recovery Services-valv](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Mall för säkerhetskopiering av klassiska virtuella datorer och virtuella Resource Manager-datorer. |
| [Skapa en princip för veckovis säkerhetskopiering av virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Mallen skapar Recovery Services-valv och en princip för veckovis säkerhetskopiering, som används för att säkerhetskopiera klassiska och Resource Manager-virtuella datorer.|
| [Skapa en princip för daglig säkerhetskopiering av virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Mallen skapar Recovery Services-valv och en princip för daglig säkerhetskopiering, som används för att säkerhetskopiera klassiska och Resource Manager-virtuella datorer.|
| [Distribuera virtuella Windows Server-datorer med säkerhetskopiering aktiverad](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Mallen skapar en virtuell Windows Server-dator och ett Recovery Services-valv med standardprincipen för säkerhetskopiering aktiverad.|
|**Övervaka säkerhetskopieringsjobb** |  |
| [Använda Azure Monitor-loggar med Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Mallen distribuerar Azure Monitor-loggar med Azure Backup, där du kan övervaka säkerhetskopierings- och återställa jobb, aviseringar om säkerhetskopiering och molnlagring som används i dina Recovery Services-valv.|  
|**Säkerhetskopiera SQL Server i Azure VM** |  |
| [Säkerhetskopiera SQL Server i Azure VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Mallen skapar ett Recovery Services-valv och arbetsbelastningen specifik princip för säkerhetskopiering. Den registreras den virtuella datorn i Azure Backup-tjänsten och konfigurerar skydd på den virtuella datorn. För närvarande fungerar det bara för SQL-galleriavbildningar. |
|   |   |
