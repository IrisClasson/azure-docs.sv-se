---
title: 'Azure Active Directory Domain Services: Uppdatera DNS-inställningarna för virtuella Azure-nätverket | Microsoft Docs'
description: Komma igång med Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: iainfou
ms.openlocfilehash: ef0c25227e98d066d75c9adb62af3f602e35db23
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474159"
---
# <a name="enable-azure-active-directory-domain-services"></a>Aktivera Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Uppgift 4 – uppdatera DNS-inställningarna för det virtuella Azure-nätverket
I de tidigare konfigurationsuppgifterna har du aktiverat Azure Active Directory Domain Services för katalogen. Sedan kontrollerar du att datorerna i det virtuella nätverket kan ansluta och använda tjänsterna. I den här artikeln får du uppdatera DNS-serverinställningarna för det virtuella nätverket så att de pekar på de bägge IP-adresserna där Azure Active Directory Domain Services är tillgängliga i det virtuella nätverket.

Utför följande konfigurationssteg för att uppdatera DNS-serverinställningarna för det virtuella nätverket där Azure Active Directory Domain Services har aktiverats:


1. Fliken **Översikt** visar **Nödvändiga konfigurationssteg** som ska utföras när din hanterade domän är helt etablerad. Det första konfigurationssteget är **Uppdatera DNS-serverinställningarna för det virtuella nätverket**.

    ![Domain Services – fliken Översikt](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Visas inte det här konfigurationssteget? Om DNS-serverinställningarna för det virtuella nätverket är uppdaterade visas inte panelen ”Uppdatera DNS-serverinställningarna för ditt virtuella nätverk” på fliken Översikt.
    >
    >

2. Klicka på knappen **Konfigurera** för att uppdatera DNS-serverinställningarna för det virtuella nätverket.

> [!NOTE]
> Virtuella datorer i nätverket kan bara hämta de nya DNS-inställningarna efter en omstart. Om du vill att de ska hämta de uppdaterade DNS-inställningarna direkt kan du utlösa en omstart genom portalen, PowerShell eller CLI.
>
>

## <a name="next-step"></a>Nästa steg
[Uppgift 5: Aktivera lösenordshashsynkronisering med Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
