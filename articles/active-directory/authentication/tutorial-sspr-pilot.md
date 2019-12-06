---
title: Aktivera en Azure AD SSPR-pilot
description: I den här självstudien aktiverar du självåterställning av lösenord med Azure AD för en pilotgrupp med användare
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846291"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Självstudie: Utföra en pilotlansering av självåterställning av lösenord med Azure AD

I den här självstudien aktiverar du en pilotlansering av självåterställning av lösenord (SSPR) med Azure AD i din organisation och testar med ett icke-administratörskonto.

Det är viktigt att alla tester av lösen ords återställning via självbetjäning görs med konton som inte är administratörs konton. Microsoft hanterar principen för lösenordsåterställning för administratörskonton och kräver användning av starkare autentiseringsmetoder. Den här principen tillåter inte användningen av säkerhetsfrågor och svar, och kräver användning av två metoder för återställning.

> [!div class="checklist"]
> * Aktivera lösenordsåterställning via självbetjäning
> * Testa SSPR som användare

## <a name="prerequisites"></a>Krav

* Ett konto som global administratör

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som global administratör.
1. Bläddra till **Azure Active Directory** och välj **Lösenordsåterställning**.
1. Börja med en pilotgrupp genom att aktivera självåterställning av lösenord för en delmängd av användarna i din organisation.
   * På sidan **Egenskaper** , under alternativet Återställning av **lösen ord**för självbetjäning aktiverat, väljer du **markerad**och väljer en pilot grupp.
      * Endast medlemmar av den specifika Azure AD-gruppen som du utser kan använda SSPR-funktionen. Vi rekommenderar att du definierar en grupp användare och använder den här inställningen när du distribuerar funktionen för ett ”proof of concept” (POC). Kapsling av säkerhetsgrupper stöds här.
      * Se till att användarna i den grupp du valde har licensierats på rätt sätt.
   * Klicka på **Spara**
1. På sidan **Autentiseringsmetoder**
   * Ange **antalet metoder som krävs för att återställa** till **1**
   * Välj vilka **Metoder som är tillgängliga för användare** som organisationen vill tillåta. I den här självstudien markerar du kryss rutorna för att aktivera **e-post**, **mobil telefon**, **kontors telefon**, **meddelande för mobilapp**och **kod för mobilapp**.
   * Klicka på **Spara**
1. På sidan **Registrering**
   * Välj **Ja** för att **kräva att användare registrerar sig vid inloggning**.
   * Ställ in **Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** på **180**.
   * Klicka på **Spara**
1. På sidan **Meddelanden**
   * Ställ in alternativet **Meddela användare om lösenordsåterställning** på **Ja**.
   * Ställ in **Meddela alla administratörer när andra administratörer återställer sina lösenord** på **Ja**.
1. På sidan **Anpassning**
   * Microsoft rekommenderar att du ställer in **Anpassa helpdesk-länk** till **Ja** och anger antingen en e-postadress eller en URL för den webbplats där användarna kan få ytterligare hjälp från din organisation i fältet **Anpassad e-postadress eller URL till helpdesk**.
   * I den här självstudien lämnar vi **länken anpassa supportavdelningen** inställd på **Nej**.

Självåterställning av lösenord har nu konfigurerats för molnanvändarna i din pilotgrupp.

## <a name="test-sspr-as-a-user"></a>Testa SSPR som användare

Testa självåterställning av lösenord med hjälp av en testanvändare som inte är administratör och som är medlem i pilotgruppen. **Om du använder ett konto som har tilldelade administratörs roller kan autentiseringsmetoderna och numret skilja sig från det som du har valt som Microsoft hanterar administratörs principen.**

1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge.
1. Använd en testanvändare för att registrera dig för självåterställning av lösenord med hjälp av registreringsportalen på [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Använd samma testanvändare för att bläddra till portalen för självåterställning av lösenord [https://aka.ms/sspr](https://aka.ms/sspr) och försök att återställa ditt lösenord med hjälp av den information som du angav i föregående steg.
1. Du bör kunna återställa ditt lösenord.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda funktioner som du har konfigurerat i den här självstudien kan du göra följande ändring.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** och välj **Lösenordsåterställning**.
1. På sidan **Egenskaper** går du till alternativet **Self Service Password Reset Enabled** (Självåterställning av lösenord aktiverat) och väljer **Inget**.
1. Klicka på **Spara**

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat självåterställning av lösenord med Azure AD. Fortsätt till nästa självstudie för att se hur en lokal Active Directory Domain Services-infrastruktur kan integreras i självåterställningen av lösenord.

> [!div class="nextstepaction"]
> [Aktivera lokal SSPR-tillbakaskrivningsintegration](tutorial-enable-writeback.md)
