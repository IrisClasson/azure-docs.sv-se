---
title: Metod tips för säkra PaaS-distributioner – Microsoft Azure
description: Lär dig metod tips för att utforma, skapa och hantera säkra moln program på Azure och förstå säkerhets fördelarna med PaaS jämfört med andra moln tjänst modeller.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 9adbe7b03283a00f78222ffdc77dca7aaadcbda0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81461709"
---
# <a name="securing-paas-deployments"></a>Skydda PaaS-distributioner

Den här artikeln innehåller information som hjälper dig att:

- Förstå säkerhets fördelarna med värdbaserade program i molnet
- Utvärdera säkerhets fördelarna med PaaS (Platform as a Service) jämfört med andra moln tjänst modeller
- Ändra säkerhets fokus från ett nätverk som är inriktad på en identitets inriktad perimeter säkerhets metod
- Implementera rekommendationer för allmänna PaaS-säkerhet

Att [utveckla säkra program på Azure](abstract-develop-secure-apps.md) är en allmän guide för de säkerhets frågor och kontroller som du bör tänka på i varje fas i livs cykeln för program utveckling när du utvecklar program för molnet.

## <a name="cloud-security-advantages"></a>Fördelar med moln säkerhet
Det är viktigt att förstå [delningen av ansvaret](shared-responsibility.md) mellan dig och Microsoft. Lokalt, du äger hela stacken, men när du flyttar till molnet kan du överföra vissa ansvars områden till Microsoft.

Det finns [säkerhets för delar i molnet](shared-responsibility.md#cloud-security-advantages). I en lokal miljö har organisationer troligen ouppfyllda ansvar och begränsade resurser som är tillgängliga för att investera i säkerhet, vilket skapar en miljö där angripare kan utnyttja sårbarheter på alla nivåer.

Organisationer kan förbättra sina hot identifierings-och svars tider genom att använda en providers molnbaserade säkerhetsfunktioner och Cloud Intelligence.  Genom att ändra ansvars områden till moln leverantören kan organisationer få mer säkerhets täckning, vilket gör att de kan allokera om säkerhets resurser och budget till andra affärs prioriteringar.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Säkerhets fördelarna med en PaaS Cloud Service-modell
Nu ska vi ta en titt på säkerhets fördelarna med en Azure PaaS-distribution kontra lokalt.

![Säkerhets fördelarna med PaaS](./media/paas-deployments/advantages-of-paas.png)

Med början längst ned i stacken, den fysiska infrastrukturen, minimeras vanliga risker och ansvars områden. Eftersom Microsoft Cloud ständigt övervakas av Microsoft är det svårt att angripa. Det är inte meningsfullt för en angripare att driva Microsoft-molnet som mål. Om angriparen har mycket pengar och resurser kommer angriparen förmodligen att gå vidare till ett annat mål.  

I mitten av stacken finns det ingen skillnad mellan en PaaS-distribution och lokalt. På program lagret och konto-och åtkomst hanterings lagret har du liknande risker. I nästa steg i den här artikeln får du hjälp med bästa praxis för att eliminera eller minimera riskerna.

Överst i stacken, data styrning och Rights Management kan du utföra en risk som kan begränsas av nyckel hantering. (Nyckel hantering beskrivs i bästa praxis.) Även om nyckel hantering är ett ytterligare ansvar har du områden i en PaaS-distribution som du inte längre behöver hantera så att du kan byta resurser till nyckel hantering.

Azure-plattformen ger dig också starkt DDoS skydd genom att använda olika nätverksbaserade tekniker. Alla typer av nätverksbaserade DDoS-skydds metoder har dock deras gränser för per länk och per Data Center. För att undvika påverkan av stora DDoS-attacker kan du dra nytta av Azures kärn kapacitet i molnet så att du snabbt och automatiskt kan skala ut för att försvara mot DDoS-attacker. Vi ska gå vidare till mer information om hur du kan göra detta i de rekommenderade praxis artiklarna.

## <a name="modernizing-the-defenders-mindset"></a>Att modernisera Defender-tänkesätt
Med PaaS-distributioner kan du växla över din övergripande metod till säkerhet. Du behöver inte styra allt du behöver för att kunna dela ansvaret med Microsoft.

En annan viktig skillnad mellan PaaS och traditionella lokala distributioner är en ny vy av vad som definierar den primära säkerhetsperimetern. Tidigare var den primära lokala säkerhetsperimetern nätverket och de flesta lokala säkerhets modeller använder nätverket som sin primära säkerhetspivotering. För PaaS-distributioner betjänas du bättre genom att ta hänsyn till att identiteten är den primära säkerhets omkretsen.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Anta en identitets princip som primär säkerhetsperimeter
En av de fem grundläggande egenskaperna för molnbaserad data behandling är bred nätverks åtkomst, vilket gör det lättare att tänka på nätverket. Målet för mycket av molnbaserad data behandling är att ge användare åtkomst till resurser oavsett plats. För de flesta användare kommer deras plats att finnas någonstans på Internet.

Följande bild visar hur säkerhetsperimetern har utvecklats från en nätverks perimeter till en identitets perimeter. Säkerheten blir mindre säker på skyddet av ditt nätverk och mer om att skydda dina data, samt att hantera säkerheten för dina appar och användare. Den viktigaste skillnaden är att du vill push-skydda det som är viktigt för ditt företag.

![Identitet som ny säkerhets perimeter](./media/paas-deployments/identity-perimeter.png)

Azure PaaS Services (till exempel Web Roles och Azure SQL) tillhandahöll en inlednings vis mindre eller inga traditionella försvar för nätverks omkrets. Det förstod att elementets syfte var att exponeras för Internet (webb roll) och att autentiseringen ger den nya omkretsen (till exempel BLOB eller Azure SQL).

Modern säkerhets praxis förutsätter att angripare har brutit mot nätverks omkretsen. Därför har modern försvars praxis flyttats till identiteten. Organisationer måste upprätta en Identity-baserad säkerhetsperimeter med stark autentisering och behörighets hygien (bästa praxis).

Principer och mönster för nätverks perimetern har varit tillgängliga i årtionden. Branschen har däremot relativt mindre erfarenhet av att använda identitet som primär säkerhets perimeter. Med detta har vi samlat tillräckligt med erfarenhet för att tillhandahålla några allmänna rekommendationer som är beprövade i fältet och som gäller för nästan alla PaaS-tjänster.

Följande är metod tips för att hantera identitets omkretsen.

**Bästa praxis**: skydda dina nycklar och autentiseringsuppgifter för att skydda din PaaS-distribution.   
**Information**: att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. Du kan använda en centraliserad lösning där nycklar och hemligheter kan lagras i HSM: er (Hardware Security modules). [Azure Key Vault](../../key-vault/general/overview.md) skyddar dina nycklar och hemligheter genom att kryptera autentiseringsnyckel, lagrings konto nycklar, data krypterings nycklar, PFX-filer och lösen ord med hjälp av nycklar som skyddas av HSM: er.

**Bästa praxis**: Ange inte autentiseringsuppgifter och andra hemligheter i käll kod eller GitHub.   
**Information**: det enda som är sämre än att förlora dina nycklar och autentiseringsuppgifter är att en obehörig part får till gång till dem. Angripare kan dra nytta av bot-teknikerna för att hitta nycklar och hemligheter som lagras i kod databaser, till exempel GitHub. Lägg inte till nyckel och hemligheter i dessa offentliga kod databaser.

**Bästa praxis**: skydda dina hanterings gränssnitt för virtuella datorer på Hybrid PaaS-och IaaS-tjänster med hjälp av ett hanterings gränssnitt som gör att du kan fjärrhantera de virtuella datorerna direkt.   
**Information**: fjärrhanterings protokoll som [SSH](https://en.wikipedia.org/wiki/Secure_Shell)-, [RDP](https://support.microsoft.com/kb/186607)-och [PowerShell-fjärrkommunikation](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) kan användas. I allmänhet rekommenderar vi att du inte aktiverar direkt fjärråtkomst till virtuella datorer från Internet.

Använd om möjligt alternativa metoder som att använda virtuella privata nätverk i ett virtuellt Azure-nätverk. Om alternativa metoder inte är tillgängliga kontrollerar du att du använder komplexa lösen fraser och tvåfaktorautentisering (till exempel [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)).

**Bästa praxis**: Använd kraftfulla plattformar för autentisering och auktorisering.   
**Information**: Använd federerade identiteter i Azure AD i stället för anpassade användar lager. När du använder federerade identiteter, drar du nytta av en plattforms beroende metod och du delegerar hanteringen av auktoriserade identiteter till dina partner. En federerad identitets metod är särskilt viktig när de anställda avslutas och informationen måste avspeglas genom flera identitets-och auktoriserings system.

Använd de mekanismer för autentisering och auktorisering som tillhandahålls av plattformen i stället för anpassad kod. Orsaken är att utveckla anpassad kod kan vara fel känsligt. De flesta av dina utvecklare är inte säkerhets experter och är inte medvetna om nyanser och den senaste utvecklingen av autentisering och auktorisering. Kommersiell kod (till exempel från Microsoft) är ofta en omfattande säkerhets granskning.

Använd tvåfaktorautentisering. Tvåfaktorautentisering är den aktuella standarden för autentisering och auktorisering eftersom den undviker säkerhets svagheter som finns i användar namn och lösen ords typer för autentisering. Åtkomst till både Azure-hanterings gränssnitten (portal/fjärr-PowerShell) och kundinriktade tjänster bör utformas och konfigureras för användning av [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication).

Använd standard protokoll för autentisering, till exempel OAuth2 och Kerberos. Dessa protokoll har bearbetats i stor utsträckning och implementeras förmodligen som en del av plattforms biblioteken för autentisering och auktorisering.

## <a name="use-threat-modeling-during-application-design"></a>Använda hot modellering under program design
[Livs cykeln för Microsoft Security Development](https://www.microsoft.com/en-us/sdl) anger att team ska delta i en process som kallas Threat Modeling under Design fasen. För att hjälpa till att förenkla den här processen har Microsoft skapat [sdl-Threat Modeling Tool](/azure/security/azure-security-threat-modeling-tool). Att modellera program designen och räkna upp [kliv](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) -hot i alla förtroende gränser kan fånga design fel tidigt på.

I följande tabell visas kliv-hoten och innehåller några exempel på åtgärder som använder Azure-funktioner. Dessa åtgärder fungerar inte i varje situation.

| Hot | Säkerhets egenskap | Potentiella Azure-plattforms begränsningar |
| --- | --- | --- |
| Förfalskning | Autentisering | Kräv HTTPS-anslutningar. |
| Manipulation | Integritet | Verifiera TLS/SSL-certifikat. |
| Avvislighet | Oavvislig het | Aktivera [övervakning och diagnostik](/azure/architecture/best-practices/monitoring)i Azure. |
| Avslöjande av information | Konfidentialitet | Kryptera känsliga data i vila med hjälp av [tjänst certifikat](/rest/api/appservice/certificates). |
| Denial of Service (nekad tjänst) | Tillgänglighet | Övervaka prestanda mått för potentiella denial-of-service-villkor. Implementera anslutnings filter. |
| Behörighets höjning | Auktorisering | Använd [Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Utveckla på Azure App Service
[Azure App Service](/azure/app-service/overview) är ett PaaS-erbjudande som gör att du kan skapa webb-och mobilappar för alla plattformar och enheter och ansluta till data var som helst, i molnet eller lokalt. App Service innehåller de webb-och mobil funktioner som tidigare levererades separat som Azure Websites och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. Som en enda integrerad tjänst ger App Service en omfattande uppsättning funktioner för webb-, mobil-och integrations scenarier.

Nedan följer metod tips för hur du använder App Service.

**Bästa praxis**: [autentisera via Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Information**: App Service tillhandahåller en OAuth 2,0-tjänst för din identitetsprovider. OAuth 2,0 fokuserar på Client Developer enkelhet samtidigt som du tillhandahåller vissa auktoriseringsarkiv för webb program, skriv bords program och mobil telefoner. Azure AD använder OAuth 2,0 för att ge åtkomst till mobil-och webb program.

**Bästa praxis**: begränsa åtkomsten baserat på behovet av att känna till och minsta behörighets säkerhets principer.   
**Information**: att begränsa åtkomsten är absolut nödvändig för organisationer som vill tillämpa säkerhets principer för data åtkomst. Du kan använda RBAC för att tilldela behörigheter till användare, grupper och program i ett visst omfång. Mer information om hur du beviljar användare åtkomst till program finns i [Kom igång med åtkomst hantering](/azure/role-based-access-control/overview).

**Bästa praxis**: skydda dina nycklar.   
**Information**: Azure Key Vault hjälper till att skydda kryptografiska nycklar och hemligheter som används av moln program och tjänster. Med Key Vault kan du kryptera nycklar och hemligheter (till exempel autentiseringsnyckel, lagrings konto nycklar, data krypterings nycklar). PFX-filer och lösen ord) med hjälp av nycklar som skyddas av HSM: er (Hardware Security modules). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Mer information finns i [Azure Key Vault](/azure/key-vault/key-vault-overview) . Du kan också använda Key Vault för att hantera dina TLS-certifikat med automatisk förnyelse.

**Bästa praxis**: begränsa inkommande käll-IP-adresser.   
**Information**: [App Service-miljön](/azure/app-service/environment/intro) har en funktion för integrering av virtuella nätverk som hjälper dig att begränsa inkommande käll-IP-adresser via nätverks säkerhets grupper. Med virtuella nätverk kan du placera Azure-resurser i ett nätverk som inte är Internet och som du styr åtkomsten till. Mer information finns i [integrera din app med ett virtuellt Azure-nätverk](/azure/app-service/web-sites-integrate-with-vnet).

**Bästa praxis**: övervaka säkerhets läget för dina app Services miljöer.   
**Information**: Använd Azure Security Center för att övervaka App Service miljöer. När Security Center identifierar potentiella säkerhets problem skapas [rekommendationer](../../security-center/security-center-virtual-machine-protection.md) som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna.

> [!NOTE]
> Övervaknings App Service är i för hands version och endast tillgängligt på [Standard nivån](/azure/security-center/security-center-pricing) för Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Installera en brand vägg för webbaserade program
Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

[Brand vägg för webbaserade program (WAF)](/azure/frontdoor/waf-overview) är en funktion i Application Gateway som tillhandahåller centraliserat skydd av dina webb program från vanliga sårbarheter och sårbarheter. WAF baseras på regler från [OWASP (Open Web Application Security Project () Core Rule set](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 eller 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Övervaka program prestanda
Övervakning handlar om att samla in och analysera data för att fastställa programmets prestanda, hälsa och tillgänglighet. En effektiv övervakningsstrategi hjälper dig att förstå den detaljerade driften av komponenterna i ditt program. Det hjälper dig att öka din drift tid genom att meddela dig om kritiska problem så att du kan lösa dem innan de blir problem. Det hjälper dig också att identifiera avvikelser som kan vara säkerhetsrelaterade.

Använd [Azure Application insikter](https://azure.microsoft.com/documentation/services/application-insights) för att övervaka tillgänglighet, prestanda och användning av ditt program, oavsett om det finns i molnet eller lokalt. Genom att använda Application Insights kan du snabbt identifiera och diagnostisera fel i programmet utan att vänta på att användaren rapporterar dem. Du kan göra välgrundade val om underhåll och förbättringar för ditt program med den information som du samlar in.

Application Insights har omfattande verktyg för att interagera med de data som samlas in. Application Insights lagrar data i en gemensam databas. Det kan dra nytta av delade funktioner som aviseringar, instrument paneler och djup analys med Kusto-frågespråket.

## <a name="perform-security-penetration-testing"></a>Utför testning av säkerhets inträngning
Att verifiera säkerhets försvar är lika viktigt som att testa alla andra funktioner. Gör [inträngande och testa](pen-testing.md) en standard del av bygg-och distributions processen. Schemalägga vanliga säkerhetstester och sårbarhets sökning på distribuerade program och övervaka för öppna portar, slut punkter och attacker.

Fuzz-test är en metod för att hitta program fel (kod fel) genom att tillhandahålla felformaterade indata till program gränssnitt (start punkter) som tolkar och använder dessa data. [Microsofts säkerhets risk identifiering](https://www.microsoft.com/en-us/security-risk-detection/) är ett molnbaserad verktyg som du kan använda för att söka efter buggar och andra säkerhets problem i program varan innan du distribuerar det till Azure. Verktyget är utformat för att fånga sårbarheter innan du distribuerar program vara så att du inte behöver korrigera en bugg, hantera krascher eller svara på ett angrepp när program varan har släppts.


## <a name="next-steps"></a>Nästa steg
I den här artikeln fokuserar vi på säkerhets fördelarna med en Azure PaaS-distribution och rekommenderade säkerhets metoder för moln program. Nu ska du läsa rekommendationer för att skydda dina PaaS webb-och mobil lösningar med hjälp av vissa Azure-tjänster. Vi börjar med Azure App Service, Azure SQL Database och Azure SQL Data Warehouse och Azure Storage. När artiklar om rekommenderade metoder för andra Azure-tjänster blir tillgängliga kommer länkar att tillhandahållas i följande lista:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database och Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Brand väggar för webb program

Se [utveckla säkra program på Azure](abstract-develop-secure-apps.md) för säkerhets frågor och kontroller som du bör tänka på i varje fas i livs cykeln för program utveckling när du utvecklar program för molnet.

Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure Security Team-bloggen](https://blogs.msdn.microsoft.com/azuresecurity/) – för uppdaterad information om det senaste i Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där säkerhets problem i Microsoft, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com
