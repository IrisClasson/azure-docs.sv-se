---
title: Anslut data källor till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter data källor som Microsoft Threat Protection, Microsoft 365 och Office 365, Azure AD, ATP och Cloud App Security till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: a2b9c1602ead56b35c46508ef4d414145eb07432
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555520"
---
# <a name="connect-data-sources"></a>Ansluta till datakällor

När du har aktiverat Azure Sentinel måste du först ansluta dina data källor. Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga direkt och ger real tids integrering, inklusive Microsoft Threat Protection-lösningar, Microsoft 365 källor (inklusive Office 365), Azure AD, Azure ATP, Microsoft Cloud App Security med mera. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda common Event format (CEF), syslog eller REST-API för att ansluta dina data källor med Azure Sentinel.

1. På menyn väljer du **data kopplingar**. På den här sidan kan du se en fullständig lista över de anslutningar som Azure Sentinel tillhandahåller och deras status. Välj den anslutning som du vill ansluta och välj **Öppna kopplings sida**. 

   ![Data insamlare](./media/collect-data/collect-data-page.png)

1. På sidan för den aktuella anslutningen ser du till att du har uppfyllt alla krav och följer anvisningarna för att ansluta data till Azure Sentinel. Det kan ta lite tid innan loggarna börjar synkronisera med Azure Sentinel. När du har anslutit visas en sammanfattning av data i grafen med **mottagna data** och anslutnings status för data typerna.

   ![Anslut insamlare](./media/collect-data/opened-connector-page.png)
  
1. Klicka på fliken **Nästa steg** om du vill hämta en lista över innehåll i Azure Sentinel som är färdiga för den aktuella data typen.

   ![Data insamlare](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Dataanslutningsmetoder

Följande data anslutnings metoder stöds av Azure Sentinel:

- **Integrering av tjänst till tjänst**:<br> Vissa tjänster är anslutna till varandra, till exempel AWS och Microsoft-tjänster, de här tjänsterna utnyttjar Azure Foundation för out of Box-integreringen. följande lösningar kan vara anslutna med några klick:
    - [Amazon Web Services-CloudTrail](connect-aws.md)
    - [Azure-aktivitet](connect-azure-activity.md)
    - [Azure Active Directory](connect-azure-active-directory.md) gransknings loggar och inloggnings loggar
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domännamnserver](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft-brandväggen för webbaserade program](connect-microsoft-waf.md)
    - [Windows-brandvägg](connect-windows-firewall.md)
    - [Windows säkerhetshändelser](connect-windows-security-events.md)

- **Externa lösningar via API**: vissa data källor är anslutna via API: er som tillhandahålls av den anslutna data källan. Oftast tillhandahåller de flesta säkerhets tekniker en uppsättning API: er via vilka händelse loggar som kan hämtas. API: erna ansluter till Azure Sentinel och samlar in vissa data typer och skickar dem till Azure Log Analytics. Enheter som är anslutna via API inkluderar:
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen-brandväggen](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Perimeter 81-loggar](connect-perimeter-81-logs.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externa lösningar via agent**: Azure Sentinel kan anslutas via en agent till en annan data källa som kan utföra logg strömning i real tid med syslog-protokollet.

    De flesta apparater använder syslog-protokollet för att skicka händelse meddelanden som innehåller själva loggen och data om loggen. Formatet på loggarna varierar, men de flesta apparater stöder CEF-baserad formatering för loggdata. 

    Azure Sentinel-agenten, som faktiskt är Log Analytics agent, konverterar CEF-formaterade loggar till ett format som kan matas in av Log Analytics. Beroende på typ av installation installeras agenten antingen direkt på enheten eller på en dedikerad Linux-baserad logg vidarebefordrare. Agenten för Linux tar emot händelser från syslog-daemon över UDP, men om en Linux-dator förväntas samla in en stor mängd Syslog-händelser skickas de via TCP från syslog-daemon till agenten och därifrån att Log Analytics.

    - **Brand väggar, proxyservrar och slut punkter:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint-produkter](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Andra CEF-enheter](connect-common-event-format.md)
        - [Andra syslog-enheter](connect-syslog.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP-lösningar
    - [Hot informations leverantörer](connect-threat-intelligence.md)
    - [DNS-datorer](connect-dns.md) – Agent installeras direkt på DNS-datorn
    - Linux-servrar
    - Andra moln
    
## <a name="agent-connection-options"></a>Alternativ för agentanslutning<a name="agent-options"></a>

Om du vill ansluta din externa installation till Azure Sentinel måste agenten distribueras på en dedikerad dator (VM eller lokalt) för att stödja kommunikationen mellan-enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är bara tillgängligt om din dedikerade dator är en ny virtuell dator som du skapar i Azure. 


![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator.

![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mappa datatyper med anslutningsalternativ för Azure Sentinel


| **Datatyp** | **Så här ansluter du** | **Data koppling?** | **Kommentarer** |
|------|---------|-------------|------|
| AWSCloudTrail | [Ansluta AWS](connect-aws.md) | &#10003; | |
| AzureActivity | Översikt över [Anslut Azure-aktivitet](connect-azure-activity.md) och [aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Ansluta till Azure Active Directory](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Ansluta till Azure Active Directory](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure Information Protection rapporter](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Ansluta till Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Detta använder vanligt vis **InformationProtectionEvents** -funktionen tillsammans med data typen. Mer information finns i [ändra rapporterna och skapa anpassade frågor](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | Trafik [analys schema](../network-watcher/traffic-analytics.md) [Traffic analytics](../network-watcher/traffic-analytics.md) analys  | | |
| CommonSecurityLog  | [Anslut CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Ansluta Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Ansluta till Windows säkerhetshändelser](connect-windows-security-events.md)  | &#10003; | Arbets böckerna för oskyddade protokoll finns i [arbets boks konfiguration för oskyddade protokoll](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks)  |
| Syslog | [Ansluta till Syslog](connect-syslog.md) | &#10003; | |
| Microsoft-brand vägg för webbaserade program (WAF) – (AzureDiagnostics) |[Anslut brand väggen för Microsoft-webbprogram](connect-microsoft-waf.md) | &#10003; | |
| SymantecICDx_CL | [Anslut Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Ansluta till hotinformation](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor tjänst karta](../azure-monitor/insights/service-map.md)<br>[Azure Monitor VM Insights onboarding](../azure-monitor/insights/vminsights-onboard.md) <br> [Aktivera Azure Monitor VM-insikter](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Använda en enda virtuell dator](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Använda fordonsbaserad via princip](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| &#10007; | Arbets bok för VM Insights  |
| DnsEvents | [Anslut DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Anslut IIS-loggar](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Anslut tråd data](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Anslut Windows-brandväggen](connect-windows-firewall.md) | &#10003; | |
| AADIP SecurityAlert  | [Ansluta Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Ansluta till Azure ATP](connect-azure-atp.md) | &#10003; | |
| ASC-SecurityAlert  | [Ansluta till Azure Security Center](connect-azure-security-center.md)  | &#10003; | |
| MCAS SecurityAlert  | [Anslut Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (händelse) | [Anslut Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Anslut Windows-händelser](../azure-monitor/platform/data-sources-windows-events.md) <br> [Hämta Sysmon-parsern](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Sysmon-samlingen installeras inte som standard på virtuella datorer. Mer information om hur du installerar Sysmon-agenten finns i [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatisera VM-inventering](../automation/automation-vm-inventory.md)| &#10007; | |
| ConfigurationChange  | [Automatisera spårning av virtuella datorer](../automation/change-tracking.md) | &#10007; | |
| F5 BIG-IP | [Ansluta F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Ansluta till Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Nästa steg

- För att komma igång med Azure Sentinel behöver du en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).
- Lär dig hur du kan [publicera dina data till Azure Sentinel](quickstart-onboard.md)och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
