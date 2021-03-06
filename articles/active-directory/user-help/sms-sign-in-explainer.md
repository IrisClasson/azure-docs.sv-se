---
title: SMS-inloggning användar upplevelse för telefonnummer (för hands version) – Azure AD
description: Lär dig mer om SMS-inloggning användar upplevelse för nya eller befintliga telefonnummer
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 864225238c10ab2fda96e95448790201cc8a16ae
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423005"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Använd ditt telefonnummer som användar namn (förhands granskning)

När du registrerar en enhet får du till gång till din organisations tjänster och ger inte din organisation åtkomst till din telefon. Om du är administratör kan du hitta mer information i [Konfigurera och aktivera användare för SMS-baserad autentisering](../authentication/howto-authentication-sms-signin.md).

Om din organisation inte har gjort SMS-inloggning tillgänglig visas inget alternativ för det när du registrerar en telefon med ditt konto.  

## <a name="when-you-have-a-new-phone-number"></a>När du har ett nytt telefonnummer

Om du får en ny telefon eller nytt nummer och du registrerar den med en organisation som SMS-inloggningen är tillgänglig för, får du den normala registrerings processen:

1. Välj **Lägg till metod**.
1. Välj **telefon**.
1. Ange telefonnummer och välj **text till mig en kod**.
1. När du har angett koden väljer du **Nästa**.
1. Du får ett meddelande om att "SMS har verifierats. Din telefon har registrerats. "

> [!Important]
> På grund av ett känt problem i för hands versionen registreras inte numret för SMS-inloggning under kort tid för att lägga till telefonnummer. Du måste logga in med det tillagda numret och sedan följa anvisningarna för att registrera numret för SMS-inloggning.

### <a name="when-the-phone-number-is-in-use"></a>När telefonnumret används

Om du försöker använda ett telefonnummer som någon annan i din organisation använder visas följande meddelande:

![Fel meddelande när ditt telefonnummer redan används](media/sms-sign-in-explainer/sms-sign-in-error.png)

Kontakta din administratör för att åtgärda problemet.

## <a name="when-you-have-an-existing-number"></a>När du har ett befintligt nummer

Om du redan använder ett telefonnummer med en organisation och använder ditt telefonnummer när ett användar namn blir tillgängligt kan du använda följande steg för att logga in.

1. När SMS-inloggning är tillgängligt visas en banderoll som frågar om du vill aktivera telefonnumret för SMS-inloggning:

    [![Banderoll för att aktivera SMS-inloggning för ett telefonnummer.](media/sms-sign-in-explainer/sms-sign-in-banner.png)](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Dessutom visas knappen **Aktivera** om du väljer cirkumflexen på panelen telefon metod:

    [![Banderoll för att aktivera SMS-inloggning för ett telefonnummer.](media/sms-sign-in-explainer/sms-sign-in-phone-method.png)](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Aktivera metoden genom att välja **Aktivera**. Du uppmanas att bekräfta åtgärden:

    ![Bekräftelse dialog ruta för att aktivera SMS-inloggning för ett telefonnummer](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Välj **Aktivera**.

## <a name="when-you-remove-your-phone-number"></a>När du tar bort ditt telefonnummer

1. Om du vill ta bort telefonnumret väljer du knappen Ta bort i rutan SMS-inloggningens telefon metod.

    [![Banderoll för att ta bort SMS-inloggning för ett telefonnummer.](media/sms-sign-in-explainer/sms-sign-in-delete-method.png)](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. När du uppmanas att bekräfta åtgärden väljer du **OK**.

Du kan inte ta bort ett telefonnummer som används som standard-inloggnings metod. Om du vill ta bort numret måste du ändra standard inloggnings metod och sedan ta bort telefonnumret igen.
