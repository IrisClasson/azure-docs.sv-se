---
title: Konfigurera registrering och inloggning med ett LinkedIn-konto med Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med LinkedIn-konton i dina program med Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8388baf88f5bb723e5b0e47bc93b100d5ce8e3e2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159808"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett LinkedIn-konto med Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Skapa ett program med LinkedIn

Om du vill använda en LinkedIn-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program i din klient som representerar den. Om du inte redan har en LinkedIn-konto, kan du hämta den på [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Logga in på den [LinkedIn utvecklare webbplats](https://www.developer.linkedin.com/) med autentiseringsuppgifterna för ditt LinkedIn-konto.
2. Välj **Mina appar**, och klicka sedan på **skapa program**.
3. Ange **företagsnamn**, **programnamn**, **Programbeskrivning**, **Programlogotyp**, **programanvändning** , **Webbadress**, **företags-e-**, och **Företagstelefon**.
4. Godkänn den **LinkedIn API användningsvillkor** och klicka på **skicka**.
5. Kopiera värdena för **klient-ID** och **Klienthemlighet**. Du hittar dem under **autentiseringsnycklar**. Du måste båda för att konfigurera LinkedIn som en identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
6. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **behörighet-URL: Omdirigeringswebbadresser**. Ersätt `your-tenant-name` med namnet på din klient. Du måste använda gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C. Välj **Lägg till**, och klicka sedan på **uppdatering**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurera ett LinkedIn-konto som identitetsprovider

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
5. Ange en **namn**. Ange till exempel *LinkedIn*.
6. Välj **identifiera providertyp**väljer **LinkedIn**, och klicka på **OK**.
7. Välj **ställa in den här identitetsprovidern** och ange klient-Id som du antecknade tidigare som den **klient-ID** och ange Klienthemligheten som du registrerade som den **klienthemlighet**av programmet för LinkedIn-konto som du skapade tidigare.
8. Klicka på **OK** och klicka sedan på **skapa** att spara din konfiguration för LinkedIn-konto.

