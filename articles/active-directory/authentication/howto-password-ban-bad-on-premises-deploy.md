---
title: Distribuera Azure AD-lösenord protection preview
description: Distribuera Azure AD lösenord protection förhandsgranskningen om du vill förbjuda felaktiga lösenord på plats
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: efa684d75cd30dcbfc971d0ef0a3717cc15bd0e4
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081287"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Förhandsversion: Distribuera Azure AD-lösenordsskydd

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Nu när vi har en förståelse för [för tvingande Azure AD-lösenordsskydd för Windows Server Active Directory](concept-password-ban-bad-on-premises.md), nästa steg är att planera och köra distributionen.

## <a name="deployment-strategy"></a>Distributionsstrategi

Föreslår vi att alla distributioner startar i granskningsläge. Granskningsläget är den första standardinställningen där lösenord kan fortsätta att ställas in och något som skulle blockeras skapa poster i händelseloggen. När proxyservrar och DC agenter distribueras helt i granskningsläge, regular övervakning ska göras för att avgöra vilken inverkan lösenordsprincip tvingande skulle ha på användare och miljön om principen har tvingats fram.

Under fasen audit hitta många organisationer:

* De behöver för att förbättra befintliga operativa processer för att använda fler säkra lösenord.
* Användarna är vana vid att regelbundet välja oskyddat lösenord
* De behöver för att informera användarna om den kommande ändringen i security tvingande, hur det kan ha på dem och hjälpa dem att bättre förstå hur de kan välja säkrare lösenord.

När funktionen har körts i granskningsläge i rimlig tid, tvingande konfiguration kan bläddra från **granska** till **tvinga** vilket kräver mer säkra lösenord. Fokuserade övervakning under denna tid är en bra idé.

## <a name="deployment-requirements"></a>Krav för distribution

* Alla domänkontrollanter där Azure AD lösenord protection DC agent-tjänsten kommer att installeras måste köra Windows Server 2012 eller senare.

   > [!NOTE]
   > Server Core-baserade operativsystem stöds inte för närvarande. Det här stödet är planerat för GA.

* Alla datorer där Azure AD-lösenordsskydd proxytjänsten installeras måste köra Windows Server 2012 R2 eller senare.

   > [!NOTE]
   > Server Core-baserade operativsystem stöds inte för närvarande. Det här stödet är planerat för GA.

* Alla datorer där Azure AD lösenord protection komponenter är installerade, inklusive domänkontrollanter måste ha den universella C som är installerad.
Detta åstadkoms helst genom att helt åtgärda datorn via Windows Update. I annat fall ett lämpligt operativsystemspecifika uppdateringspaketet vara installerat – Se [uppdatera för Universal C-körning i Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Nätverksanslutning måste det finnas mellan minst en domänkontrollant i varje domän och minst en server som är värd för Azure AD lösenord protection proxy-tjänsten. Den här anslutningen måste tillåta domänkontrollanten för att få åtkomst till RPC-mapper slutpunktsport (135) och RPC-server-porten i proxy-tjänsten.  RPC-serverport är som standard en dynamisk RPC-port, men kan konfigureras (se nedan) för att använda en statisk port.
* Alla datorer som är värd för tjänsten Azure AD lösenord protection proxy måste ha nätverksåtkomst till följande slutpunkter:

    |Slutpunkt |Syfte|
    | --- | --- |
    |`https://login.microsoftonline.com`|Begäranden om autentisering|
    |`https://enterpriseregistration.windows.net`|Skyddsfunktioner för Azure AD-lösenord|

* Ett globalt administratörskonto för att registrera Azure AD-lösenord protection proxy-tjänsten och skog med Azure AD.
* Ett konto med administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen för att registrera Windows Server Active Directory-skog med Azure AD.
* Alla Active Directory-domän som kör domänkontrollanten agentprogramvaran för tjänsten måste använda DFSR för sysvol-replikering.

## <a name="single-forest-deployment"></a>Enkel skog distribution

Förhandsversionen av Azure AD-lösenordsskydd distribueras med proxytjänsten på upp till två servrar och DC-agenttjänsten stegvis kan distribueras till alla domänkontrollanter i Active Directory-skogen.

![Hur Azure AD-lösenord protection komponenter fungerar tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Ladda ned programvaran

Det finns två nödvändiga installationsprogram för Azure AD-lösenordsskydd som kan laddas ned från den [Microsoft download center](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installera och konfigurera Azure AD lösenord protection proxy-tjänsten

1. Välj en eller flera servrar som värd för Azure AD lösenord protection proxy-tjänsten.
   * Varje sådan tjänst kan endast ange lösenordsprinciper för en enda skog och värddatorn måste vara domänansluten till en domän (rot- och underordnade både lika stöds) i den skogen. För Azure AD lösenord protection proxy-tjänsten att utföra sitt uppdrag, måste det finnas nätverksanslutning mellan minst en Domänkontrollant i varje domän i skogen och värddatorn för Azure AD lösenord protection Proxy.
   * Det finns stöd för att installera och köra Azure AD lösenord protection proxy-tjänsten på en domänkontrollant för att testa syften men domänkontrollanten och kräver en Internetanslutning.

   > [!NOTE]
   > Den offentliga förhandsversionen stöder högst två (2) proxy-servrar per skog.

2. Installera programvara lösenord Policy Proxy-tjänsten med hjälp av AzureADPasswordProtectionProxy.msi MSI-paketet.
   * Installationen av programmet kräver inte en omstart. Programinstallationen kan automatiseras med hjälp av MSI standardprocedurerna, till exempel: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

      > [!NOTE]
      > Tjänsten Windows-brandväggen måste köras innan du installerar AzureADPasswordProtectionProxy.msi MSI-paketet. Annars uppstår ett installationsfel. Om Windows-brandväggen är konfigurerad för att inte köra kan är lösningen att tillfälligt aktivera och starta Windows-brandväggen under installationen. Proxy-programvara har inga särskilda beroenden för Windows-brandväggen programvaran efter installationen. Om du använder en brandvägg från tredje part kan den fortfarande konfigureras för att uppfylla kraven för en distribution (Tillåt inkommande åtkomst till port 135 och proxy RPC-serverport om dynamisk eller statisk). [Se distributionskrav](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Öppna ett PowerShell-fönster som administratör.
   * Azure AD-lösenordsskydd proxyprogrammet innehåller en ny PowerShell-modulen med namnet AzureADPasswordProtection. Följande steg är baserade på kör olika cmdletar från PowerShell-modulen och förutsätter att du har öppnat ett nytt PowerShell-fönster och att du har importerat den nya modulen enligt följande:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Installationen av programvaran ändrar den värddatorn PSModulePath-miljövariabeln. Du kan behöva öppna en helt ny PowerShell-konsolfönster för den här ändringen ska börja gälla så att powershell-modulen AzureADPasswordProtection kan importeras och användas.

   * Kontrollera att tjänsten körs med hjälp av följande PowerShell-kommando: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Resultatet bör producera ett resultat med den **Status** ”körs” resultatet returneras.

4. Registrera proxyn.
   * När steg 3 har slutförts på Azure AD lösenord protection proxy-tjänsten körs på datorn, men ännu har inte de behörighet som krävs för att kommunicera med Azure AD. Registrering med Azure AD som krävs för att aktivera den möjligheten med hjälp av den `Register-AzureADPasswordProtectionProxy` PowerShell-cmdlet. Cmdlet: en kräver global administratör autentiseringsuppgifter för din Azure-klient som en lokal administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen. När den har slutförts för en viss proxy-tjänst, ytterligare funktionsanrop i `Register-AzureADPasswordProtectionProxy` fortsätta att lyckas men är onödiga.
      * Cmdlet: en kan köras på följande sätt:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         Exemplet fungerar bara om den inloggade användaren också är administratör för Active Directory-domän för rotdomänen. Ett alternativ är att ange autentiseringsuppgifter för nödvändiga domänen via den `-ForestCredential` parametern.

   > [!NOTE]
   > Den här åtgärden kan misslyckas om Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera IESC, registrera proxy, sedan återaktivera IESC.

   > [!NOTE]
   > Registrering av Azure AD lösenord protection proxy-tjänsten förväntas vara ett enstaka steg av tjänsten. Proxy-tjänsten utför automatiskt andra nödvändiga maintainenance från och med nu och senare. När den har slutförts för en viss skog, ytterligare anrop för ”registrera AzureADPasswordProtectionProxy' fortsätta att lyckas men är onödiga.

   > [!TIP]
   > Det kan finnas en avsevärd fördröjning (antal sekunder) första gången den här cmdleten körs för en viss Azure-klient innan cmdleten är slutfört. Om inte ett fel rapporteras ska inte den här fördröjningen betraktas oroväckande.

5. Registrera skogen.
   * Den lokala Active Directory-skogen måste initieras med autentiseringsuppgifterna som krävs för att kommunicera med Azure med hjälp av den `Register-AzureADPasswordProtectionForest` Powershell-cmdlet. Cmdlet: en kräver global administratör autentiseringsuppgifter för din Azure-klient som en lokal administratörsbehörighet för Active Directory-domänen i rotdomänen i skogen. Det här steget körs en gång per skog.
      * Cmdlet: en kan köras på följande sätt:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         Exemplet fungerar bara om den inloggade användaren också är administratör för Active Directory-domän för rotdomänen. Ett alternativ är att ange autentiseringsuppgifter för nödvändiga domänen via parametern - ForestCredential.

         > [!NOTE]
         > Den här åtgärden kan misslyckas om Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera IESC, registrera proxy, sedan återaktivera IESC.

         > [!NOTE]
         > Om flera proxyservrar är installerade i din miljö, spelar ingen roll vilken proxyserver har angetts i proceduren ovan.

         > [!TIP]
         > Det kan finnas en avsevärd fördröjning (antal sekunder) första gången den här cmdleten körs för en viss Azure-klient innan cmdleten är slutfört. Om inte ett fel rapporteras ska inte den här fördröjningen betraktas oroväckande.

   > [!NOTE]
   > Registrering av Active Directory-skogen förväntas vara ett enstaka steg i livslängden för skogen. Domain controller-agenter som körs i skogen utför automatiskt andra nödvändiga maintainenance från och med nu och senare. När den har slutförts för en viss skog ytterligare funktionsanrop i `Register-AzureADPasswordProtectionForest` fortsätta att lyckas men är onödiga.

   > [!NOTE]
   > För att `Register-AzureADPasswordProtectionForest` ska lyckas minst en Windows Server 2012 eller senare domain controller måste finnas i proxyserverns domän. Det finns dock inget krav på att DC klientprogrammet installeras på alla domänkontrollanter före det här steget.

6. Valfritt: Konfigurera Azure AD lösenord protection proxy-tjänsten för att lyssna på en specifik port.
   * RPC via TCP används av Azure AD-lösenordsskydd DC-agentprogramvaran på domänkontrollanterna för att kommunicera med Azure AD lösenord protection proxy-tjänsten. Azure AD-lösenordsskydd lösenord princip Proxy-tjänsten lyssnar på alla tillgängliga dynamiska RPC-slutpunkt som standard. Om det behövs på grund av nätverkets topologi eller krav på Brandvägg för konfigureras tjänsten i stället för att lyssna på en specifik TCP-port.
      * För att konfigurera tjänsten körs under en statisk port, använda den `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

      * För att konfigurera tjänsten körs under en dynamisk port, Använd samma procedur men nollställs StaticPort, t.ex:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Du måste stoppa och starta om tjänsten för att ändringarna ska börja gälla.

   > [!NOTE]
   > Azure AD lösenord protection proxy-tjänsten kräver en manuell omstart efter varje ändring i portkonfiguration. Du behöver inte starta om den DC service-agentprogramvaran som körs på domänkontrollanter när du har gjort ändringar i konfigurationen av den här typen.

   * Den aktuella konfigurationen av tjänsten kan efterfrågas med hjälp av den `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet enligt följande exempel:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installera Azure AD lösenord protection DC-Agenttjänst

* Installera Azure AD lösenord protection DC agenten service programvara med hjälp av den `AzureADPasswordProtectionDCAgent.msi` MSI-paketet:
   * Programvaruinstallationen kräver en omstart vid installation och avinstallera på grund av kravet på operativsystem att lösenord filter DLL: er endast läsa in eller tas bort från minnet vid en omstart.
   * Det går för att installera tjänsten DC-agent på en dator som inte ännu är en domänkontrollant. I det här fallet tjänsten startar och kör men ska annars inaktiveras tills när datorn är tillfrågas för att vara en domänkontrollant.

   Programinstallationen kan automatiseras med hjälp av MSI standardprocedurerna, till exempel:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Detta kommando msiexec leder startas om direkt; Detta kan undvikas genom att ange den `/norestart` flaggan.

När installerats på en domänkontrollant och startas om, har Azure AD-lösenordsskydd DC agenten installationen slutförts. Ingen konfiguration är obligatorisk eller möjligt.

## <a name="multiple-forest-deployments"></a>Flera skogar distributioner

Det finns inga ytterligare krav för att distribuera Azure AD-lösenordsskydd i flera skogar. Varje skog konfigureras oberoende enligt beskrivningen i avsnittet skog distribution. Varje Azure AD-lösenordsskydd Proxy har endast stöd för domänkontrollanter i skogen som den är ansluten till. Azure AD lösenord protection-programmet i en viss skog är inte medveten om av Azure AD lösenord protection program som distribueras i en annan skog oavsett förtroendekonfigurationer i Active Directory.

## <a name="read-only-domain-controllers"></a>Skrivskyddade domänkontrollanter

Lösenord changes\sets aldrig bearbetas och sparas på skrivskyddade domänkontrollanter (RODC); i stället vidarebefordras de till skrivbara domänkontrollanter. Det finns därför behöver inte installera DC-agentprogramvaran på skrivskyddade domänkontrollanter.

## <a name="high-availability"></a>Hög tillgänglighet

Det största problemet för att säkerställa hög tillgänglighet för Azure AD-lösenordsskydd är tillgängligheten för proxy-servrar när domänkontrollanter i en skog försöker hämta nya principer eller andra data från Azure. Den offentliga förhandsversionen stöder högst två proxyservrar per skog. Varje DC-agenten använder en algoritm för enkelt resursallokering format när du bestämmer vilken proxyserver för att anropa och hoppar över över proxyservrar som inte svarar.
Vanliga problem med hög tillgänglighet begränsas av utformningen av DC-agentprogramvaran. DC-agentens har ett lokalt cacheminne med den nyligen hämtade lösenordsprincipen. DC-agenter fortsätter att genomdriva sin cachelagrade lösenordsprincip även om alla registrerade proxy-servrar som blir otillgänglig av någon anledning. En rimlig uppdateringsfrekvensen för lösenordsprinciper i en stor distribution är vanligtvis på för dagar, inte timmar eller mindre.   Därför orsakar kort avbrott av proxy-servrar inte betydande påverkan på driften av Azure AD-lösenord skyddsfunktionen eller dess säkerhetsfördelarna.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som krävs för Azure AD-lösenordsskydd på dina lokala servrar kan du slutföra de [efter installera konfigurations- och samla rapportinformation](howto-password-ban-bad-on-premises-operations.md) att slutföra distributionen.

[Översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md)
