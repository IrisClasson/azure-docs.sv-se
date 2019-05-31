---
title: 'Azure Active Directory Domain Services: Felsöka Nätverkssäkerhetsgrupp configuration | Microsoft Docs'
description: Felsökning av NSG-konfiguration för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 743f83fd25ff897492fda7103d3db1f4b961714d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246576"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Felsöka ogiltig nätverkskonfiguration för din hanterade domän
Den här artikeln hjälper dig att felsöka och lösa nätverksrelaterade konfigurationsfel som resulterar i följande varningsmeddelande:

## <a name="alert-aadds104-network-error"></a>Varning AADDS104: Nätverksfel
**Varningsmeddelande:** *Microsoft kan inte nå domänkontrollanterna för den här hanterade domänen. Detta kan inträffa om en nätverkssäkerhetsgrupp (NSG) som har konfigurerats på ditt virtuella nätverk blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är att om det finns en användardefinierad väg som blockerar inkommande trafik från internet.*

Ogiltig NSG-konfigurationer är den vanligaste orsaken för nätverksfel för Azure AD Domain Services. Den Nätverkssäkerhetsgrupp (NSG) konfigurerats för det virtuella nätverket måste tillåta åtkomst till [specifika portar](network-considerations.md#ports-required-for-azure-ad-domain-services). Om dessa portar blockeras kan inte Microsoft övervaka eller uppdatera din hanterade domän. Dessutom påverkas synkroniseringen mellan Azure AD-katalogen och din hanterade domän. När du skapar din NSG Låt portarna som är öppet för att undvika avbrott i tjänsten.

### <a name="checking-your-nsg-for-compliance"></a>Kontrollera din NSG för kompatibilitet

1. Navigera till den [Nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) sidan på Azure portal
2. Välj Nätverkssäkerhetsgrupp kopplad till undernätet där den hanterade domänen aktiveras från tabellen.
3. Under **inställningar** i den vänstra panelen klickar du på **ingående säkerhetsregler**
4. Granska reglerna på plats och identifiera vilka regler blockerar åtkomst till [dessa portar](network-considerations.md#ports-required-for-azure-ad-domain-services)
5. Redigera NSG: N för att säkerställa efterlevnad genom att antingen ta bort regeln, lägger till en regel eller skapa en ny NSG helt och hållet. Steg för att [lägga till en regel](#add-a-rule-to-a-network-security-group-using-the-azure-portal) eller skapa en ny, kompatibla NSG är lägre än

## <a name="sample-nsg"></a>Exemplet NSG
I följande tabell visar ett exempel NSG som skulle skydda din hanterade domän samtidigt som Microsoft för att övervaka, hantera och uppdatera informationen.

![Exemplet NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services kräver obegränsad utgående åtkomst från det virtuella nätverket. Vi rekommenderar inte för att skapa någon ytterligare NSG-regel som begränsar utgående åtkomst för det virtuella nätverket.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Lägga till en regel i en grupp med Azure portal
Om du inte vill använda PowerShell kan du manuellt lägga till regler för enkel NSG: er med Azure-portalen. För att skapa regler i nätverkssäkerhetsgruppen, gör du följande:

1. Navigera till den [Nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) i Azure-portalen.
2. Välj Nätverkssäkerhetsgrupp kopplad till undernätet där den hanterade domänen aktiveras från tabellen.
3. Under **inställningar** i den vänstra panelen klickar du antingen **ingående säkerhetsregler** eller **utgående säkerhetsregler**.
4. Skapa regeln genom att klicka på **Lägg till** och fylla i informationen. Klicka på **OK**.
5. Kontrollera att regeln har skapats genom att leta upp den i tabellen.


## <a name="need-help"></a>Behöver du hjälp?
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](contact-us.md).
