---
title: 'Azure AD Connect: Migrera från federation till PHS för Azure AD | Microsoft Docs'
description: Den här artikeln innehåller information om hur du flyttar din hybridmiljön identitet från federation till synkronisering av lösenordshash.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1d2c587129f9f9f09c8f3871748449dc7b1755b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474024"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrera från federation till synkronisering av lösenordshash för Azure Active Directory

Den här artikeln beskriver hur du flyttar din organisation domäner från Active Directory Federation Services (AD FS) till synkronisering av lösenordshash.

Du kan [ladda ned den här artikeln](https://aka.ms/ADFSTOPHSDPDownload).

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Förutsättningar för att migrera till synkronisering av lösenordshash

Följande krävs för att migrera från med hjälp av AD FS att använda synkronisering av lösenordshash.

### <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

Du bör ha minst ska kunna genomföra stegen för att migrera till synkronisering av lösenordshash [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Den här versionen innehåller betydande förändringar i hur inloggning konverteringen utförs och minskar den totala tid att migrera från Federation till autentisering i molnet från potentiellt timmar och minuter.


> [!IMPORTANT]
> Du kan läsa i inaktuella dokumentation, verktyg och bloggar att användarkonvertering krävs när du konverterar domäner från federerad identitet till hanterad identitet. *Konvertera användare* inte längre behövs. Microsoft arbetar med för att uppdatera dokumentation och verktyg för att ändringen visas.

Om du vill uppdatera Azure AD Connect måste du slutföra stegen i [Azure AD Connect: Uppgradera till den senaste versionen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Lösenord hash-synkronisering krävs behörigheter

Du kan konfigurera Azure AD Connect med standardinställningar eller en anpassad installation. Om du har använt alternativet Anpassad installation den [nödvändiga behörigheter](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) för lösenords-hash synkronisering kanske inte är på plats.

Azure AD Connect Active Directory Domain Services (AD DS)-tjänstkontot kräver följande behörigheter att synkronisera lösenords-hash:

* Replikera katalogändringar
* Replikera katalogen ändras alla

Nu är dags att kontrollera att behörigheterna är på plats för alla domäner i skogen.

### <a name="plan-the-migration-method"></a>Planera migreringsmetod

Du kan välja från två metoder för att migrera från federerad Identitetshantering till synkronisering av lösenordshash och sömlös enkel inloggning (SSO). Vilken metod du använder beror på hur din instans av AD FS ursprungligen konfigurerades.

* **Azure AD Connect**. Om du ursprungligen konfigurerades AD FS med hjälp av Azure AD Connect, du *måste* ändra till synkronisering av lösenordshash med hjälp av Azure AD Connect-guiden.

   Azure AD Connect körs automatiskt den **Set-MsolDomainAuthentication** cmdlet när du ändrar inloggningsmetod för användare. Azure AD Connect unfederates automatiskt alla verifierade federerade domäner i Azure AD-klienten.

   > [!NOTE]
   > För närvarande om du ursprungligen använde Azure AD Connect kan konfigurera AD FS kan du undvika unfederating alla domäner i din klientorganisation när du ändrar den användaren logga in till synkronisering av lösenordshash. ‎
* **Azure AD Connect med PowerShell**. Du kan använda den här metoden om du inte har ursprungligen konfigurera AD FS med hjälp av Azure AD Connect. För det här alternativet kan ändra du fortfarande metoden användare logga in via Azure AD Connect-guiden. Den grundläggande skillnaden med det här alternativet är att guiden automatiskt inte körs den **Set-MsolDomainAuthentication** cmdlet. Med det här alternativet har du fullständig kontroll över vilka domäner konverteras och i vilken ordning.

Slutför stegen i följande avsnitt för att förstå vilken metod du ska använda.

#### <a name="verify-current-user-sign-in-settings"></a>Kontrollera aktuell användare logga in inställningarna

För att verifiera din aktuella inloggning användarinställningar:

1. Logga in på den [Azure AD-portalen](https://aad.portal.azure.com/) med hjälp av ett globalt administratörskonto.
2. I den **användarinloggning** verifierar du följande inställningar:
   * **Federation** är inställd på **aktiverad**.
   * **Sömlös enkel inloggning** är inställd på **inaktiverad**.
   * **Direktautentisering** är inställd på **inaktiverad**.

   ![Skärmbild av inställningarna i avsnittet för Azure AD Connect-användare](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verifiera konfigurationen av Azure AD Connect

1. Öppna Azure AD Connect på din Azure AD Connect-servern. Välj **Konfigurera**.
2. På den **ytterligare uppgifter** väljer **visa aktuell konfiguration**, och välj sedan **nästa**.<br />

   ![Skärmbild av konfigurationsalternativet Visa aktuella valde på sidan ytterligare uppgifter](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. På den **granska din lösning** , Lägg den **lösenordshashsynkronisering** status.<br /> 

   * Om **lösenordshashsynkronisering** är inställd på **inaktiverad**, utför stegen i den här artikeln om du vill aktivera den.
   * Om **lösenordshashsynkronisering** är inställd på **aktiverad**, du kan hoppa över avsnittet **steg 1: Aktivera lösenordshashsynkronisering** i den här artikeln.
4. På den **granska din lösning** , bläddrar du till **Active Directory Federation Services (AD FS)** .<br />

   * Om AD FS-konfigurationen visas i det här avsnittet, kan du på ett säkert sätt anta att AD FS ursprungligen konfigurerades med hjälp av Azure AD Connect. Du kan konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av Azure AD Connect **ändra användarinloggning** alternativet. Processen beskrivs i avsnittet **alternativ A: Växla från federation till lösenordshashsynkronisering med Azure AD Connect**.
   * Om AD FS inte visas i de aktuella inställningarna, måste du manuellt konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av PowerShell. Mer information om den här processen finns i avsnittet **alternativ B: Växla från federation till lösenordshashsynkronisering med Azure AD Connect och PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentet aktuella federationsinställningar

Du hittar de aktuella inställningarna för federation genom att köra den **Get-MsolDomainFederationSettings** cmdlet:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exempel:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Kontrollera alla inställningar som kan ha anpassats för dokumentationen för federation design och distribution. Mer specifikt söker för anpassningar i **PreferredAuthenticationProtocol**, **SupportsMfa**, och **PromptLoginBehavior**.

Mer information finns i dessa artiklar:

* [AD FS-prompten = parameterstöd för inloggning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Om **SupportsMfa** är inställd på **SANT**, du använder en lokal multifaktorautentisering lösning för att mata in en andra faktor-utmaning i autentiseringsflödet för användaren. Den här installationen inte längre fungerar för scenarier med Azure AD-autentisering när du har konverterat den här domänen från federerat till hanterad autentisering. När du har inaktiverat federation Server av relationen till din lokala federationsserver, vilket omfattar den lokala MFA-kort. 
>
> Använd i stället Azure Multi-Factor Authentication molnbaserad tjänst för att utföra samma funktion. Utvärdera din multifaktorautentiseringskrav noggrant innan du fortsätter. Innan du konverterar domäner, se till att du förstår hur du använder Azure Multi-Factor Authentication och licensiering konsekvenserna registreringsprocessen för användaren.

#### <a name="back-up-federation-settings"></a>Säkerhetskopiera federationsinställningar

Även om inga ändringar görs till andra förlitande parter i din AD FS-servergrupp under de processer som beskrivs i den här artikeln, rekommenderar vi att du har en aktuell giltig säkerhetskopia av AD FS-gruppen som du kan återställa från. Du kan skapa en aktuell giltig säkerhetskopia med hjälp av den kostnadsfria Microsoft [AD FS snabb återställa verktyget](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Du kan använda verktyget för att säkerhetskopiera AD FS och för att återställa en befintlig servergrupp eller skapa en ny servergrupp.

Om du väljer att inte använda AD FS snabb återställa verktyg, minst, ska du exportera Microsoft Office 365 Identity Platform förlitande parten och alla associerade anpassade anspråksregler som du har lagt till. Du kan exportera förtroende för förlitande part och associerade anspråksregler med hjälp av följande PowerShell-exempel:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Överväganden vid distribution och använder AD FS

Det här avsnittet beskrivs överväganden vid distribution och information om hur du använder AD FS.

### <a name="current-ad-fs-use"></a>Den aktuella AD FS använder

Innan du konverterar från federerad identitet till hanterad identitet, titta på hur du använder AD FS för Azure AD, Office 365 och andra program (förtroenden för förlitande part). Mer specifikt beskriver scenarier som beskrivs i följande tabell:

| Om | sedan |
|-|-|
| Du planerar att fortsätta att använda AD FS med andra program (än Azure AD och Office 365). | När du har konverterat domäner ska du använda både AD FS och Azure AD. Överväg att användarupplevelsen. I vissa situationer kan användare vara tvungna att autentiseras två gånger: en gång till Azure AD (där en användare får åtkomst med enkel inloggning till andra program som Office 365) och igen för alla program som fortfarande är bundna till AD FS som en förlitande part. |
| Din instans av AD FS är kraftigt anpassad och förlitar sig på specifika anpassningsinställningarna i onload.js-filen (till exempel om du har ändrat inloggningen så att användare bara använda en **SamAccountName** format för sina användarnamn i stället för en användare har huvudnamn (UPN) eller din organisation kraftigt märkesprodukter inloggningen går). Vara det går inte att dubbletter av onload.js-filen i Azure AD. | Innan du fortsätter måste du kontrollera att Azure AD kan uppfylla din aktuella anpassningskrav. Mer information och anvisningar finns i avsnitt om anpassning av AD FS och AD FS-anpassning.|
| Du kan använda AD FS för att blockera tidigare versioner av autentiseringsklienter.| Överväg att ersätta AD FS-kontroller som blockerar tidigare versioner av autentiseringsklienter med hjälp av en kombination av [villkorlig åtkomstkontroller](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) och [åtkomstregler för Exchange Online-klienten](https://aka.ms/EXOCAR). |
| Du behöver användare att utföra multifaktorautentisering mot en lokal Multi-Factor authentication server-lösning när användare autentiseras till AD FS.| I en hanterad identitet-domän, kan du mata in en utmaning för multifaktorautentisering via lokal multifaktorautentisering lösning i autentiseringsflödet. Du kan dock använda Azure Multi-Factor Authentication-tjänsten för multi-Factor authentication när domänen har konverterats.<br /><br /> Om användarna inte använder Azure Multi-Factor Authentication, krävs ett genomfört användaren registreringssteget. Du måste förbereda för och kommunicera planerad registreringen till dina användare. |
| Du använder principer för åtkomstkontroll (AuthZ-regler) i AD FS för att styra åtkomsten till Office 365.| Överväg att ersätta principerna med motsvarande Azure AD [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) och [åtkomstregler för Exchange Online-klienten](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Vanliga AD FS-anpassningar

Det här avsnittet beskrivs vanliga AD FS-anpassningar.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork anspråk

AD FS-problem i **InsideCorporateNetwork** anspråk om användaren som autentiseras är i företagsnätverket. Det här anspråket kan sedan skickas vidare till Azure AD. Anspråket används för att koppla förbi Multi-Factor authentication baserat på användarens nätverksplats. Läs hur du avgör om den här funktionen för närvarande är aktiverad i AD FS i [tillförlitliga IP-adresser för federerade användare](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

Den **InsideCorporateNetwork** anspråk är inte tillgängligt när dina domäner har konverterats till synkronisering av lösenordshash. Du kan använda [namngivna platser i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) att ersätta den här funktionen.

När du har konfigurerat namngivna platser måste du uppdatera alla principer för villkorlig åtkomst som är konfigurerade för antingen inkludera eller exkludera nätverket **alla betrodda platser** eller **MFA tillförlitliga IP-adresser** värden till återspegla den nya namngivna platser.

Mer information om den **plats** ange villkor för villkorlig åtkomst, se [Active Directory-platser för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

När du ansluter en enhet till Azure AD kan du skapa regler för villkorlig åtkomst som leder till att enheterna uppfyller dina access-standarder för säkerhet och efterlevnad. Dessutom kan användare logga in på en enhet med ett organisations arbets- eller skolkonto i stället för ett personligt konto. När du använder hybrid Azure AD-anslutna enheter kan du ansluta din Active Directory-domänanslutna enheter till Azure AD. Miljön federerad kanske har ställts in att använda den här funktionen.

För att säkerställa att hybrid anslutning fortsätter att fungera för alla enheter som är anslutna till domänen när dina domäner har konverterats till synkronisering av lösenordshash, för Windows 10-klienter, måste du använda Azure AD Connect för att synkronisera Active Directory-datorkonton till Azure AD. 

För Windows 8 och Windows 7-datorkonton använder hybrid anslutning sömlös enkel inloggning för att registrera datorn i Azure AD. Du behöver inte synkronisera Windows 8 och Windows 7-datorkonton som du gör med Windows 10-enheter. Dock måste du distribuera en uppdaterad workplacejoin.exe-fil (via en MSI-fil) för Windows 8 och Windows 7-klienter så att de kan registrera sig själva med sömlös enkel inloggning. [Ladda ned MSI-filen](https://www.microsoft.com/download/details.aspx?id=53554).

Mer information finns i [konfigurera hybrid Azure AD-anslutna enheter](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Anpassning

Om din organisation [anpassade din AD FS-inloggningssidor](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) för att visa information som är mer relevant för organisationen, Överväg att göra liknande [anpassningar till inloggningssidan för Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Även om liknande anpassningar är tillgängliga, väntat vissa visuella ändringar på inloggningssidorna efter konverteringen. Du kanske vill ange information om ändringarna i din kommunikation för användare.

> [!NOTE]
> Anpassning av organisation är tillgänglig endast om du köper Premium eller Basic-licens för Azure Active Directory eller om du har en Office 365-licens.

## <a name="plan-deployment-and-support"></a>Planera distribution och support

Utföra uppgifter som beskrivs i det här avsnittet hjälper dig att planera för distribution och support.

### <a name="plan-the-maintenance-window"></a>Planera underhållet

Även om domänen processen är relativt snabba, kan Azure AD fortsätta att skicka vissa autentiseringsbegäranden till AD FS-servrarna i upp till fyra timmar efter domän konverteringen har slutförts. Under den här fyra timmar och beroende på olika cacheminnen för tjänsten på klientsidan, kan Azure AD inte acceptera dessa autentiseringar. Användare kan få felmeddelanden. Användaren kan fortfarande har autentiseras mot AD FS, men Azure AD accepterar inte längre användarens utfärdade token eftersom det federationsförtroendet har nu tagits bort.

Endast användare som har åtkomst till tjänster via en webbläsare under det här fönstret efter konverteringen innan tjänsten på klientsidan cachen rensats påverkas. Äldre klienter (Exchange ActiveSync, Outlook 2010/2013) är inte förväntas påverkas eftersom Exchange Online från ett cacheminne med sina autentiseringsuppgifter för en angiven tidsperiod. Cachen används tyst autentiseras användaren. Användaren behöver inte gå tillbaka till AD FS. Autentiseringsuppgifter som lagras på enheten för dessa klienter används för att tyst återautentisera sig när det cachelagrade är avmarkerad. Användare är inte förväntas ta emot några frågor för lösenord på grund av domän-konverteringsprocessen. 

Modern autentiseringsklienter (Office 2016 och Office 2013, iOS och Android-appar) använder en giltig uppdateringstoken för att hämta nya åtkomsttoken för fortsatt åtkomst till resurser i stället för att återgå till AD FS. De här klienterna är immun till alla frågor för lösenord som härrör från domänen processen. Klienterna fortsätter att fungera utan ytterligare konfiguration.

> [!IMPORTANT]
> Inte stänga av din AD FS-miljö eller ta bort Office 365-förtroende för förlitande part förrän du har kontrollerat att alla användare kan autentisera med hjälp av molnautentisering.

### <a name="plan-for-rollback"></a>Planera för återställning

Om det uppstår ett större problem som du inte kan lösa snabbt, kan du välja att återställa lösningen till federation. Det är viktigt att planera vad du gör om distributionen inte lanseras som avsett. Om konverteringen i domänen eller en användare inte kan genomföras under distributionen, eller om du vill återställa till federation, måste du förstå hur att minimera eventuella driftstopp och minska inverkan på användarna.

#### <a name="to-roll-back"></a>Att återställa

Om du vill planera för återställning i dokumentationen federation design och distribution för din specifika distributionsinformation. Processen bör innehålla dessa uppgifter:

* Konvertera hanterade domäner till federerade domäner med hjälp av den **Convert-MSOLDomainToFederated** cmdlet.
* Om det behövs kan du konfigurera ytterligare anspråk regler.

### <a name="plan-communications"></a>Planera kommunikation

En viktig del av planeringen av distribution och support är att säkerställa att dina användare är proaktivt dig informerad om kommande ändringar. Användare bör i förväg veta vad det kan uppstå och vad som krävs av dem. 

När både lösenordshashsynkronisering och sömlös SSO distribueras är den användaren inloggningen för att komma åt Office 365 och andra resurser som autentiseras via Azure AD-ändringar. Användare som är utanför nätverket se bara Azure AD-inloggningssidan. Dessa användare omdirigeras inte till sidan formulärbaserad som presenteras av utåtriktade webbprogramproxyservrarna.

Inkludera följande element i din kommunikationsstrategi för:

* Meddela användare om kommande och utgivna funktioner med hjälp av:
   * E-post och andra interna kommunikationskanaler.
   * Visuella objekt, till exempel affischer.
   * Executive, live eller andra kommunikation.
* Bestämma vem som anpassar kommunikationen och som skickar meddelandet, och när.

## <a name="implement-your-solution"></a>Implementera din lösning

Du har planerat din lösning. Nu kan du nu kan du implementera den. Implementering omfattar följande komponenter:

* Aktiverar synkronisering av lösenordshash.
* Förbereder för sömlös enkel inloggning.
* Ändrar inloggningsmetoden till synkronisering av lösenordshash och Aktivera sömlös SSO.

### <a name="step-1-enable-password-hash-synchronization"></a>Steg 1: Aktivera synkronisering av lösenordshash

Det första steget för att implementera den här lösningen är att aktivera lösenordshashsynkronisering med hjälp av Azure AD Connect-guiden. Synkronisering av lösenordshash är en valfri funktion som du kan aktivera i miljöer som använder federation. Det finns ingen effekt på autentiseringsflödet. I så fall startar Azure AD Connect synkroniserar hashvärden för lösenord utan att påverka användare som loggar in med hjälp av federation.

Därför rekommenderar vi att du slutför det här steget som en förberedande aktivitet som är korrekt innan du ändrar inloggningsmetod för din domän. Sedan har du gott om tid för att kontrollera att synkronisering av lösenordshash fungerar korrekt.

Så här aktiverar synkronisering av lösenordshash:

1. Öppna Azure AD Connect-guiden på Azure AD Connect-servern och välj sedan **konfigurera**.
2. Välj **anpassa synkroniseringsalternativ**, och välj sedan **nästa**.
3. På den **Anslut till Azure AD** anger du användarnamnet och lösenordet för ett globalt administratörskonto.
4. På den **Anslut dina kataloger** väljer **nästa**.
5. På den **domän och Organisationsenhet filtrering** väljer **nästa**.
6. På den **valfria funktioner** väljer **Lösenordssynkronisering**, och välj sedan **nästa**.
 
   ![Skärmbild av alternativet lösenord synkronisering valde på sidan valfria funktioner](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Välj **nästa** på de återstående sidorna. På den sista sidan Välj **konfigurera**.
8. Azure AD Connect börjar synkronisera hashvärden för lösenord vid nästa synkronisering.

När synkronisering av lösenordshash har aktiverats, hashar lösenordet för alla användare i den Azure AD Connect synkronisering omfång rehashed och skrivs till Azure AD. Åtgärden kan ta några minuter eller flera timmar beroende på hur många användare.

För planering, bör du uppskattar att cirka 20 000 användare bearbetas i timmen.

Kontrollera att synkronisering av lösenordshash fungerar genom att slutföra den **felsökning** aktivitet i Azure AD Connect-guiden:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med alternativet Kör som administratör.
2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted`.
3. Starta Azure AD Connect-guiden.
4. Gå till den **ytterligare uppgifter** väljer **Felsök**, och välj sedan **nästa**.
5. På den **felsökning** väljer **starta** till startmenyn felsökning i PowerShell.
6. På huvudmenyn väljer **Felsök synkronisering av lösenordshash**.
7. Välj på undermenyn **lösenordshashsynkronisering inte fungerar alls**.

Felsökning av problem, finns i [felsöka lösenordshashsynkronisering med Azure AD Connect-synkronisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>Steg 2: Förbereda för sömlös SSO

För dina enheter att använda sömlös enkel inloggning, måste du lägga till en Azure AD-URL: en användares Zoninställningar för intranätet med hjälp av en Grupprincip i Active Directory.

Webbläsare beräkna automatiskt rätt zonen internet- eller intranätvärdar från en URL. Till exempel **http:\/\/contoso /** mappar till intranätzonen och **http:\/\/intranet.contoso.com** mappar till zonen internet eftersom ( URL: en innehåller en punkt). Webbläsare skickar Kerberos-biljetter till en slutpunkt i molnet, t.ex. Azure AD-URL, endast om du uttryckligen lägga till URL: en till webbläsarens intranätzonen.

Slutför steg för att [lansera](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) önskade ändringar till dina enheter.

> [!IMPORTANT]
> Den här ändringen ändrar inte det sätt som användarna måste logga in till Azure AD. Det är dock viktigt att du använder den här konfigurationen till alla dina enheter innan du fortsätter. Användare som loggar in på enheter som inte har fått den här konfigurationen krävs bara att ange ett användarnamn och lösenord för att logga in på Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Steg 3: Ändra inloggningsmetoden till synkronisering av lösenordshash och Aktivera sömlös enkel inloggning

Du har två alternativ för att ändra inloggningsmetoden till synkronisering av lösenordshash och Aktivera sömlös SSO.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Alternativ A: Växla från federation till lösenordshashsynkronisering med Azure AD Connect

Använd den här metoden om du har konfigurerat din AD FS-miljön med hjälp av Azure AD Connect. Du kan inte använda den här metoden om du *inte* ursprungligen konfigurera AD FS-miljön med hjälp av Azure AD Connect.

Börja med att ändra inloggningsmetoden:

1. Öppna Azure AD Connect-guiden på Azure AD Connect-servern.
2. Välj **ändra användarinloggning**, och välj sedan **nästa**. 

   ![Skärmbild av användaren logga in alternativet Ändra på sidan ytterligare uppgifter](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. På den **Anslut till Azure AD** anger du användarnamnet och lösenordet för ett globalt administratörskonto.
4. På den **användarinloggning** väljer den **lösenordshash synkroniseringsknappen**. Se till att välja den **konvertera inte användarkonton** markerar du kryssrutan. Alternativet är inaktuell. Välj **aktivera enkel inloggning**, och välj sedan **nästa**.

   ![Skärmbild av aktivera enkel inloggning på sidan](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Från och med Azure AD Connect version 1.1.880.0, den **sömlös enkel inloggning** kryssrutan är markerad som standard.

   > [!IMPORTANT]
   > Du kan ignorera varningar som indikerar den användarkonvertering och fullständig synkronisering av lösenordshash är obligatoriska steg för att konvertera från federation till autentisering i molnet. Observera att de här stegen inte behövs längre. Om du fortfarande ser dessa varningar, se till att du kör den senaste versionen av Azure AD Connect och som använder du den senaste versionen av den här guiden. Mer information finns i avsnittet [uppdatering Azure AD Connect](#update-azure-ad-connect).

5. På den **aktivera enkel inloggning** , ange autentiseringsuppgifter för administratörskontot för domänen och välj sedan **nästa**.

   ![Skärmbild av aktivera enkel inloggning på sidan](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Autentiseringsuppgifter för administratörskontot för domänen krävs för att aktivera sömlös enkel inloggning. Processen är klar följande åtgärder, som kräver dessa förhöjd behörighet. Inloggningsuppgifterna för domänadministratören konto lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifter för domänadministratör används endast för att aktivera funktionen. Autentiseringsuppgifterna tas bort när processen har slutförts.
   >
   > 1. Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory-instans.
   > 2. Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
   > 3. Två Kerberos tjänsternas huvudnamn (SPN) skapas för att representera två URL: er som används under Azure AD-inloggningen.

6. På den **redo att konfigurera** sidan och se till att den **starta synkroniseringsprocessen när konfigurationen är klar** kryssrutan är markerad. Välj **konfigurera**.

      ![Skärmbild av klart att konfigurera](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Nu ändras alla federerade domäner för hanterad autentisering. Synkronisering av lösenordshash är den nya metoden för autentisering.

7. I Azure AD-portalen väljer du **Azure Active Directory** > **Azure AD Connect**.
8. Kontrollera inställningarna:
   * **Federation** är inställd på **inaktiverad**.
   * **Sömlös enkel inloggning** är inställd på **aktiverad**.
   * **Lösenordssynkronisering** är inställd på **aktiverad**.<br /> 

   ![Skärmbild som visar inställningarna i avsnittet för användare](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Gå vidare till [testning och nästa steg](#testing-and-next-steps).

   > [!IMPORTANT]
   > Hoppa över avsnittet **alternativ B: Växla från federation till lösenordshashsynkronisering med Azure AD Connect och PowerShell**. Stegen i avsnittet gäller inte om du väljer alternativ A att ändra inloggningsmetoden till synkronisering av lösenordshash och Aktivera sömlös enkel inloggning.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Alternativ B: Växla från federation till lösenordshashsynkronisering med Azure AD Connect och PowerShell

Använd det här alternativet om du inte har konfigurerat ditt federerade domäner med hjälp av Azure AD Connect. Under den här processen kan aktivera du sömlös SSO och växel domäner från federerad som hanteras.

1. Öppna Azure AD Connect-guiden på Azure AD Connect-servern.
2. Välj **ändra användarinloggning**, och välj sedan **nästa**.
3. På den **Anslut till Azure AD** ange användarnamnet och lösenordet för ett globalt administratörskonto.
4. På den **användarinloggning** väljer den **lösenordshashsynkronisering** knappen. Välj **aktivera enkel inloggning**, och välj sedan **nästa**.

   Innan du aktiverar synkronisering av lösenordshash: ![Skärmbild som visar de inte att konfigurera alternativ på sidan för användare](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   När du aktiverar synkronisering av lösenordshash: ![Skärmbild som visar nya alternativ på sidan för användare](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Från och med Azure AD Connect version 1.1.880.0, den **sömlös enkel inloggning** kryssrutan är markerad som standard.

5. På den **aktivera enkel inloggning** , anger du autentiseringsuppgifterna för ett domänadministratörskonto och välj sedan **nästa**.

   > [!NOTE]
   > Autentiseringsuppgifter för administratörskontot för domänen krävs för att aktivera sömlös enkel inloggning. Processen är klar följande åtgärder, som kräver dessa förhöjd behörighet. Inloggningsuppgifterna för domänadministratören konto lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifter för domänadministratör används endast för att aktivera funktionen. Autentiseringsuppgifterna tas bort när processen har slutförts.
   >
   > 1. Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory-instans.
   > 2. Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
   > 3. Två Kerberos tjänsternas huvudnamn (SPN) skapas för att representera två URL: er som används under Azure AD-inloggningen.

6. På den **redo att konfigurera** sidan och se till att den **starta synkroniseringsprocessen när konfigurationen är klar** kryssrutan är markerad. Välj **konfigurera**.

   ![Skärmbild som visar knappen Konfigurera på klart att konfigurera](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   När du väljer den **konfigurera** knapp, sömlös enkel inloggning har konfigurerats som anges i föregående steg. Lösenordshash-synkroniseringskonfigurationen ändras inte eftersom det har aktiverats tidigare.

   > [!IMPORTANT]
   > Inga ändringar görs i hur användare logga in just nu.

7. Kontrollera inställningarna i Azure AD-portalen:
   * **Federation** är inställd på **aktiverad**.
   * **Sömlös enkel inloggning** är inställd på **aktiverad**.
   * **Lösenordssynkronisering** är inställd på **aktiverad**.

   ![Skärmbild som visar inställningarna i avsnittet för användare](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konvertera domäner från federerad som hanteras

Nu är federation fortfarande aktiverade och fungerar för dina domäner. Om du vill fortsätta med distributionen, måste varje domän som ska konverteras från federerat till hanteras för att tvinga användarautentisering via synkronisering av lösenordshash.

> [!IMPORTANT]
> Du behöver att konvertera alla domäner på samma gång. Du kan välja att starta med en testdomän i din produktionsklient eller börja med din domän som har det lägsta antalet användare.

Slutföra konverteringen med hjälp av Azure AD PowerShell-modulen:

1. I PowerShell inloggningen till Azure AD med hjälp av ett globalt administratörskonto.
2. Om du vill konvertera den första domänen, kör du följande kommando:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. I Azure AD-portalen väljer du **Azure Active Directory** > **Azure AD Connect**.
4. Kontrollera att domänen har konverterats till hanteras genom att köra följande kommando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testning och nästa steg

Utför följande uppgifter för att kontrollera lösenordshashsynkronisering och för att slutföra processen.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testa autentisering med hjälp av synkronisering av lösenordshash 

När din klient används för federerad identitet, omdirigerades användare från inloggningssidan för Azure AD till din AD FS-miljön. Nu när klienten har konfigurerats för synkronisering av lösenordshash istället för federerad autentisering omdirigeras inte användare till AD FS. Användare kan i stället logga in direkt på inloggningssidan för Azure AD.

Så här testar synkronisering av lösenordshash:

1. Öppna Internet Explorer i InPrivate-läge så att sömlös SSO inte logga in dig automatiskt.
2. Gå till inloggningssidan för Office 365 ([https://portal.office.com](https://portal.office.com/)).
3. Ange ett användar-UPN och välj sedan **nästa**. Se till att du anger UPN-namnet för en hybrid-användare som har synkroniserats från din lokala Active Directory-instans och som har använt federerad autentisering. Det visas en sida där du anger det användarnamn och lösenord:

   ![Skärmbild som visar inloggningssidan där du anger ett användarnamn](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Skärmbild som visar inloggningssidan där du anger ett lösenord](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. När du har angett lösenordet och välj **logga in**, du är omdirigeras till Office 365-portalen.

   ![Skärmbild som visar Office 365-portalen](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testa sömlös SSO

1. Logga in på en domänansluten dator som är ansluten till företagsnätverket.
2. I Internet Explorer eller Chrome, går du till någon av följande webbadresser (Ersätt ”contoso” med domänen):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Användaren omdirigeras kort till Azure AD-inloggningssidan, som visar meddelandet ”försöker att logga in”. Användaren är inte uppge ett användarnamn eller lösenord.<br />

   ![Skärmbild som visar inloggningssidan för Azure AD och meddelande](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. Användaren omdirigeras och loggat har in på åtkomstpanelen:

   > [!NOTE]
   > Sömlös SSO fungerar på Office 365-tjänster som stöder tips för domänen (till exempel myapps.microsoft.com/contoso.com). Office 365-portalen (portal.office.com) stöder för närvarande inte tips för domänen. Användare måste ange ett UPN. När ett UPN har angetts, hämtar sömlös SSO Kerberos-biljetten för användarens räkning. Användaren är inloggad utan att ange ett lösenord.

   > [!TIP]
   > Överväg att distribuera [hybrid Azure AD join i Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) för en bättre SSO-upplevelse.

### <a name="remove-the-relying-party-trust"></a>Ta bort det förlitande partsförtroendet

När du verifierar att alla användare och klienter har autentiseras via Azure AD, är det säkert att ta bort det förlitande partsförtroendet för Office 365.

Om du inte använder AD FS för andra ändamål (det vill säga för andra förtroenden för förlitande part), är det säkert att inaktivera AD FS i det här läget.

### <a name="rollback"></a>Återställning

Om du identifierar ett större problem och inte kan lösa det snabbt, kan du välja att återställa lösningen till federation.

I dokumentationen federation design och distribution för din specifika distributionsinformation. Processen ska omfatta dessa uppgifter:

* Konvertera hanterade domäner till federerad autentisering med hjälp av den **Convert-MSOLDomainToFederated** cmdlet.
* Om det behövs konfigurerar du ytterligare anspråksregler.

### <a name="sync-userprincipalname-updates"></a>Synkronisera userPrincipalName uppdateringar

Historiskt sett uppdaterar till den **UserPrincipalName** attribut, som använder synkroniseringstjänsten från den lokala miljön, blockeras såvida inte båda dessa villkor är uppfyllda:

* Användaren är i en hanterad identitet för (icke-federerade)-domän.
* Användaren har inte tilldelats en licens.

Läs hur du kontrollerar eller aktivera den här funktionen i [synkronisera userPrincipalName uppdateringar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Felsökning

Supportteamet bör förstå hur du kan felsöka eventuella problem med autentisering som kan uppstå under eller efter ändringen från federation som hanteras. Använd följande Felsökningsdokumentation för att supportteamet bekanta dig med vanliga åtgärder för felsökning och lämpliga åtgärder som kan hjälpa till att isolera och lösa problemet.

[Felsöka Azure Active Directory synkronisering av lösenordshash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Felsöka Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Förnya sömlös SSO Kerberos-krypteringsnyckel

Det är viktigt att ofta förnyar Kerberos-krypteringsnyckel för AZUREADSSOACC datorkontot (som representerar Azure AD). Datorkontot AZUREADSSOACC skapas i din lokala Active Directory-skog. Vi rekommenderar starkt att du förnyar Kerberos-krypteringsnyckel minst var 30 dagar så att den överensstämmer med det sättet att medlemmar i Active Directory-domänen skickar lösenordsändringar. Det finns inga associerade enheten ansluten till AZUREADSSOACC datorkontoobjektet, så du måste utföra uppdateringen manuellt.

Initiera förnyelse av krypteringsnyckel sömlös SSO Kerberos på den lokala servern som kör Azure AD Connect.

Mer information finns i [hur jag förnyar Kerberos-krypteringsnyckel för datorkontot AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [designbegrepp för Azure AD Connect](plan-connect-design-concepts.md).
* Välj den [rätt autentisering](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).
* Lär dig mer om [stöds topologier](plan-connect-design-concepts.md).
