---
title: Konfigurera registrering och inloggning med ett WeChat-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med WeChat konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa44edcf009d381894a581172ea5edffefe946a0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508143"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett WeChat-konto med Azure Active Directory B2C

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

## <a name="create-a-wechat-application"></a>Skapa ett WeChat-program

Om du vill använda ett WeChat-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har ett WeChat-konto, du kan få information på [ https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrera ett WeChat-program

1. Logga in på [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) med dina autentiseringsuppgifter för WeChat.
2. Välj**管理中心**(management center).
3. Följ stegen för att registrera ett nytt program.
4. Ange `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i**授权回调域**(Motringnings-URL). Till exempel om ditt klientnamn är contoso anger du URL: en ska vara `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Kopiera den **APP-ID** och **APPNYCKELN**. Du behöver dessa att lägga till identitetsleverantören till din klient.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurera WeChat som en identitetsprovider i din klient

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *WeChat*.
6. Välj **identifiera providertyp**väljer **WeChat (förhandsversion)** , och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange det ID som du antecknade tidigare som den **klient-ID** och ange APP-NYCKELN som du registrerade som den **klienthemlighet** av den WeChat program som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din WeChat konfiguration.

