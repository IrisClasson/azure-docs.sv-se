---
title: Ansluta en Windows Server-dator till Azure Active Directory Domain Services | Microsoft Docs
description: Anslut en Windows Server-dator till en hanterad domän med hjälp av Azure Resource Manager-mallar.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 3d16a4240b7a30a483b70b068ab7d91ca7bdcb17
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473041"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Ansluta en Windows Server-dator till en hanterad domän med en Resource Manager-mall
Den här artikeln visar hur du kopplar en Windows Server-dator till en Azure AD Domain Services-hanterad domän med hjälp av Resource Manager-mallar.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:
1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](create-instance.md).
4. Se till att du har konfigurerat IP-adresserna för den hanterade domänen som DNS-servrar för det virtuella nätverket. Mer information finns i [så här uppdaterar du DNS-inställningarna för virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)
5. Slutför stegen som krävs för att [synkronisera lösenord till din hanterade domän i Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Installera och konfigurera verktyg som krävs
Du kan använda något av följande alternativ för att utföra de steg som beskrivs i det här dokumentet:
* **Azure PowerShell**: [Installera och konfigurera](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Installera och konfigurera](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Alternativ 1: Etablera en ny Windows Server VM och ansluta den till en hanterad domän
**Snabbstart mallnamn**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Om du vill distribuera en Windows Server-datorn och ansluta den till en hanterad domän, utför du följande steg:
1. Navigera till den [Snabbstart mallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Klicka på **Distribuera till Azure**.
3. I den **anpassad distribution** anger du informationen som krävs för att etablera den virtuella datorn.
4. Välj den **Azure-prenumeration** där du kan etablera den virtuella datorn. Välj samma Azure-prenumeration som du har aktiverat Azure AD Domain Services.
5. Välj en befintlig **resursgrupp** eller skapa en ny.
6. Välj en **plats** där du kan distribuera den nya virtuella datorn.
7. I **befintliga VNET-namn**, ange det virtuella nätverket där du har distribuerat din hanterade domän i Azure AD Domain Services.
8. I **befintliga Undernätsnamn**, anger ett undernät i det virtuella nätverket där du vill distribuera den här virtuella datorn. Markera inte gateway-undernätet i det virtuella nätverket. Välj dessutom inte dedikerade undernät som den hanterade domänen har distribuerats.
9. I **DNS-etikett Prefix**, ange värdnamnet för den virtuella datorn håller på att etableras. Till exempel ”contoso-win”.
10. Välj lämplig **VM-storlek** för den virtuella datorn.
11. I **domänanslutning till**, ange DNS-domännamnet för den hanterade domänen.
12. I **domänanvändarnamn**, ange namnet på användarkontot på den hanterade domänen som ska användas för att ansluta till den virtuella datorn till den hanterade domänen.
13. I **domänlösenord**, ange lösenordet för det användarkonto som anges av parametern 'domainUsername'.
14. Valfritt: Du kan ange en **OU sökväg** till en anpassad Organisationsenheten där du vill lägga till den virtuella datorn. Om du inte anger ett värde för den här parametern, den virtuella datorn har lagts till standard **AAD DC datorer** Organisationsenhet i den hanterade domänen.
15. I den **VM administratörsanvändarnamnet** fältet, ange ett kontonamn för lokal administratör för den virtuella datorn.
16. I den **VM adminlösenord** fältet, ange ett lösenord för lokal administratör för den virtuella datorn. Ange ett starkt lokala administratörslösenordet för den virtuella datorn att skydda mot lösenord brute force-attacker.
17. Klicka på **jag godkänner villkoren som anges ovan**.
18. Klicka på **köp** att etablera den virtuella datorn.

> [!WARNING]
> **Hantera lösenord med försiktighet.**
> Parametern mallfilen innehåller lösenord för domänkonton samt lokal administratörslösenord för den virtuella datorn. Se till att du lämnar den här filen ligger runt på filresurser eller andra delade platser. Vi rekommenderar att du förfoga över den här filen när du är klar distribuera dina virtuella datorer.
>

När distributionen är klar är din nyetablerade virtuella Windows-dator ansluten till den hanterade domänen.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Alternativ 2: Ansluta en befintlig Windows Server-dator till en hanterad domän
**Snabbstart för mallen**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Utför följande steg för att ansluta till en befintlig Windows Server-dator till en hanterad domän:
1. Navigera till den [Snabbstart mallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Klicka på **Distribuera till Azure**.
3. I den **anpassad distribution** anger du informationen som krävs för att etablera den virtuella datorn.
4. Välj den **Azure-prenumeration** där du kan etablera den virtuella datorn. Välj samma Azure-prenumeration som du har aktiverat Azure AD Domain Services.
5. Välj en befintlig **resursgrupp** eller skapa en ny.
6. Välj en **plats** där du kan distribuera den nya virtuella datorn.
7. I den **VM List** fältet, ange namnen på befintliga virtuella datorer är ansluten till den hanterade domänen. Använd kommatecken för att avgränsa namn på enskilda virtuella datorer. Till exempel **contoso-webb-, contoso-api**.
8. I **domänanvändarnamn för anslutning till**, ange namnet på användarkontot på den hanterade domänen som ska användas för att ansluta till den virtuella datorn till den hanterade domänen.
9. I **ansluta Domänanvändarlösenord**, ange lösenordet för det användarkonto som anges av parametern 'domainUsername'.
10. I **domännamn FQDN**, ange DNS-domännamnet för den hanterade domänen.
11. Valfritt: Du kan ange en **OU sökväg** till en anpassad Organisationsenheten där du vill lägga till den virtuella datorn. Om du inte anger ett värde för den här parametern, den virtuella datorn har lagts till standard **AAD DC datorer** Organisationsenhet i den hanterade domänen.
12. Klicka på **jag godkänner villkoren som anges ovan**.
13. Klicka på **köp** att etablera den virtuella datorn.

> [!WARNING]
> **Hantera lösenord med försiktighet.**
> Parametern mallfilen innehåller lösenord för domänkonton samt lokal administratörslösenord för den virtuella datorn. Se till att du lämnar den här filen ligger runt på filresurser eller andra delade platser. Vi rekommenderar att du förfoga över den här filen när du är klar distribuera dina virtuella datorer.
>

När distributionen är klar är de angivna Windows virtuella datorerna anslutna till den hanterade domänen.


## <a name="related-content"></a>Relaterat innehåll
* [Översikt över Azure PowerShell](/powershell/azure/overview)
* [Azure Snabbstart mall - ansluten till en ny virtuell dator](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure Snabbstart mall - ansluten till befintliga virtuella datorer](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
