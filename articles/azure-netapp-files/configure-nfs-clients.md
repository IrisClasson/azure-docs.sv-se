---
title: Konfigurera en NFS-klient för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du konfigurerar NFS-klienter som ska användas med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2020
ms.author: b-juche
ms.openlocfilehash: bdcffcea8d695b6a3d49272ffa135187d77aae1e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535485"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurera en NFS-klient för Azure NetApp Files

Det finns många olika Linux-distributioner som du kan använda med Azure NetApp Files. I den här artikeln beskrivs konfigurationer för två av de ofta använda miljöerna: RHEL 8 och Ubuntu 18,04.

Oavsett vilken Linux-variant du använder, krävs följande konfigurationer:
* Konfigurera en NTP-klient för att undvika problem med tids skevning.
* Konfigurera DNS-poster för Linux-klienten för namn matchning.  
    Den här konfigurationen omfattar posten "A" (forward) och PTR (omvänd). 
* Skapa ett dator konto i mål Active Directory för domän anslutning (som skapas vid kommandot sfär anslutning). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Variabeln som används i kommandona nedan måste vara ett användar konto med behörighet eller delegering för att skapa ett dator konto i den aktuella organisationsenheten.
* Aktivera klienten för montering av NFS-volymer och andra relevanta övervaknings verktyg.

## <a name="rhel-8-configuration"></a>RHEL 8-konfiguration 

1. Installera paket:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Konfigurera NTP-klienten:  
    RHEL 8 använder `chrony` som standard.  Följ rikt linjerna för konfiguration i [chrony-programsviten för att konfigurera NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Anslut Active Directory-domänen:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu-konfiguration 

1. Installera paket:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Konfigurera NTP-klienten.  
    Ubuntu 18,04 används `chrony` som standard.  Följa konfigurations rikt linjerna i [Ubuntu Bionic: använda chrony för att konfigurera NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Gå med i Active Directory-domän:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Nästa steg  

* [Skapa en NFS-volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Skapa en dual-Protocol-volym för Azure NetApp Files](create-volumes-dual-protocol.md)

