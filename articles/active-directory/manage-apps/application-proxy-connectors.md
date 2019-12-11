---
title: Förstå Azure AD Application Proxy-anslutningar | Microsoft Docs
description: Beskriver grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2036bf9995725e4bbef44e4c039f8336eb81a0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997045"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Förstå Azure AD Application Proxy-anslutningar

Kopplingar är vad gör att Azure AD Application Proxy är möjligt. De är enkla, enkla att distribuera och underhålla och super kraftfulla. Den här artikeln beskrivs vilka anslutningar som är, hur de fungerar och några förslag att optimera din distribution.

## <a name="what-is-an-application-proxy-connector"></a>Vad är ett programproxy-kopplingen?

Kopplingar är förenklad agenter som finns lokalt och underlätta den utgående anslutningen till Application Proxy-tjänsten. Kopplingar måste installeras på en Windows-Server som har åtkomst till backend-applikationer. Du kan ordna kopplingar i anslutningsapp-grupper, med varje grupp som hanterar trafik till specifika program.

## <a name="requirements-and-deployment"></a>Krav och distribution

Om du vill distribuera Application Proxy har du behöver minst en koppling, men vi rekommenderar två eller fler för större flexibilitet. Installera anslutningen på en dator som kör Windows Server 2012 R2 eller senare. Anslutningen måste kommunicera med Application Proxy-tjänsten och de lokala program som du publicerar.

### <a name="windows-server"></a>Windows-server
Du behöver en server som kör Windows Server 2012 R2 eller senare som du kan installera anslutningsappen för programproxyn. Servern måste ansluta till Application Proxy-tjänsterna i Azure och de lokala program som du publicerar.

Windows server måste ha aktiverats innan du installerar Application Proxy connector som TLS 1.2. Så här aktiverar du TLS 1,2 på servern:

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern

Mer information om nätverkskrav connector-server finns i [Kom igång med Application Proxy och installera ett anslutningsprogram](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Underhåll

Kopplingar och tjänsten tar hand om alla aktiviteter för hög tillgänglighet. De kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran kommer dirigeras till en av de kopplingar som finns för närvarande. Om en anslutning inte är tillgänglig, kan de inte svara på den här trafiken.

Anslutningarna är tillståndslösa och har inga configuration-data på datorn. De enda data som lagras är inställningarna för att ansluta tjänsten och dess certifikat för serverautentisering. När de ansluter till tjänsten kan de hämta alla nödvändiga konfigurationsdata och uppdatera den varje par minuter.

Kopplingar avsöka också servern för att ta reda på om det finns en nyare version av anslutningen. Om en sådan finns, uppdateras kopplingarna.

Du kan övervaka dina anslutningar från den datorn som de körs på, använder du händelseloggen och prestandaräknare. Eller så kan du visa deras status från sidan Application Proxy på Azure-portalen:

![Exempel: Azure AD-programproxy-kopplingar](./media/application-proxy-connectors/app-proxy-connectors.png)

Du har inte manuellt ta bort kopplingar som inte används. När en koppling är igång, förblir den aktiv när den ansluter till tjänsten. Oanvända anslutningar är märkta som _inaktiva_ och tas bort efter 10 dagar av inaktivitet. Om du vill avinstallera en anslutning, men avinstallera både anslutnings- och uppdateringstjänsten från servern. Ta bort tjänsten helt och hållet genom att starta om datorn.

## <a name="automatic-updates"></a>Automatiska uppdateringar

Azure AD tillhandahåller automatiska uppdateringar för alla kopplingar som du distribuerar. Så länge Application Proxy Connector Updater-tjänsten körs, uppdatera dina anslutningsappar automatiskt. Om du inte ser Connector Updater-tjänsten på servern, måste du [installera om din anslutningsapp](application-proxy-add-on-premises-application.md) att hämta uppdateringar.

Om du inte vill vänta på att en automatisk uppdatering ska komma till din anslutning kan du göra en manuell uppgradering. Gå till den [connector hämtningssidan](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) på servern där din anslutningsapp är placerad och välj **hämta**. Den här processen startar en uppgradering för den lokala anslutningen.

För klienter med flera anslutningar kan rikta automatiska uppdateringar en anslutning i taget i varje grupp för att förhindra avbrott i din miljö.

Avbrott kan uppstå när anslutningsappens uppdaterar om:
  
- Du har bara en koppling vi rekommenderar att du installerar en andra koppling och [skapar en kopplings grupp](application-proxy-connector-groups.md). På så sätt undviker du avbrott och ger högre tillgänglighet.  
- En anslutning har mitt i en transaktion när uppdateringen började. Även om den första transaktionen tappas bort, webbläsaren bör automatiskt att försöka igen eller du kan uppdatera din sida. När begäran igen, dirigeras trafiken till en säkerhetskopiering koppling.

Information om tidigare utgivna versioner och vilka ändringar de innehåller finns i [Application Proxy-versions historik](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Skapa anslutningsapp-grupper

Anslutningsapp-grupper kan du tilldela specifika anslutningsappar för att hantera specifika program. Du kan gruppera flera kopplingar och sedan tilldela varje program till en grupp.

Anslutningsappgrupper gör det enklare att hantera stora distributioner. De kan även förbättra svarstiden för klienter som har program som körs i olika regioner, eftersom du kan skapa platsbaserad anslutningsapp-grupper så att den stöder endast lokala program.

Läs mer om anslutningsapp-grupper i [publicera program på separata nätverk och platser med hjälp av anslutningsappgrupper](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitetsplanering

Det är viktigt att se till att du har planerat tillräckligt med kapacitet mellan kopplingarna för att hantera den förväntade trafik volymen. Vi rekommenderar att varje kopplings grupp har minst två kopplingar för att ge hög tillgänglighet och skalning. Att ha tre anslutningar är optimalt om du kan behöva underhålla en dator när som helst.

I allmänhet är ju fler användare som du har, desto större dator behöver du. Nedan visas en tabell som ger en översikt av volymen och förväntad fördröjning som olika datorer kan hantera. Observera att det är baserat på förväntade transaktioner per sekund (TPS) i stället för användare sedan användnings mönster varierar och inte kan användas för att förutse belastningen. Det finns också vissa skillnader beroende på storleken på svaren och Server del programmets svars tid – större svars storlekar och långsamma svars tider resulterar i en lägre Max TPS. Vi rekommenderar också att du har ytterligare datorer så att den distribuerade belastningen på datorerna alltid ger en gott buffert. Den extra kapaciteten ser till att du har hög tillgänglighet och återhämtnings kapacitet.

|Kärnor|RAM|Förväntat svarstid (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* den här datorn använde en anpassad inställning för att öka några av standard anslutnings gränserna utöver de rekommenderade .NET-inställningarna. Vi rekommenderar att köra ett test med standardinställningarna innan du kontaktar supporten för att få den här gränsen som har ändrats för din klient.

> [!NOTE]
> Det finns inte mycket skillnaden i maximala TPS mellan 4 och 8 datorer med 16 kärnor. Den största skillnaden mellan de som finns i den förväntade svarstiden.
>
> Den här tabellen fokuserar också på förväntade prestanda för en anslutning baserat på vilken typ av dator den är installerad på. Detta är skilt från Application Proxy-tjänstens begränsnings gränser, se begränsningar [och begränsningar för tjänsten](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Säkerhet och nätverk

Kopplingar kan installeras var som helst i nätverket som gör att de kan skicka begäranden till Application Proxy-tjänsten. Det är viktiga att den dator som kör anslutningstjänsten även har åtkomst till dina appar. Du kan installera kopplingar i företagets nätverk eller på en virtuell dator som körs i molnet. Kopplingar kan köras i ett perimeternätverk, även kallat demilitariserad Zone (DMZ), men det är inte nödvändigt eftersom all trafik är utgående så att nätverket förblir säkert.

Kopplingar kan bara skicka utgående begäranden. Den utgående trafiken skickas till Application Proxy-tjänsten och till publicerade program. Du behöver att öppna ingående portar eftersom trafiken flödar båda riktningarna när en session har upprättats. Du behöver inte heller konfigurera inkommande åtkomst genom brand väggarna.

Läs mer om hur du konfigurerar utgående brandväggsregler [fungerar med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Prestanda och skalbarhet

Skala för Application Proxy-tjänsten är transparent, men skalning är en faktor för kopplingar. Du måste ha tillräckligt med anslutningsappar för att hantera högtrafik. Eftersom kopplingar är tillstånds lösa, påverkas de inte av antalet användare eller sessioner. I stället svarar de på antalet begäranden och deras nyttolast. Med standard webbtrafik kan en genomsnittlig virtuell dator hantera några tusen begäranden per sekund. Den aktuella kapaciteten är beroende av de exakta dator-egenskaperna.

Prestanda för kopplingen är bunden av processor- och nätverk. CPU-prestanda krävs för SSL-kryptering och dekryptering, även om nätverk är viktigt att få snabb anslutning till program och online-tjänsten i Azure.

Minnet är däremot mindre av ett problem för kopplingar. Online-tjänsten hand tar om mycket av bearbetning och alla icke-autentiserade trafik. Allt som kan göras i molnet görs i molnet.

Om du av någon anledning att anslutningen eller dator blir otillgänglig, trafiken börjar gå till en annan koppling i gruppen. Den här återhämtning är också varför vi rekommenderar att du har flera anslutningar.

En annan faktor som påverkar prestanda är kvaliteten på nätverk mellan kopplingar, inklusive:

- **Onlinetjänsten**: långsamma eller lång svarstid anslutningar till Application Proxy-tjänsten i Azure möjlighet att påverka prestanda för kopplingen. Ansluta din organisation till Azure med Express Route för bästa prestanda. I annat fall har ditt nätverksteam att säkerställa att anslutningar till Azure hanteras så effektivt som möjligt.
- **Serverdelsprogrammen**: I vissa fall kan det finns ytterligare Proxys mellan anslutningsappen och backend-program som kan långsam eller förhindra anslutningar. Om du vill felsöka det här scenariot, öppna en webbläsare från connector-server och försök att komma åt programmet. Om du kör anslutningarna i Azure, men program som finns på plats, kanske inte upplevelsen användarna förväntar sig.
- **Domän kontrol Lanterna**: om anslutningarna utför enkel inloggning (SSO) med Kerberos-begränsad delegering, kontaktar de domän kontrol Lanterna innan begäran skickas till Server delen. Anslutningarna har ett cacheminne med Kerberos-biljetter, men i en miljö med upptagen svarstiden för domänkontrollanterna kan påverka prestanda. Det här problemet är vanligare för kopplingar som körs i Azure men kommunicerar med domänkontrollanter som finns på plats.

Läs mer om hur du optimerar ditt nätverk, [Network topologiöverväganden när du använder Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Domänanslutning

Kopplingar kan köras på en dator som inte är anslutna till en domän. Om du vill ha enkel inloggning (SSO) till program som använder integrerad Windows autentisering (IWA) måste dock en domänansluten dator. I det här fallet connector-datorer måste vara ansluten till en domän som kan utföra [Kerberos](https://web.mit.edu/kerberos) begränsad delegering för användare av de publicerade program.

Kopplingar kan också anslutas till domäner eller skogar som har ett partiellt förtroende eller till skrivskyddade domänkontrollanter.

## <a name="connector-deployments-on-hardened-environments"></a>Distribution av anslutningen på strikt miljöer

Vanligtvis connector distribution är enkelt och kräver ingen särskild konfiguration. Det finns dock vissa unika villkor som du bör överväga:

- Organisationer som begränsar den utgående trafiken måste [öppna portar som krävs](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- FIPS-kompatibla datorer kan krävas för att ändra konfigurationen för att tillåta connector-processer för att generera och lagra ett certifikat.
- Organisationer som låsa sin miljö, baserat på de processer som utfärdar de nätverk som har att se till att båda kopplingstjänsterna är aktiverade för åtkomst till alla nödvändiga portar och IP-adresser.
- I vissa fall kan utgående vidarebefordra proxyservrar bryta dubbelriktat certifikatautentisering och orsaka kommunikationen misslyckas.

## <a name="connector-authentication"></a>Connector-autentisering

Om du vill tillhandahålla en säker tjänst kopplingar att autentisera mot tjänsten och tjänsten att autentisera mot anslutningen. Den här autentiseringen görs med hjälp av klient- och servercertifikat när anslutningarna att upprätta anslutningen. Det här sättet administratörens användarnamn och lösenord inte lagras på connector-datorn.

De certifikat som används är specifika för Application Proxy-tjänsten. De skapas vid den första registreringen och automatiskt av anslutningarna förnyas varje för några månader.

Om en anslutning inte är ansluten till tjänsten för flera månader kan vara dess certifikat inaktuell. I det här fallet, avinstallera och installera om anslutningen så att registrering av utlösare. Du kan köra följande PowerShell-kommandon:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Under huven

Kopplingar är baserade på Windows Server Web Application Proxy, så att de har de flesta av samma hanteringsverktyg, inklusive Windows-händelseloggar

![Hantera händelseloggar med Loggboken](./media/application-proxy-connectors/event-view-window.png)

och Windows-prestandaräknare.

![Lägga till räknare i anslutningen med Resursövervakaren](./media/application-proxy-connectors/performance-monitor.png)

Anslutningarna har både admin och sessionen loggar. Administratörsloggar innehåller viktiga händelser och deras fel. Sessionsloggar innehåller alla transaktioner och bearbetningsinformation om.

Om du vill visa loggarna går du till Loggboken, öppna den **visa** menyn och aktivera **visa analytiska loggar och felsökningsloggar**. Aktivera sedan de kan börja samla in händelser. De här loggarna visas inte i Webbprogramproxy i Windows Server 2012 R2, som anslutningarna baseras på en senare version.

Du kan undersöka statusen för tjänsten i fönstret tjänster. Anslutningen består av två Windows-tjänster: den faktiska anslutningen och uppdateraren. Båda måste köras hela tiden.

 ![Exempel: fönstret tjänster visar lokala Azure AD-tjänster](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Nästa steg

- [Publicera program på separata nätverk och platser med hjälp av anslutningsapp-grupper](application-proxy-connector-groups.md)
- [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
- [Felsöka Application Proxy och anslutning](application-proxy-troubleshoot.md)
- [Tyst installation av Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)
