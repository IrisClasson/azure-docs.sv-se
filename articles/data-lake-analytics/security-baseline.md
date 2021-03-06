---
title: Azures säkerhets bas linje för Data Lake Analytics
description: Data Lake Analytics säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 33084d72e26f2926751def686c0a4c1d0485a5c2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005128"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Azures säkerhets bas linje för Data Lake Analytics

Azures säkerhets bas linje för Data Lake Analytics innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Använd brand Väggs inställningar för data Lake Analytics för att begränsa externa IP-adressintervall för att tillåta åtkomst från dina lokala klienter och tjänster från tredje part. Konfiguration av brand Väggs inställningar är tillgängligt via portalen, REST API: er eller PowerShell.

* [Brand Väggs regler](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: ej tillämpligt; Azure Data Lake Analytics körs inte i ett virtuellt nätverk och när du använder federerade frågor kan utgående samtal inte konfigureras att dirigera via ett virtuellt kund nätverk.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; Den här kontrollen är avsedd för webb program som körs på Azure App Service-eller IaaS-instanser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd brand Väggs inställningar för data Lake Analytics för att begränsa externa IP-adressintervall för att tillåta åtkomst från dina lokala klienter och tjänster från tredje part. Konfiguration av brand Väggs inställningar är tillgängligt via portalen, REST API: er eller PowerShell.

* [Brand Väggs regler](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: ej tillämpligt; Data Lake Analytics körs inte i kundens virtuella nätverk och kan inte använda nätverks säkerhets grupper (NSG: er) för att registrera nätverks flödes loggar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: ej tillämpligt; Data Lake Analytics är ett PaaS-erbjudande som inte distribueras till kund nätverk.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; Den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: ej tillämpligt; Data Lake Analytics körs inte i kundens virtuella nätverk och kan inte använda nätverks säkerhets grupper (NSG: er).

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: ej tillämpligt; Data Lake Analytics körs inte i kundens virtuella nätverk och kan inte använda nätverks säkerhets grupper (NSG: er).

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: ej tillämpligt; Data Lake Analytics körs inte i kundens virtuella nätverk och kan inte använda nätverks säkerhets grupper (NSG: er).

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: ej tillämpligt; Data Lake Analytics körs inte i kundens virtuella nätverk och kan inte använda nätverks säkerhets grupper (NSG: er).

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: ej tillämpligt; Microsoft underhåller tids källan för Data Lake Analytics.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som data Lake Analytics gransknings-och begär ande-diagnostik. I Azure Monitor använder du en Log Analytics arbets yta för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring eller lagring, om du vill ha säkerhetsfunktioner som oföränderlig lagring och tvingande kvarhållning.

Du kan också aktivera och inaktivera data till Azure Sentinel eller en SIEM från tredje part.

* [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Samla in plattforms loggar och mått med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för data Lake Analytics för att få åtkomst till gransknings-och förfrågnings loggar. Detta inkluderar data som händelse källa, datum, användare, tidsstämpel och andra användbara element.

* [Samla in plattforms loggar och mått med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Förstå loggning och olika logg typer i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger Log Analytics du arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig lagring och arkivering.

* [Ändra data lagrings perioden i Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Konfigurera bevarande princip för Azure Storage konto loggar](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet för dina data Lake Analytics-resurser. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Mer information om arbets ytan Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Så här utför du anpassade frågor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Aktivera diagnostikinställningar för data Lake Analytics och skicka loggar till en Log Analytics arbets yta. Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem.

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Så här aviserar du om Log Analytics-loggdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Data Lake Analytics bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part från Azure Marketplace för DNS-loggning av lösningar enligt organisationens behov.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser där kunden har åtkomst till det underliggande operativ systemet.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure AD har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: data Lake Analytics saknar begreppet standard lösen ord som autentiseringen tillhandahålls med Azure Active Directory och skyddas av rollbaserad åtkomst kontroll i Azure (Azure RBAC).

* [Översikt över Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton.

Du kan också aktivera en just-in-Time-åtkomst genom att använda Azure AD Privileged Identity Management och Azure Resource Manager.

* [Läs mer om Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: där det är möjligt använder Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center identitets-och åtkomst rekommendationer.

* [Förstå SSO med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory Multi-Factor Authentication (MFA) och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering som hjälper dig att skydda dina data Lake Analytics resurser.

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Övervaka identitet och åtkomst i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station eller Paw) för administrativa uppgifter som kräver förhöjda privilegier.

* [Förstå säkra, Azure-hanterade arbets stationer](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-managed-workstation)

* [Så här aktiverar du Azure AD MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

* [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd namngivna platser i Azure AD för att endast tillåta åtkomst från särskilda logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du Azure AD-namngivna platser](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD tillhandahåller rollbaserad åtkomst kontroll (RBAC) för detaljerad kontroll över en klients åtkomst till Data Lake Analytics resurser.

* [Så här skapar och konfigurerar du en Azure AD-instans](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure AD-identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

* [Förstå Azure AD repor ting](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Använda Azure AD-identitets-och åtkomst granskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Aktivera diagnostikinställningar för Data Lake Analytics och Azure Active Directory och skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel försök att komma åt inaktiverade hemligheter) i Log Analytics.

* [Integrera Azure AD-loggar med Azure Monitor loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directorys risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör dina data Lake Analytics resurser. Du bör aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

* [Visa Azure AD-riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ej tillämpligt; Customer Lockbox stöds inte för Azure Data Lake Analytics.

* [Tjänster och scenarier som stöds i allmän tillgänglighet](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra data Lake Analytics resurser som lagrar eller bearbetar känslig information.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer, hanterings grupper för enskilda säkerhets domäner, till exempel miljö, data känslighet. Du kan begränsa din Data Lake Analytics för att kontrol lera åtkomst nivån till dina Data Lake Analytics resurser som dina program och företags miljöer kräver. När brand Väggs regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt dina Data Lake Analytics-resurser. Du kan styra åtkomsten till Azure Data Lake Analytics via Azure AD RBAC.

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Hantera rollbaserade Access Control](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

* [Brand Väggs regler](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: funktioner för data förlust skydd är ännu inte tillgängliga för Azure Data Lake Analytics-resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Skydda Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Microsoft Azure resurser förhandlar TLS 1,2 som standard. Se till att alla klienter som ansluter till Data Lake Analytics kan förhandla med TLS 1,2 eller senare.

* [Exempel på åtgärds lista](https://docs.microsoft.com/rest/api/datalakeanalytics/operations/list)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering är ännu inte tillgängliga för Azure Data Lake Analytics-resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll (RBAC) för att styra hur användare interagerar med tjänsten.

* [Hantera rollbaserade Access Control](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: data lagras på standard data Lake Storage Gen1s kontot. För data i vila stöder Data Lake Storage Gen1 "på som standard" transparent kryptering.

* [Kryptering av data i Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Data Lake Analytics resurser.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om hur du skyddar dina Azure Data Lake Analytics-resurser.

Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Data Lake Analytics.

* [Förstå Azure Security Center rekommendationer](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av ditt genomsöknings verktyg från tredje part.

* [NIST-publicering – vanliga sårbarhets bedömnings system](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph Explorer, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

* [Så här skapar du frågor med Azure Resource Graph Explorer](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Förstå Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra Azure Data Lake Analytics resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du frågor med Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till ARM](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DataLakeAnalytics" för att skapa anpassade principer som ska granskas eller tillämpas på konfigurationen av din Azure Data Lake Analytics. Du kan också använda inbyggda princip definitioner som är relaterade till din Azure Data Lake Analytics, till exempel:
- Diagnostikloggar i Data Lake Analytics ska vara aktive rad

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Förstå Azure Policys effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-databaser för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar, önskade tillstånds konfigurations skript osv. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

* [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Om behörigheter och grupper i Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DataLakeAnalytics" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om den inte finns] för att automatiskt genomdriva konfigurationer för dina Azure Data Lake Analytics-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: ej tillämpligt; Data Lake Analytics visar inte några hemligheter som kunden behöver hantera.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: ej tillämpligt; Data Lake Analytics stöder inte Azure Managed Identities.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Data Lake Analytics), men det körs inte på kund innehåll.

Förskanna allt innehåll som överförs till Azure-resurser, till exempel App Service, Data Lake Analytics, Blob Storage osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här kontrollen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: data Lake Analytics jobb loggar och data utdata lagras i den underliggande data Lake Storage Gen1s tjänsten. Du kan använda olika metoder för att kopiera data, inklusive ADLCopy, Azure PowerShell eller Azure Data Factory. Du kan också använda Azure Automation för att säkerhetskopiera data regelbundet med jämna mellanrum.

* [Hantera Azure Data Lake Storage Gen1 resurser med Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Kopiera data från Azure Storage blobbar till Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

* [Översikt över Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: data Lake Analytics jobb loggar och data utdata lagras i den underliggande data Lake Storage Gen1s tjänsten. Du kan använda olika metoder för att kopiera data, inklusive ADLCopy, Azure PowerShell eller Azure Data Factory.

* [Hantera Azure Data Lake Storage Gen1 resurser med Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Kopiera data från Azure Storage blobbar till Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: regelbundet utföra Data återställning av dina säkerhets kopierings data för att testa data integriteten.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: data Lake Analytics säkerhets kopior som lagras i Data Lake Storage Gen1 eller Azure Storage stöder kryptering som standard och inte kan stängas av. Du bör behandla dina säkerhets kopior som känsliga data och tillämpa relevanta åtkomst-och data skydds kontroller som en del av den här bas linjen.

* [Skydda data som lagras i Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data)

* [Auktorisera åtkomst till data i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

* [Säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Använd taggar till att organisera dina Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov.

* [NISTs publikation – guide för att testa, träna och träna program för IT-planer och-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

* [Så här ställer du in Azure Security Center säkerhets kontakt](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

* [Så här konfigurerar du kontinuerlig export](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Strömma aviseringar till Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

* [Konfigurera automatisering av arbets flöden och Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

* [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](/azure/security/benchmarks/security-baselines-overview)
