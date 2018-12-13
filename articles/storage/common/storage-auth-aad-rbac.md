---
title: Använd RBAC för att hantera åtkomstbehörigheter till behållare och Azure Storage-köer (förhandsversion) | Microsoft Docs
description: Använda rollbaserad åtkomstkontroll (RBAC) för att tilldela roller för åtkomst till blob och kö data till användare, grupper, tjänsthuvudnamn för programmet eller hanterade tjänstidentiteter. Azure Storage stöder inbyggda och anpassade roller för åtkomst till behållare och köer.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 911b8051530daca57fe26fb22c6dc1be845c6d6d
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321434"
---
# <a name="manage-access-rights-to-azure-blob-and-queue-data-with-rbac-preview"></a>Hantera åtkomstbehörigheter till Azure Blob- och ködata med RBAC (förhandsversion)

Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för åtkomst till behållare eller köer. När en RBAC-roll tilldelas till en Azure AD-identitet identitet beviljas åtkomst till dessa resurser, enligt det angivna omfånget. Åtkomst kan begränsas till nivån för prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller en kö. Du kan tilldela åtkomsträttigheter för Azure Storage-resurser med Azure-portalen, Azure kommandoradsverktyg och Azure Management API: er. 

En Azure AD-identitet kan vara en användare, grupp eller program tjänstens huvudnamn eller det kan vara en hanterad identitet för Azure-resurser. Ett säkerhetsobjekt kan vara en användare, grupp eller program tjänstens huvudnamn. En [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) är ett automatiskt hanterad identitet som används för att autentisera från program som körs i Azure-datorer, funktionsappar, skalningsuppsättningar för virtuella datorer och andra. En översikt av identitet i Azure AD finns i [förstå Azure-identitetslösningar](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

Azure Storage stöder både inbyggda och anpassade RBAC-roller. Azure Storage erbjuder dessa inbyggda RBAC-roller för användning med Azure AD:

- [Storage Blob Data-deltagare (förhandsgranskning)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Storage Blob Data-läsare (förhandsgranskning)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Storage Blob Data-ägare (förhandsversion)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)
- [Lagringsködata-deltagare (förhandsgranskning)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Lagringsködata-läsare (förhandsgranskning)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Mer information om hur inbyggda roller definieras för Azure Storage, se [förstå rolldefinitioner](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Du kan också definiera anpassade roller för användning med behållare och köer. Mer information finns i [skapa anpassade roller för rollbaserad åtkomstkontroll i](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>Tilldela en roll till ett säkerhetsobjekt

Tilldela en RBAC-roll till en Azure-identitet att ge behörighet till behållare eller köer i lagringskontot. Du kan begränsa rolltilldelning till storage-kontot eller till en specifik behållare eller en kö. I följande tabell sammanfattas åtkomsträttigheterna som beviljas av de inbyggda rollerna, beroende på omfattningen:

|Scope|Ägare för BLOB-Data|BLOB Data-deltagare|BLOB-dataläsare|Lagringsködata-deltagare|Lagringsködata-läsare|
|---|---|---|---|---|---|
|Prenumerationsnivå|Läs-/ skrivåtkomst till alla behållare och blobbar i prenumerationen|Läs-/ skrivåtkomst till alla behållare och blobbar i prenumerationen| Läsbehörighet till alla behållare och blobbar i prenumerationen|Läs-/ skrivåtkomst till alla köer i prenumerationen|Läsbehörighet till alla köer i prenumerationen|
|Resursgrupp|Läs-/ skrivåtkomst till alla behållare och blobbar i resursgruppen.|Läs-/ skrivåtkomst till alla behållare och blobbar i resursgruppen.|Läsbehörighet till alla behållare och blobbar i resursgruppen.|Läs-/ skrivåtkomst till alla köer i resursgruppen|Läsbehörighet till alla köer i resursgruppen|
|Lagringskontonivån|Läs-/ skrivåtkomst till alla behållare och blobbar i lagringskontot|Läs-/ skrivåtkomst till alla behållare och blobbar i lagringskontot|Läsbehörighet till alla behållare och blobbar i lagringskontot|Läs-/ skrivåtkomst till alla köer i lagringskontot|Läsbehörighet till alla köer i lagringskontot|
|Behållare/kö-nivå|Läs/skrivbehörighet till den angivna behållaren och dess blobbar|Läs/skrivbehörighet till den angivna behållaren och dess blobbar|Läsbehörighet till den angivna behållaren och dess blobbar|Läs/skrivbehörighet till den angivna kön|Läsbehörighet till den angivna kön|

> [!NOTE]
> Ägare för Azure Storage-kontot kan tilldelas du automatiskt inte behörighet att komma åt data. Du måste uttryckligen tilldela dig själv en RBAC-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resursgrupp, storage-konto, eller en behållare eller en kö.

Mer information om de behörigheter som krävs för att anropa åtgärder för Azure Storage finns [behörigheter för att anropa REST-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

I följande avsnitt visas hur du tilldelar en roll som omfattar storage-konto eller begränsade till en enskild behållare.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Tilldela en roll som är begränsade till lagringskontot i Azure portal

Tilldela en inbyggd roll som beviljar åtkomst till alla behållare eller köer i lagringskontot i Azure portal:

1. I den [Azure-portalen](https://portal.azure.com), navigera till ditt lagringskonto.
1. Välj ditt lagringskonto och sedan **åtkomstkontroll (IAM)** att visa inställningar för åtkomstkontroll för kontot. Välj den **rolltilldelningar** flik för att se en lista över rolltilldelningar.

    ![Skärmbild som visar behörighetsinställningar för lagring](media/storage-auth-aad-rbac/portal-access-control.png)

1. Klicka på den **Lägg till rolltilldelning** för att lägga till en ny roll.
1. I den **Lägg till rolltilldelning** fönstret Välj roll att tilldela till en Azure AD-identitet. Leta sedan för att hitta identiteten som du vill tilldela rollen. Exempelvis följande bild visar den **Storage Blob Data-läsare (förhandsgranskning)** roll som tilldelats till en användare.

    ![Skärmbild som visar hur du tilldelar en RBAC-roll](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Klicka på **Spara**. Identiteten som du tilldelats rollen visas listan under rollen. Följande bild visar till exempel att användaren nu har läsbehörighet för alla blobbdata i lagringskontot.

    ![Skärmbild som visar listan över användare som har tilldelats en roll](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Tilldela en roll som är begränsade till en behållare eller en kö i Azure portal

> [!IMPORTANT]
> Du kan inte göra detta om du använder ett konto med hierarkiskt namnområde ännu aktiverat.

Stegen för att tilldela en inbyggd roll som är begränsade till en behållare eller till en kö är liknande. Proceduren som visas här tilldelas en roll som är begränsade till en behållare, men du kan följa samma steg för att tilldela en roll som är begränsade till en kö: 

1. I den [Azure-portalen](https://portal.azure.com)går du till ditt lagringskonto och visa den **översikt** för kontot.
1. Under Services väljer **Blobar**. 
1. Leta rätt på behållaren som du vill tilldela en roll och visa behållarens inställningar. 
1. Välj **åtkomstkontroll (IAM)** att visa inställningar för åtkomstkontroll för behållaren. Välj den **rolltilldelningar** flik för att se en lista över rolltilldelningar.

    ![Skärmbild som visar hur behållarinställningar för åtkomstkontroll](media/storage-auth-aad-rbac/portal-access-control-container.png)
1. Klicka på den **Lägg till rolltilldelning** för att lägga till en ny roll.
1. I den **Lägg till rolltilldelning** fönstret, Välj den roll som du vill tilldela till en Azure AD-identitet. Och sedan söka efter den identitet som du vill tilldela rollen.
1. Klicka på **Spara**. Identiteten som du tilldelats rollen visas listan under rollen. Följande bild visar till exempel att användaren nu har läsbehörighet till data i behållare med namnet *exempelbehållaren*.

    ![Skärmbild som visar listan över användare som har tilldelats en roll](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om RBAC i [vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).
- Om du vill lära dig mer om att tilldela och hantera RBAC-rolltilldelningar med Azure PowerShell, Azure CLI eller REST-API kan du läsa följande artiklar:
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC med REST API)](../../role-based-access-control/role-assignments-rest.md)
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med Azure Storage-program](storage-auth-aad-app.md).
- Ytterligare information om Azure AD-integrering för Azure-behållare och köer finns i Azure Storage-teamets blogg publicerar, [meddelande om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
