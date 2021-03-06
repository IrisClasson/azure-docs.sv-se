---
title: Tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure Portal – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitet till en resurs åtkomst till en annan resurs med hjälp av Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2af718c3555176167eb154b0a718218c42e93dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608304"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhets objekt. Den här artikeln visar hur du ger åtkomst till ett Azure Storage-konto med en virtuell Azure-dator eller skalnings uppsättning för virtuella datorer, med hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd RBAC för att tilldela en hanterad identitets åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en virtuell [Azure-dator](qs-configure-portal-windows-vm.md) eller en [virtuell Azure-dators skalnings uppsättning](qs-configure-portal-windows-vmss.md):

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till den Azure-prenumeration under vilken du har konfigurerat den hanterade identiteten.

2. Navigera till önskad resurs där du vill ändra åtkomst kontrollen. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagrings konto, så vi går till lagrings kontot.

3. Välj sidan **åtkomst kontroll (IAM)** i resursen och välj **+ Lägg till roll tilldelning**. Ange sedan **rollen**, **tilldela åtkomst till**och ange motsvarande **prenumeration**. Under Sök villkors ytan bör du se resursen. Välj resursen och välj **Spara**. 

   ![Skärm bild för åtkomst kontroll (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterad identitet på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](qs-configure-portal-windows-vm.md).
- Om du vill aktivera hanterad identitet på en skalnings uppsättning för virtuella Azure-datorer, se [Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med hjälp av Azure Portal](qs-configure-portal-windows-vmss.md).


