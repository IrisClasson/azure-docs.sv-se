---
title: Lägg till din organisations sekretess information – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till din organisations sekretess information till området Azure Active Directory-egenskaper.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2397662112bda6c6789b25691848344a4e611b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561814"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Lägg till din organisations sekretess information med hjälp av Azure Active Directory
Den här artikeln beskrivs hur en administratör kan lägga till sekretessrelaterade information till en organisations Azure Active Directory (Azure AD)-klient, via Azure portal.

Vi rekommenderar starkt att du lägger till både dina global sekretesskontakt och sekretesspolicyn för din organisation så att interna anställda och externa gäster kan granska dina principer. Eftersom sekretesspolicy unikt skapas och skräddarsydd för varje företag, rekommenderar vi du kontakta en jurist om du behöver hjälp.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Lägg till din sekretess-information i Azure AD
Du lägger till din organisations sekretessinformation i den **egenskaper** område i Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Få åtkomst till området för egenskaper och lägga till din sekretessinformation

1.  Logga in på Azure-portalen som en Innehavaradministratör.

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**, och välj sedan **egenskaper**.

    Den **egenskaper** området visas.

    ![Azure AD-egenskaper området markering info sekretessområdet](media/active-directory-properties-area/properties-area.png)

3.  Lägg till din sekretess-information för dina anställda:

    - **Teknisk kontakt.** Ange e-postadressen för personen att kontakta teknisk support i din organisation.
    
    - **Global sekretesskontakt.** Ange e-postadress på kontaktpersonen för frågor om sekretess av personuppgifter. Den här personen finns också som Microsoft kontaktar om det finns en överträdelse av data. Om det finns ingen person som visas här, kontaktar Microsoft dina globala administratörer.

    - **URL till sekretesspolicy.** Ange en länk till din organisations dokument som beskriver hur din organisation hanterar både interna och externa gästens datasekretess.

        >[!Important]
        >Om du inte anger en anpassad sekretesspolicy eller din sekretesskontakt för, din externa gäster visas texten i den **granska behörigheter** rutan,  **< _ditt organisationsnamn_> tillhandahöll inte länkat till sina villkor som du kan granska**. Till exempel visas en gästanvändare det här meddelandet när de får en inbjudan att få åtkomst till en organisation via B2B-samarbete.

        ![B2B-samarbete granska behörigheter box med meddelande](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
- [Inlösning av inbjudan Azure Active Directory B2B-samarbete](https://aka.ms/b2bredemption)
- [Lägga till eller ändra profilinformationen för en användare i Azure Active Directory](active-directory-users-profile-azure-portal.md)
