---
title: Azure AD SSPR från Windows 10-inloggningsskärmen
description: I den här självstudien aktiverar du lösenordsåterställning på inloggningsskärmen för Windows 10 för att minska antalet samtal till supportavdelningen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: acd100ccc3aafc2de1f2c7970ff9437c92156b17
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861520"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Självstudie: Azure AD-lösenordsåterställning från inloggningsskärmen

I den här självstudien gör du så att användare kan återställa sina lösenord från Windows 10-inloggningsskärmen. Med den nya April 2018-uppdateringen för Windows 10 kan användare med **Azure AD-anslutna** eller **Hybrid Azure AD-anslutna** enheter använda länken ”Återställ lösenord” på sin inloggningsskärm. När användarna klickar på den här länken kommer de till samma lösenordsåterställning via självbetjäning (SSPR) som de är vana vid. Om en användare har låsts ute låsa konton i den lokala Active Directory inte upp av den här processen.

> [!div class="checklist"]
> * Konfigurera länken Återställ lösenord med Intune
> * Konfigurera med hjälp av Windows-registret som alternativ lösning
> * Förstå vad användarna ser

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du måste köra minst Windows 10, versionen med uppdatering från april 2018, och enheterna måste vara antingen:
   * [Azure AD-anslutna](../device-management-azure-portal.md) eller
   * [Hybrid Azure AD-anslutna](../device-management-hybrid-azuread-joined-devices-setup.md) med en nätverksanslutning till en domänkontrollant.
* Du måste aktivera självbetjäning av lösenordsåterställning med Azure AD.
* Om Windows 10-enheter är bakom en proxyserver eller brandvägg, måste du lägga till URL: er, `passwordreset.microsoftonline.com` och `ajax.aspnetcdn.com` till HTTPS-trafik (port 443) tillåtna URL-listan.
* SSPR för Windows 10 stöds endast med datornivå proxyservrar
* Granska begränsningarna nedan innan du provar den här funktionen i din miljö.
* Se till att webb-cachen rensats för det inbyggda administratörskontot innan du utför CopyProfile steg innan sysprep om du använder en bild. Mer information om detta finns i supportartikeln [prestanda dåligt när du använder anpassad standardanvändarprofil](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).

## <a name="configure-reset-password-link-using-intune"></a>Konfigurera länken Återställ lösenord med Intune

Att distribuera konfigurationsändringen för att aktivera lösenordsåterställning från inloggningsskärmen med Intune är den mest flexibla metoden. Med Intune kan du distribuera konfigurationsändringen till en särskild grupp av datorer som du definierar. Den här metoden kräver Intune-registrering av enheten.

### <a name="create-a-device-configuration-policy-in-intune"></a>Skapa en princip för enhetskonfiguration i Intune

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Intune**.
2. Skapa en ny profil för enhetskonfiguration genom att gå till **Enhetskonfiguration** > **Profiler** > **Skapa profil**
   * Ge profilen ett beskrivande namn
   * Du kan också ange en beskrivning av profilen
   * Plattform **Windows 10 och senare**
   * Profiltyp **Anpassad**

3. Konfigurera **inställningar**
   * **Lägg till** följande OMA-URI-inställning för att aktivera länken Återställ lösenord
      * Ange ett beskrivande namn som förklarar vad inställningen gör
      * Du kan också ange en beskrivning av inställningen
      * **OMA-URI** inställt på `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Datatyp** inställt på **Integer**
      * **Värde** inställt på **1**
      * Klicka på **OK**
   * Klicka på **OK**
4. Klicka på **Skapa**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Tilldela en princip för enhetskonfiguration i Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Skapa en grupp om du vill använda enhetskonfigurationsprincip för

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Azure Active Directory**.
2. Gå till **Användare och grupper** > **Alla grupper** > **Ny grupp**
3. Ange ett namn på gruppen. Under **Medlemstyp** väljer du **Tilldelad**
   * Under **Medlemmar** väljer du de Azure AD-kopplade Windows 10-enheter du vill tillämpa principen på.
   * Klicka på **Välj**
4. Klicka på **Skapa**

Mer information om att skapa grupper finns i artikeln [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Tilldela enhetskonfigurationsprincip till enhetsgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Intune**.
2. Leta reda på den enhetskonfigurationsprofil du skapade tidigare genom att gå till **Enhetskonfiguration** > **Profiler** > Klicka på profilen du skapade tidigare
3. Tilldela profilen till en grupp med enheter 
   * Klicka på **Tilldelningar** > under **Ta med** > **Välj grupper att ta med**
   * Välj gruppen du skapade tidigare och klicka på **Välj**
   * Klicka på **Spara**

   ![Överlåtelse][Assignment]

Du har nu skapat och tilldelat en enhetskonfigurationsprincip för att aktivera länken Återställ lösenord på inloggningsskärmen med Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Konfigurera länken Återställ lösenord med registret

1. Logga in på Windows-datorn med autentiseringsuppgifterna för administratören
2. Kör **regedit** som administratör
3. Ange följande registernyckel
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Vad ser användarna

Vad förändras för användaren när principen är konfigurerad och tilldelad? Hur vet de att de kan återställa sitt lösenord på inloggningsskärmen?

![LoginScreen][LoginScreen]

När användarna försöker logga in ser de nu en länk för återställning av lösenord som öppnar självbetjäningen för återställning av lösenord på inloggningsskärmen. Via den här funktionen kan användarna återställa sina lösenord utan att de behöver använda en annan enhet för att få åtkomst till webbläsaren.

Dina användare får hjälp med att använda funktionen i [Reset your work or school password](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in) (Återställa ditt arbets- eller skollösenord)

Azure AD-granskningsloggen innehåller information om IP-adressen och klienttypen där lösenordsåterställningen har gjorts.

![Exempel på lösenordsåterställning för inloggningssida i Azure AD-granskningsloggen](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

När användarna återställer sina lösenord från inloggningsskärmen för en Windows 10-enhet skapas ett tillfälligt lågprivilegierat konto med namnet ”defaultuser1”. Det här kontot används för att skydda processen för lösenordsåterställning. Själva kontot har ett slumpmässigt genererat lösenord som inte visas för att logga in enheten och tas bort automatiskt när användaren återställer sitt lösenord. Flera ”defaultuser”-profiler kan finnas men kan ignoreras.

## <a name="limitations"></a>Begränsningar

När du testar funktionen med Hyper-V visas inte länken ”Återställ lösenord”.

* Gå till den virtuella dator du använder för att testklicka på **Visa** och avmarkera **Avancerad session**.

När du testar funktionen med hjälp av Fjärrskrivbord eller en förbättrad VM-session visas inte länken ”Återställ lösenord”.

* Återställning av lösenord stöds inte från ett Fjärrskrivbord.

Om Ctrl + Alt + Del krävs av principen i versioner av Windows 10 innan 1809 fungerar inte **Återställ lösenord**.

Om meddelanden för låsskärmen är avstängda fungerar inte **Återställ lösenord**.

Det har bekräftats att följande principinställningar stör möjligheten att återställa lösenord

   * HideFastUserSwitching har angetts på aktiverat eller 1
   * DontDisplayLastUserName har angetts på aktiverat eller 1
   * NoLockScreen har angetts på aktiverat eller 1
   * EnableLostMode har angetts på enheten
   * Explorer.exe har ersatts med ett anpassat gränssnitt

Den här funktionen fungerar inte för nätverk som distribuerar nätverksautentisering 802.1x och alternativet ”Utför omedelbart innan användaren loggar in”. Nätverk med nätverksautentiseringen 802.1x distribuerad rekommenderas att använda datorautentisering för att aktivera funktionen.

För Hybrid-domänanslutna scenarier slutförs SSPR-arbetsflödet utan att en Active Directory-domänkontrollant behövs. Om en användare har slutfört processen för lösenordsåterställning när kommunikation till en Active Directory-domänkontrollant inte är ansluten, till exempel när du arbetar via fjärranslutning, kommer användaren inte att kunna logga in på enheten förrän enheten kan kommunicera med en domänkontrollant och uppdatera cachelagrade autentiseringsuppgifter. **Anslutning med en domänkontrollant krävs när lösenordet ska användas för första gången**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda funktioner som du har konfigurerat i den här kursen kan du ta bort Intune-enhetens konfigurationsprofil som du skapade eller registernyckeln.

## <a name="next-steps"></a>Nästa steg

I den här självstudien gjorde du så att användare kan återställa sina lösenord från Windows 10-inloggningsskärmen. Fortsätt till nästa självstudie för att se hur Azure Identity Protection kan integreras i funktionerna för självåterställning av lösenord samt multifaktorautentisering.

> [!div class="nextstepaction"]
> [Utvärdera risk vid inloggning](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Tilldela principer för Intune-enhetskonfiguration till en grupp med Windows 10-enheter"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Länken Återställ lösenord på Windows 10-inloggningsskärmen"
