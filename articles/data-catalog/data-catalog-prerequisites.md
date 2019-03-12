---
title: Förutsättningar för Azure Data Catalog
description: Läs mer om vilka förutsättningar du behöver att komma igång med Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 788e7a453e9879d9c901448659ff1c8cfa7201ff
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548327"
---
# <a name="azure-data-catalog-prerequisites"></a>Förutsättningar för Azure Data Catalog

Du måste ta hand om några saker innan du kan ställa in Azure Data Catalog. Oroa dig inte, den här processen tar inte lång tid.

## <a name="azure-subscription"></a>Azure-prenumeration
Om du vill konfigurera Data Catalog, måste du vara ägare eller Medägare av en Azure-prenumeration.

Azure-prenumeration kan du styra åtkomsten till resurser i molnet tjänst, till exempel Data Catalog. -Prenumeration kan du styra hur resursanvändningen rapporteras, faktureras och betalas för. Varje prenumeration kan ha en separat fakturering och betalning konfiguration, så du kan ha prenumerationer och planer som varierar beroende på avdelning, projekt, lokalkontor och så vidare. Varje molntjänst tillhör en prenumeration och du måste ha en prenumeration innan du installerar Data Catalog. Mer information finns i [Hantera konton, prenumerationer och administrativa roller](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Om du vill konfigurera Data Catalog, måste du vara inloggad med ett användarkonto för Azure Active Directory (AD Azure).

Azure AD tillhandahåller ett enkelt sätt för ditt företag att hantera identitet och åtkomst, både i molnet och lokalt. Användare kan använda ett arbets- eller skolkonto för enkel inloggning till alla moln och lokala webbprogram. Data Catalog använder Azure AD för att autentisera inloggningen. Mer information finns i [vad är Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> Med hjälp av den [Azure-portalen](https://portal.azure.com/), kan du logga in med ett personligt microsoftkonto eller ett Azure Active Directory arbets- eller skolkonto. Du ställer in Data Catalog genom att använda antingen Azure portal eller [Data Catalog-portalen](https://www.azuredatacatalog.com), måste du logga in med ett Azure Active Directory-konto, inte ett personligt konto.
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory-principkonfiguration
Du kan stöta på en situation där du kan logga in på Data Catalog-portalen, men när du försöker logga in på registreringsverktyget, du får ett felmeddelande som hindrar dig från att logga in. Det här problemet kan inträffa när du är ansluten till företagets nätverk eller det kan uppstå bara när du ansluter från utanför företagets nätverk.

Registreringsverktyget använder formulärautentisering för att verifiera dina autentiseringsuppgifter mot Active Directory. För att hjälpa dig logga in har aktivera en Active Directory-administratör formulärautentisering i den globala autentiseringsprincipen.

I den globala autentiseringsprincipen kan autentiseringsmetoder aktiveras separat för intranät- och Extranätsanslutningar, som visas i följande skärmbild. Inloggningsfel kan uppstå om formulärautentisering inte är aktiverad för det virtuella nätverket som du ansluter.

 ![Active Directory Global Authentication Policy](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Nästa steg
Mer information finns i [konfigurera autentiseringsprinciper](https://technet.microsoft.com/library/dn486781.aspx).
