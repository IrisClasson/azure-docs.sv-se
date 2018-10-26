---
title: 'Anvisningar: Konfigurera tillbakaskrivning av lösenord för Azure AD SSPR'
description: Använd Azure AD och Azure AD Connect för tillbakaskrivning av lösenord till en lokal katalog
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e8a09a9fc87bff692b5d5b4c54f87839f2591b63
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086955"
---
# <a name="how-to-configure-password-writeback"></a>Anvisningar: Konfigurera tillbakaskrivning av lösenord

Vi rekommenderar att du använder funktionen för automatisk uppdatering av [Azure AD Connect](../hybrid/how-to-connect-install-express.md) när du använder tillbakaskrivning av lösenord.

Följande steg förutsätter att du redan har konfigurerat Azure AD Connect i din miljö med hjälp av den [Express](../hybrid/how-to-connect-install-express.md) eller [anpassade](../hybrid/how-to-connect-install-custom.md) inställningar.

1. För att konfigurera och aktivera tillbakaskrivning av lösenord loggar du in på din Azure AD Connect-server och startar konfigurationsguiden för **Azure AD Connect**.
2. På sidan **Välkommen** klickar du på **Konfigurera**.
3. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
4. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifter för global administratör och väljer sedan **Nästa**.
5. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
6. På sidan **Valfria funktioner** markerar du rutan bredvid **Tillbakaskrivning av lösenord** och väljer **Nästa**.
   ![Aktivera tillbakaskrivning av lösenord i Azure AD Connect][Writeback]
7. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
8. När du ser att konfigurationen är klar väljer du **Avsluta**.

Vanliga uppgifter för felsökning för tillbakaskrivning av lösenord finns i avsnittet [felsöka tillbakaskrivning av lösenord](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) i vår felsökningsartikel.

> [!WARNING]
> Tillbakaskrivning av lösenord att sluta fungera för kunder som använder Azure AD Connect-versioner 1.0.8641.0 och äldre när den [Azure Access Control service (ACS) är ur bruk 7 November 2018](../develop/active-directory-acs-migration.md). Azure AD Connect-versioner 1.0.8641.0 och äldre inte längre att tillåta tillbakaskrivning av lösenord vid den tidpunkten eftersom de använder ACS för den funktionen.
>
> Om du vill undvika avbrott i tjänsten, uppgradera från en tidigare version av Azure AD Connect till en nyare version finns i artikeln [Azure AD Connect: uppgradera från en tidigare version till senast](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="active-directory-permissions"></a>Active Directory-behörigheter

Konton som angetts i Azure AD Connect-verktyget måste ha följande ange om du vill ska vara i omfånget för SSPR:

* **Återställ lösenord** 
* **Ändra lösenord** 
* **Skrivbehörighet** på `lockoutTime`
* **Skrivbehörighet** på `pwdLastSet`
* **Utökade rättigheter för** på antingen:
   * Rotobjektet av *varje domän* i den skogen
   * Användaren organisationsenheter (OU) som du vill ska vara i omfånget för SSPR

Om du inte vet vilket konto beskrivs kontot refererar öppnar Azure Active Directory Connect Konfigurationsgränssnittet och väljer den **visa aktuell konfiguration** alternativet. Det konto som du behöver lägga till behörigheter till anges under **synkroniserade kataloger**.

Om du anger dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. 

> [!IMPORTANT]
> Om du inte tilldelar dessa behörigheter, sedan, visas även om tillbakaskrivning av verkar vara korrekt konfigurerade, användare felmeddelanden när de försöker hantera sina lokala lösenord från molnet.
>

> [!NOTE]
> Det kan ta upp till ungefär en timme för behörigheterna att replikeras till alla objekt i din katalog.
>

Om du vill konfigurera lämpliga behörigheter för tillbakaskrivning av lösenord ska ske, gör du följande:

1. Öppna Active Directory-användare och datorer med ett konto som har administratörsbehörighet för lämplig domän.
2. Från den **visa** menyn, se till att **avancerade funktioner** är påslagen.
3. I den vänstra rutan högerklickar du på det objekt som representerar roten för domänen och välj **egenskaper** > **Security** > **Avancerat**.
4. Från den **behörigheter** fliken **Lägg till**.
5. Välj det konto som behörigheter som används för (från Azure AD Connect-konfiguration).
6. I den **gäller** listrutan, väljer **underordnad användarobjekt**.
7. Under **behörigheter**, markerar kryssrutorna för följande alternativ:
    * **Ändra lösenord**
    * **Återställ lösenord**
8. Under **egenskaper**, markerar kryssrutorna för följande alternativ:
    * **Skriva lockoutTime**
    * **Skriva pwdLastSet**
9. Välj **Använd/OK** att tillämpa ändringarna och stänga alla öppna dialogrutor.

## <a name="next-steps"></a>Nästa steg

[Vad är tillbakaskrivning av lösenord?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Aktivera tillbakaskrivning av lösenord i Azure AD Connect"
