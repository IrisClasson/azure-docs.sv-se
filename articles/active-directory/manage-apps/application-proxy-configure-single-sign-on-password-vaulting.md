---
title: Enkel inloggning till appar med Azure AD Application Proxy | Microsoft Docs
description: Aktivera enkel inloggning för dina publicerade lokala program med Azure AD Application Proxy på Azure-portalen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c19550adf500ba91462af12b4c5f7f5e38240e67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783505"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Lösenord vaulting för enkel inloggning med programproxy

Azure Active Directory Application Proxy kan du förbättra produktiviteten genom att publicera lokala program så att fjärranslutna anställda kan få säker åtkomst till dem, för. I Azure-portalen kan du också ställa in enkel inloggning (SSO) till de här apparna. Användarna behöver bara för att autentisera med Azure AD och de kan komma åt dina företagsprogram utan att behöva logga in igen.

Programproxyn har stöd för flera [enkel inloggning lägen](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Lösenordsbaserad inloggning är avsedd för program som använder en kombination av användarnamn/lösenord för autentisering. När du konfigurerar lösenordsbaserad inloggning för ditt program kan måste användarna logga in på dina lokala program en gång. Efter det Azure Active Directory lagrar inloggningsinformation och tillhandahåller det automatiskt till programmet när dina användare få fjärråtkomst till den. 

Du bör redan har publicerats och testas din app med Application Proxy. Om inte, följer du stegen i [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md) Kom sedan tillbaka hit. 

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurera lösenord vaulting för ditt program

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory** > **företagsprogram** > **alla program**.
3. Välj den app som du vill ställa in med enkel inloggning i listan.  
4. Välj **enkel inloggning**.

   ![Välj enkel inloggning](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. SSO-läge, Välj **lösenordsbaserad inloggning**.
6. För inloggnings-URL: en, anger du URL: en för sidan där användarna anger sina användarnamn och lösenord för att logga in på din app utanför företagets nätverk. Detta kan vara en extern URL som du skapade när du har publicerat appen via programproxyn. 

   ![Välj lösenordsbaserad inloggning och anger en URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Välj **Spara**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testa din app

Gå till externa URL: en som du konfigurerade för fjärråtkomst till ditt program. Logga in med dina autentiseringsuppgifter för appen (eller autentiseringsuppgifterna för ett testkonto som du har konfigurerat med åtkomst). När du loggar in har, bör du kunna lämna appen och gå tillbaka utan att ange dina autentiseringsuppgifter igen. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra sätt att implementera [enkel inloggning](what-is-single-sign-on.md)
- Lär dig mer om [säkerhetsöverväganden för att komma åt appar med Azure AD Application Proxy](application-proxy-security.md)
