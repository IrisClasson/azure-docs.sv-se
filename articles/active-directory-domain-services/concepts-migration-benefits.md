---
title: Fördelar med klassisk distributions migrering i Azure AD Domain Services | Microsoft Docs
description: Läs mer om fördelarna med att migrera en klassisk distribution av Azure Active Directory Domain Services till distributions modellen för Resource Manager
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 837ca3afaf014c41fded09fa8342eed1c13e5dcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734936"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Fördelar med migrering från den klassiska distributions modellen till Resource Manager i Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) kan du migrera en befintlig hanterad domän som använder den klassiska distributions modellen till distributions modellen för Resource Manager. Azure AD DS-hanterade domäner som använder distributions modellen för Resource Manager ger ytterligare funktioner, till exempel detaljerade lösen ords principer, gransknings loggar och konto utelåsnings skydd.

Den här artikeln beskriver fördelarna med migrering. Information om hur du kommer igång finns i [migrera Azure AD Domain Services från den klassiska virtuella nätverks modellen till Resource Manager][howto-migrate].

> [!NOTE]
> I 2017 blev Azure AD Domain Services tillgänglig som värd i ett Azure Resource Manager nätverk. Sedan dess har vi kunnat bygga en säkrare tjänst med Azure Resource Manager moderna funktioner. Eftersom Azure Resource Manager distributioner fullständigt ersätter klassiska distributioner, kommer Azure AD DS klassiska virtuella nätverks distributioner att dras tillbaka den 1 mars 2023.
>
> Mer information finns i meddelande om [officiellt utfasning](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Fördelar med migrering

Migreringsprocessen tar en befintlig hanterad domän som använder den klassiska distributions modellen och fortsätter att använda distributions modellen för Resource Manager. När du migrerar en hanterad domän från den klassiska distributions modellen till Resource Manager undviker du att behöva ansluta datorer till den hanterade domänen eller ta bort den hanterade domänen och skapa en från grunden. De virtuella datorerna fortsätter att vara anslutna till den hanterade domänen i slutet av migreringsprocessen.

Efter migreringen tillhandahåller Azure AD DS många funktioner som bara är tillgängliga för domäner som använder distributions modellen för Resource Manager, till exempel följande:

* [Stöd för detaljerade lösen ords principer][password-policy].
* Snabbare synkronisering mellan Azure AD och Azure AD Domain Services.
* Två nya [attribut som synkroniseras från Azure AD][attributes]  -  *Manager* och *Anställningsnr*.
* Åtkomst till domänkontrollanter med högre strömförsörjning när du [uppgraderar SKU: n][skus].
* Skydd för AD-konto utelåsning.
* [E-postmeddelanden för aviseringar på din hanterade domän][email-alerts].
* [Använd Azure-arbetsböcker och Azure Monitor för att Visa gransknings loggar och inloggnings aktivitet][workbooks].
* [Azure-tillgänglighetszoner][availability-zones]i regioner som stöds.
* Integrering med andra Azure-produkter som [Azure Files][azure-files], [HD Insights][hd-insights]och [Windows Virtual Desktop][wvd].
* Support har åtkomst till mer telemetri och kan hjälpa dig att felsöka mer effektivt.
* Kryptering i vila med [Azure Managed disks][managed-disks] för data på de hanterade domän kontrol Lanterna.

Hanterade domäner som använder en distributions modell för Resource Manager hjälper dig att hålla dig uppdaterad med de senaste nya funktionerna. Nya funktioner är inte tillgängliga för hanterade domäner som använder den klassiska distributions modellen.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [migrera Azure AD Domain Services från den klassiska virtuella nätverks modellen till Resource Manager][howto-migrate].

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/windows/managed-disks-overview.md
