---
title: Det går inte att fjärransluta till Azure Virtual Machines eftersom nätverkskortet är inaktiverat | Microsoft Docs
description: Lär dig att felsöka ett problem där RDP misslyckas eftersom nätverkskortet är inaktiverat i Azure VM | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: bb4583c88f2e867c619a2163d91bff1d48b6a7a3
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70230702"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Det går inte att fjärrskrivbord till en virtuell dator eftersom nätverksgränssnittet har inaktiverats

Den här artikeln förklarar hur du löser ett problem där du inte kan göra en fjärrskrivbordsanslutning till Azure Windows Virtual Machines (VMs) om nätverksgränssnittet har inaktiverats.

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen.

## <a name="symptoms"></a>Symtom

Du kan inte göra en RDP-anslutning eller någon annan typ av anslutning till andra portar till en virtuell dator i Azure eftersom nätverksgränssnittet i den virtuella datorn är inaktiverad.

## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

Om du vill aktivera gränssnittet för den virtuella datorn använder Serial kontroll eller [Återställ nätverksgränssnittet](##reset-network-interface) för den virtuella datorn.

### <a name="use-serial-control"></a>Använda seriell kontroll

1. Ansluta till [seriella konsolen och öppna CMD instans](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om Seriekonsolen inte är aktiverad på den virtuella datorn finns i [Återställ nätverksgränssnittet](#reset-network-interface).
2. Kontrollera tillståndet för nätverksgränssnittet:

        netsh interface show interface

    Anteckna namnet på det inaktiverade nätverksgränssnittet.

3. Aktivera nätverksgränssnittet:

        netsh interface set interface name="interface Name" admin=enabled

    Till exempel gränssnittet interwork heter ”Ethernet 2”, kör du följande kommando:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Kontrollera tillståndet för nätverksgränssnittet igen för att se till att nätverksgränssnittet är aktiverad.

        netsh interface show interface

    Du behöver inte starta om den virtuella datorn nu. Den virtuella datorn kommer att tillbaka kan nås.

5.  Ansluter till den virtuella datorn och se om problemet är löst.

## <a name="reset-network-interface"></a>Återställ nätverksgränssnittet

Om du vill återställa nätverksgränssnitt, ändra IP-adressen till en annan IP-adress som är tillgänglig i undernätet. Gör detta genom att använda Azure portal eller Azure PowerShell. Mer information finns i [Återställ nätverksgränssnittet](reset-network-interface.md).
