---
title: Azure Active Directory-enhetshantering vanliga frågor och svar | Microsoft Docs
description: Azure Active Directory enhetshantering vanliga frågor och svar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 9402147e2dab7fbf52fc893f339f6f3b8e112377
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515649"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory-enhetshantering vanliga frågor och svar

**F: Jag har registrerat enheten nyligen. Varför visas inte enheten under Mina användarinformation i Azure-portalen? Eller varför enhetens ägare markeras som ej tillämpligt för hybrid Azure AD-anslutna enheter? ** 
 **S:** Windows 10-enheter som är hybrid Azure AD-anslutna visas inte under användarenheterna.
Du måste använda vy över alla enheter i Azure portal. Du kan också använda PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet.

Endast följande enheter visas under användarenheterna:

- Alla personliga enheter som inte är hybrid Azure AD-anslutna. 
- Alla icke - Windows 10 / Windows Server 2016-enheter.
- Alla icke-Windows-enheter 

--- 

**F: hur vet jag enhetstillstånd för registrering av klienten är?**

**S:** du kan använda Azure-portalen, gå till alla enheter och Sök efter enheten med enhets-ID. Kontrollera värdet under typen kopplingskolumn. Ibland kunde enheten har återställa eller avbildas på nytt. Det är därför viktigt att också kontrollera status för enhetsregistreringen på enheten för:

- Kör dsregcmd.exe/status för Windows 10 och Windows Server 2016 eller senare enheter.
- För äldre OS-versioner, ”%programFiles%\Microsoft arbetsplats Join\autoworkplace.exe” som kör

---

**F: jag se enhetspost under användarinformation i Azure portal och kan se tillståndet som registrerats på enheten. Kan jag in korrekt för att använda villkorlig åtkomst?**

**S:** anslutningstillståndet enheten visas deviceID, måste överensstämma med den i Azure AD och utvärderingen kriterier för villkorlig åtkomst. Mer information finns i [kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

---

**F: Jag har tagits bort i Azure portal eller via Windows PowerShell, men det lokala tillståndet på enheten säger att den fortfarande är registrerad?**

**S:** det här är avsiktligt. Enheten har inte åtkomst till resurser i molnet. 

Om du vill registrera igen, måste vara en manuell åtgärd som ska vidtas på enheten. 

Rensa anslutningstillståndet från Windows 10 och Windows Server 2016 som är lokal AD som är anslutna till en domän:

1.  Öppna Kommandotolken som administratör.

2.  Typ `dsregcmd.exe /debug /leave`

3.  Logga ut och logga in att utlösa den schemalagda aktiviteten som registreras med Azure AD igen. 

För äldre Windows OS-versioner som är en lokal AD-ansluten till domänen:

1.  Öppna Kommandotolken som administratör.
2.  Skriv `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Skriv `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**F: Varför ser jag dubblettenheten poster i Azure-portalen?**

**S:**

-   För Windows 10 och Windows Server 2016, om det finns upprepade försök till frånkoppling från och återansluta till samma enhet, kan det finnas dubblettposter. 

-   Om du har använt Lägg till arbets- eller Skolkonto, skapas en ny enhetspost med samma enhetsnamn i varje windowsanvändare som använder Lägg till arbets- eller Skolkonto.

-   För äldre Windows OS-versioner som är en lokal skapar AD ansluten till domänen med hjälp av automatisk registrering en ny enhetspost med samma enhetsnamn för varje domänanvändare som loggar in på enheten. 

-   En domänansluten dator i Azure AD som har rensats, installeras om och återkopplas med samma namn, visas som en annan post med samma enhetsnamn.

---

**F: Varför kan en användare fortfarande komma åt resurser från en enhet som jag har inaktiverats i Azure-portalen?**

**S:** det kan ta upp till en timme innan en återkalla som ska användas.

>[!Note] 
>För registrerade enheter rekommenderar vi att rensa en enhet för att säkerställa att användarna inte kan komma åt resurserna. Mer information finns i [registrera enheter för hantering i Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 
---

# <a name="azure-ad-join-faq"></a>Azure AD Join vanliga frågor och svar

**F: hur jag frånkoppling från en enhet i Azure AD har anslutits lokalt på enheten?**

**S:** 
- För enheter för hybrid Azure AD har anslutits kan du se till att inaktivera automatisk registrering, så att den schemalagda aktiviteten inte registrera enheten igen. Därefter öppnar du Kommandotolken som administratör och skriv `dsregcmd.exe /debug /leave`. Det här kommandot kan också köras som ett skript på flera enheter vid frånkoppling från gruppvis.

- För ren Azure AD har anslutits enheter, kontrollera att du har en offline lokal administratör konto eller skapa ett, som du kan inte logga in med autentiseringsuppgifter för alla Azure AD-användare. Gå sedan till **inställningar** > **konton** > **åtkomst till arbete eller skola**. Välj ditt konto och klicka på **Disconnect**. Följ anvisningarna och ange autentiseringsuppgifterna som lokal administratör när du tillfrågas. Starta om enheten för att slutföra processen frånkoppling.

---

**F: Mina användare kan inte söka efter skrivare från Azure AD har anslutits enheter. Hur kan jag aktivera utskrift från Azure AD har anslutits enheter?**

**S:** distribuera skrivare för Azure AD har anslutits enheter finns i [Hybrid cloud utskrifts](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Du behöver en lokal Windows Server att distribuera hybrid cloud utskrifts. Molnbaserad utskriftstjänster är för närvarande inte tillgänglig. 

---

**F: hur ansluter jag till en fjärransluten Azure AD ansluten enhet? ** 
 **S:** finns i artikeln https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc information.

---

**F: Varför Mina användare ser ”du inte kan ta dig dit härifrån”?**

**S:** om du har konfigurerat vissa regler för villkorlig åtkomst för att kräva en specifik enhetstillstånd och enheten inte uppfyller villkoren, användare blockeras och ser det här meddelandet. Utvärdera princip-regler för villkorlig åtkomst och se till att enheten ska kunna uppfylla kriterier för att undvika det här meddelandet.

---

**F: Varför gör vissa av Mina användare inte get MFA anvisningarna på Azure AD-anslutna enheter?**

**S:** om användaren ansluter till eller registrerar en enhet med Azure AD med hjälp av Multi-Factor Authentication kan själva enheten kommer att bli en betrodd andra faktor för en viss användare. Därefter när samma användare loggar in på enheten och har åtkomst till ett program, Azure AD tar hänsyn till enheten som en andra faktor och gör att användaren sömlöst kan komma åt sina program utan ytterligare MFA-frågor. Det här beteendet gäller inte för alla användare som loggar in på den aktuella enheten så att alla andra användare med åtkomst till enheten skulle fortfarande en fråga med en MFA-kontrollen innan åtkomst till program som kräver MFA.

---

**F: Varför visas ett meddelande om ”användarnamnet eller lösenordet är felaktigt” för en enhet som jag precis har anslutit till Azure AD?**

**S:** vanliga orsaker till det här scenariot:

- Dina autentiseringsuppgifter är inte längre giltig.

- Datorn kan inte kommunicera med Azure Active Directory. Sök efter eventuella problem med nätverksanslutningen.

- Federerad inloggningar kräver federationsservern som stöd för WS-Trust-slutpunkter aktiverade och kan nås. 

- Du har aktiverat Pass via autentisering och användaren har ett tillfälligt lösenord som måste ändras vid inloggning.

---

**F: Varför visas den ”Oops... uppstod ett fel”! dialogrutan när jag försöker göra Azure AD join min dator?**

**S:** detta beror på hur du konfigurerar Azure Active Directory-registrering med Intune. Kontrollera att den användare som försöker att göra Azure AD-anslutning har rätt Intune-licens. Mer information finns i [konfigurera enhetshantering för Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**F: varför min försöker Azure AD join en dator misslyckas även om jag inte får någon information om fel?**

**S:** en trolig orsak är att användaren är inloggad till enheten med det inbyggda lokala administratörskontot. Skapa ett annat lokala konto innan du använder Azure Active Directory Join för att slutföra installationen. 

---

# <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD Join vanliga frågor och svar

**F: var kan jag hitta felsökning information för felsökning av hybrid Azure AD join?**

**S:** information om felsökning finns:

- [Felsöka automatisk registrering av domän domänanslutna datorer till Azure AD – Windows 10 och Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Felsöka automatisk registrering av domän domänanslutna datorer till Azure AD för Windows-klientversioner](troubleshoot-hybrid-join-windows-legacy.md)
 

---

# <a name="azure-ad-register-faq"></a>Registrera dig för Azure AD vanliga frågor och svar

**F: kan jag registrera Android eller iOS BYOD-enheter?**

**S:** Ja, men endast med enhetsregistreringstjänsten för Azure och för kunder med hybriddistributioner. Det stöds inte med den lokala enhetsregistreringstjänsten i AD FS.

**F: hur kan jag registrera en macOS-enhet?**

**S:** att registrera macOS-enhet:

1.  [Skapa en efterlevnadsprincip](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiera principer för villkorlig åtkomst för macOS-enheter](../active-directory-conditional-access-azure-portal.md) 

**Anmärkning:**

- Användare som ingår i din princip för villkorlig åtkomst måste en [version av Office som stöds för macOS](../conditional-access/technical-reference.md#client-apps-condition) att komma åt resurser. 

- Under den första åtkomstförsök uppmanas användarna att registrera enheten med hjälp av Företagsportalen.

---
