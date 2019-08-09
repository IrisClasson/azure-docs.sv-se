---
layout: LandingPage
description: Lär dig hur du felsöker distributioner av virtuella datorer.
title: Felsökning för dokumentation om Azure Virtual Machines | Microsoft Docs
services: virtual-machines
author: genlin
manager: gwallace
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: 1db5829abd52a1b010d38dd8e9151ab2df3f1de5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854408"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Felsöka virtuella Azure-datorer

- Allokeringsfel
    - [Allokeringsfel](allocation-failure.md)
    - [Allokeringsfel för klassiska distributioner](allocation-failure-classic.md)
- [Startdiagnostik](boot-diagnostics.md)
- RDP
    - [Återställa RDP](reset-rdp.md)
    - [Felsökning av RDP](troubleshoot-rdp-connection.md)
    - [Detaljerad felsökning av RDP](detailed-troubleshoot-rdp.md)
    - [Felsöka specifika fel](troubleshoot-specific-rdp-errors.md)
- SSH 
    - [Felsökning av SSH](troubleshoot-ssh-connection.md)
    - [Detaljerad felsökning av SSH](detailed-troubleshoot-ssh-connection.md)
    - [Vanliga felmeddelanden](error-messages.md)
- [Installera agent för virtuell Windows-dator offline](install-vm-agent-offline.md)
- Felsöka VM-prestanda
    - [Prestandaproblem med virtuella datorer](performance-diagnostics.md)
    - Windows
        - [Använda PerfInsights](how-to-use-perfinsights.md)
        - [Tillägg för prestandadiagnostik](performance-diagnostics-vm-extension.md)
    - Linux
        - [Använda PerfInsights](how-to-use-perfinsights-linux.md)
- Distribuera om en VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Återställa VM-lösenord
    - [Windows](reset-local-password-without-agent.md)
    - [Linux](reset-password.md)
- [Återställ NIC](reset-network-interface.md)
- [Starta om eller ändra storlek på en virtuell dator](restart-resize-error-troubleshooting.md)
- Använda seriekonsolen
    - [Virtuell Linux-dator](serial-console-linux.md)
        - [GRUB/läge för en enskild användare för seriekonsol](serial-console-grub-single-user-mode.md)
        - [NMI/SysRq för seriekonsol](serial-console-nmi-sysrq.md)
    - [Virtuell Windows-dator](serial-console-windows.md)
        - [CMD- och PowerShell-kommandon](serial-console-cmd-ps-commands.md)
- [Fel när du tar bort lagringsresurser](storage-resource-deletion-errors.md      )
- [Oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar](unexpected-reboots-attached-vhds.md)
- [Problem med Windows-aktivering](troubleshoot-activation-problems.md)
- [Problem med programåtkomst](troubleshoot-app-connection.md)
- Felsöka distributioner
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Enhetsnamnen har ändrats](troubleshoot-device-names-problems.md)
- Återställningsåtkomst till virtuell dator
    - Windows
        - [PowerShell](troubleshoot-recovery-disks-windows.md)
        - [Azure Portal](troubleshoot-recovery-disks-portal-windows.md)
    - Linux
        - [CLI](troubleshoot-recovery-disks-linux.md)
    - [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)
- [Startfel](boot-error-troubleshoot.md)
- [BitLocker-fel](troubleshoot-bitlocker-boot-error.md)
- [Kontrollera filsystemfel](troubleshoot-check-disk-boot-error.md)
- [Fel som visar en blå kraschskärm](troubleshoot-common-blue-screen-error.md)
- [Begränsningsfel](troubleshooting-throttling-errors.md)
- [Använda kapslad virtualisering](troubleshoot-vm-by-use-nested-virtualization.md)
- [Förstå en omstart av systemet](understand-vm-reboot.md)

