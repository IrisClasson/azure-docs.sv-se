---
title: Aktivera säkert LDAP (LDAPS) i Azure AD Domain Services | Microsoft Docs
description: Aktivera säkert LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359862"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Aktivera säkert LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän

## <a name="before-you-begin"></a>Innan du börjar
Fullständig [uppgift 2 – exportera certifikatet för säkert LDAP till en. PFX-filen](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Uppgift 3: Aktivera säkert LDAP för den hanterade domänen med Azure-portalen
Utför följande konfigurationssteg för att aktivera säkert LDAP:

1. Navigera till den  **[Azure-portalen](https://portal.azure.com)** .

2. Sök efter ”domain services” i den **Sök efter resurser** sökrutan. Välj **Azure AD Domain Services** från sökresultaten. Den **Azure AD Domain Services** sidan listar din hanterade domän.

    ![Hitta hanterade domänen som håller på att etableras](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicka på namnet på den hanterade domänen (till exempel ”contoso100.com”) om du vill ha mer information om domänen.

    ![Domain Services - Etableringsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Klicka på **säker LDAP** i navigeringsfönstret.

    ![Domain Services - sidan för säkert LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Åtkomst med säkert LDAP till din hanterade domän är inaktiverad som standard. Visa/dölj **säkert LDAP** till **aktivera**.

    ![Aktivera säkert LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Som standard inaktiveras åtkomst med säkert LDAP till din hanterade domän via internet. Visa/dölj **Tillåt åtkomst med säkert LDAP över internet** till **aktivera**, om du behöver.

    > [!WARNING]
    > När du aktiverar åtkomst med säkert LDAP via internet, är din domän sårbara för råstyrkeattacker för lösenord via internet. Därför rekommenderar vi att konfigurera en NSG för att låsa åtkomst till nödvändiga käll-IP-adressintervall. Se anvisningarna för att [låsa LDAPS åtkomst till din hanterade domän via internet](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Klickar du på mappen ikonen **. PFX-filen med certifikatet för säkert LDAP**. Ange sökvägen till PFX-filen med certifikatet för åtkomst med säkert LDAP till den hanterade domänen.

7. Ange den **lösenord för att dekryptera. PFX-filen**. Ange samma lösenord som du använde när du exporterar certifikatet till PFX-filen.

8. När du är klar klickar du på den **spara** knappen.

9. Du ser ett meddelande som informerar du säkert LDAP konfigureras för den hanterade domänen. Andra inställningar för domänen kan inte ändras förrän den här åtgärden är slutförd.

    ![Konfigurera säkert LDAP för den hanterade domänen](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Det tar cirka 10 – 15 minuter för att aktivera säkert LDAP för den hanterade domänen. Om det angivna certifikatet för säkert LDAP inte matchar kriterierna som krävs, säkert LDAP har inte aktiverats för din katalog och du ser ett fel. Till exempel domännamnet är felaktig, certifikatet har gått ut eller upphör snart att gälla. I det här fallet, försök igen med ett giltigt certifikat.
>
>

## <a name="next-step"></a>Nästa steg
[Uppgift 4: Konfigurera DNS att komma åt den hanterade domänen från Internet](active-directory-ds-ldaps-configure-dns.md)
