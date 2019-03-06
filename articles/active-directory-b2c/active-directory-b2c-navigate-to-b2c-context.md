---
title: Växla till en B2C-klient i Azure Active Directory B2C | Microsoft Docs
description: Så här byter du till kontexten för din Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9315b3a5e1641098daf2c7dadf9fa4f09d0cb2a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438516"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Byta till en Azure AD B2C-klient

Om du vill konfigurera Azure AD B2C måste du först byta till samma kontext som Azure AD B2C-klienten finns i.

## <a name="log-into-azure-ad-b2c-tenant"></a>Logga in på Azure AD B2C-klienten

Om du vill navigera till din Azure AD B2C-klient måste du vara inloggad i Azure Portal som global administratör för Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Växla mellan klienterna genom att klicka på din e-postadress eller på bilden i det övre högra hörnet.
1. I den `Directory`-lista som visas väljer du den Azure AD B2C-klient som du vill hantera.

Azure-portalen uppdateras.  Du är nu inloggad på Azure Portal, i samma kontext som din Azure AD B2C-klient.

## <a name="navigate-to-the-b2c-features-pane"></a>Gå till B2C-funktionsfönstret

1. Klicka på **Bläddra** i det vänstra navigeringsfönstret.
1. Klicka på **Alla tjänster** och sök efter `Azure AD B2C` i det vänstra navigeringsfönstret.  (Klicka på stjärnan till vänster om Azure AD B2C för att fästa vid vänster startsida)
1. Klicka på **Azure AD B2C** för att komma åt B2C-funktionsfönstret.
   
    ![Skärmbild av Bläddra till B2C-funktionsfönstret](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Du måste vara global administratör för B2C-klienten för att kunna komma åt B2C-funktionsfönstret. En global administratör från en annan klient eller en användare från en klient kan inte komma åt det.  Du kan växla till B2C-klienten med klientväxlaren i det övre högra hörnet i Azure Portal.
