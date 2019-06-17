---
title: Migrera en klassisk princip som kräver Multi-Factor authentication i Azure portal
description: Den här artikeln visar hur du migrerar en klassiska princip som kräver Multi-Factor authentication i Azure-portalen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2804a50cc1ef7bb257e1549afabdef466ce3c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112202"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrera en klassisk princip som kräver Multi-Factor authentication i Azure portal

Den här kursen visar hur du migrerar en klassiska princip som kräver **multifaktorautentisering** för en molnapp. Även om det inte är en förutsättning, rekommenderar vi att du läser [migrera klassiska principer i Azure-portalen](policy-migration.md) innan du börjar migrera din klassiska principer.

## <a name="overview"></a>Översikt

Scenariot i den här artikeln visar hur du migrerar en klassiska princip som kräver **multifaktorautentisering** för en molnapp.

![Azure Active Directory](./media/policy-migration/33.png)

Migreringen består av följande steg:

1. [Öppna den klassiska principen](#open-a-classic-policy) att hämta konfigurationsinställningarna.
1. Skapa en ny Azure AD villkorlig åtkomstprincip för att ersätta din klassiska principen. 
1. Inaktivera den klassiska principen.

## <a name="open-a-classic-policy"></a>Öppna den klassiska principen

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

   ![Villkorlig åtkomst](./media/policy-migration-mfa/02.png)

1. I den **hantera** klickar du på **klassiska principer (förhandsversion)** .

   ![Klassiska principer](./media/policy-migration-mfa/12.png)

1. I listan över klassiska principer, klickar du på den princip som kräver **multifaktorautentisering** för en molnapp.

   ![Klassiska principer](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Skapa en ny princip för villkorlig åtkomst

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

   ![Azure Active Directory](./media/policy-migration/01.png)

1. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

   ![Villkorlig åtkomst](./media/policy-migration/02.png)

1. På den **villkorlig åtkomst** sidan för att öppna den **New** , i verktygsfältet högst upp, klickar du på **Lägg till**.

   ![Villkorlig åtkomst](./media/policy-migration/03.png)

1. På den **New** sidan den **namn** textrutan anger du ett namn för principen.

   ![Villkorlig åtkomst](./media/policy-migration/29.png)

1. I den **tilldelningar** klickar du på **användare och grupper**.

   ![Villkorlig åtkomst](./media/policy-migration/05.png)

   1. Om du har valt alla användare i din klassiska principen, klickar du på **alla användare**. 

   ![Villkorlig åtkomst](./media/policy-migration/35.png)

   1. Om du har valt i den klassiska principen grupper klickar du på **Välj användare och grupper**, och välj sedan de nödvändiga användare och grupper.

   ![Villkorlig åtkomst](./media/policy-migration/36.png)

   1. Om du har de exkluderade grupperna, klickar du på den **undanta** fliken och välj sedan de nödvändiga användare och grupper. 

   ![Villkorlig åtkomst](./media/policy-migration/37.png)

1. På den **New** sidan för att öppna den **Molnappar** sidan den **tilldelning** klickar du på **Molnappar**.

1. På den **Molnappar** utför följande steg:

   ![Villkorlig åtkomst](./media/policy-migration/08.png)

   1. Klicka på **Välj appar**.

   1. Klicka på **Välj**.

   1. På den **Välj** sidan, Välj din molnapp och klicka sedan på **Välj**.

   1. På den **Molnappar** klickar du på **klar**.

1. Om du har **kräva multifaktorautentisering** valda:

   ![Villkorlig åtkomst](./media/policy-migration/26.png)

   1. I den **åtkomstkontroller** klickar du på **bevilja**.

   ![Villkorlig åtkomst](./media/policy-migration/27.png)

   1. På den **bevilja** klickar du på **bevilja åtkomst**, och klicka sedan på **kräva multifaktorautentisering**.

   1. Klicka på **Välj**.

1. Klicka på **på** att aktivera din princip.

   ![Villkorlig åtkomst](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Inaktivera den klassiska principen

Om du vill inaktivera din klassiska principen, klickar du på **inaktivera** i den **information** vy.

![Klassiska principer](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om den klassiska principen migrering finns i [migrera klassiska principer i Azure-portalen](policy-migration.md).
- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med Azure Active Directory villkorsstyrd åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa den [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md).
