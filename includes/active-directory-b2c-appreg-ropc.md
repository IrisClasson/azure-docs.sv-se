---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f941f67105227b3973386b015344d5fa787c3485
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84317409"
---
Om du vill registrera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre **program (äldre)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregtraining)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *ROPC_Auth_app*.
1. Lämna de andra värdena som de är och välj sedan **Registrera**.
1. Registrera **program-ID: t (Client)** för användning i ett senare steg.
1. Under **Hantera**väljer du **autentisering**.
1. Välj **testa den nya upplevelsen** (om den visas).
1. Under **standard klient typ**väljer du **Ja** för att behandla programmet som en offentlig klient. Den här inställningen krävs för ROPC-flödet.
1. Välj **Spara**.
1. I den vänstra menyn väljer du **manifest** för att öppna manifest redigeraren. 
1. Ange attributet **oauth2AllowImplicitFlow** till *True*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Välj **Spara**.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan **Lägg till**.
1. Ange ett namn på programmet. Till exempel *ROPC_Auth_app*.
1. För **Native Client**väljer du **Ja**.
1. Lämna de andra värdena som de är och välj sedan **skapa**.
1. Registrera **program-ID** för användning i ett senare steg.