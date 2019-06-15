---
title: Samla in loggdata med Azure Log Analytics-agenten | Microsoft Docs
description: Det här avsnittet hjälper dig att samla in data och övervaka datorer i Azure, lokala eller andra moln med Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: magoedte
ms.openlocfilehash: 436685f3bba58ed7d06dfe834d808e7fe422176b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751977"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Samla in loggdata med Azure Log Analytics-agenten

Azure Log Analytics-agenten, tidigare kallade Microsoft Monitoring Agent (MMA) eller OMS Linux-agenten har utvecklats specifikt för omfattande hantering mellan lokala datorer, datorer som övervakas av [System Center Operations Manager ](https://docs.microsoft.com/system-center/scom/), och virtuella datorer i ett moln. Windows och Linux-agenter koppla till en Azure Monitor och lagra insamlade loggdata från olika källor i Log Analytics-arbetsytan, samt alla unika loggar eller mått som definieras i en lösning för övervakning. 

Den här artikeln innehåller en detaljerad översikt över agenten, system och nätverkskrav och olika distributionsmetoder.   

## <a name="overview"></a>Översikt

![Log Analytics-agenten kommunikation diagram](./media/log-analytics-agent/log-analytics-agent-01.png)

Innan du analysera och agera på insamlade data, måste du först installera och ansluta agenter för alla datorer som du vill skicka data till Azure Monitor-tjänsten. Du kan installera agenter på virtuella datorer i Azure med hjälp av Azure Log Analytics VM-tillägg för Windows och Linux, och för datorer i en hybridmiljö med hjälp av installationsprogrammet, kommandorad, eller med Desired State Configuration (DSC) i Azure Automation. 

Agenten för Linux och Windows kommunicerar utgående trafik till Azure Monitor-tjänsten via TCP-port 443 och om datorn ansluter via en brandvägg eller proxy server kommunicerar via Internet, granska kraven nedan för att förstå nätverkskonfigurationen krävs. Om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket att ansluta till Internet, du kan ställa in en [Log Analytics gateway](gateway.md) och sedan konfigurera agenten för att ansluta via gatewayen till Azure Monitor-loggar. Agenten kan sedan ta emot konfigurationsinformation och skicka data som samlas in beroende på vilka data samlingsregler och övervakningslösningar som du har aktiverat i din arbetsyta. 

Om du övervakar en dator med System Center Operations Manager 2012 R2 eller senare, kan det vara multihomed med Azure Monitor-tjänsten för att samla in data och vidarebefordra till tjänsten och fortfarande att övervakas av [Operations Manager](../../azure-monitor/platform/om-agents.md). Med Linux-datorer med inte agenten tjänstkomponenten hälsotillstånd som Windows-agenten, och information som samlas in och bearbetas av en hanteringsserver för dess räkning. Eftersom Linux-datorer finns på olika sätt övervakas med Operations Manager, de inte ta emot configuration eller samla in data direkt och vidarebefordra via hanteringsgruppen som har ett Windows-agenthanterade system. Därför kan stöds det här scenariot inte med Linux-datorer som rapporterar till Operations Manager.  

Windows-agent kan rapportera upp till fyra Log Analytics-arbetsytor, medan Linux-agenten stöder endast rapporterar till en enda arbetsyta.  

Agenten för Linux och Windows är inte bara för att ansluta till Azure Monitor, Azure Automation för att vara värd för Hybrid Runbook worker-roll och andra tjänster som stöder också [ändringsspårning](../../automation/change-tracking.md) och [uppdateringshantering](../../automation/automation-update-management.md). Mer information om Hybrid Runbook Worker-rollen finns i [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Windows-operativsystem som stöds
Följande versioner av Windows-operativsystemet stöds officiellt för Windows-agenten:

* Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, version 1709 och 1803
* Windows 7 SP1 och senare

## <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds
Det här avsnittet innehåller information om Linux-distributioner som stöds.    

Från och med versioner som lanseras efter augusti 2018 har gör vi följande ändringar i vår supportmodell:  

* Endast de server-versioner stöds, inte klienten.  
* Nya versioner av [Azure Linux-godkända distributioner](../../virtual-machines/linux/endorsed-distros.md) stöds alltid.  
* Alla mindre versioner stöds för varje större version i listan.
* Versioner som har klarat sina tillverkarens support upphör datum stöds inte.  
* Nya versioner av AMI stöds inte.  
* Endast de versioner som kör SSL 1.x som standard som stöds.

>[!NOTE]
>Om du använder en distribution eller en version som stöds för närvarande inte och inte justeras till vår supportmodell, rekommenderar vi att du Förgrena lagringsplatsen uppmärksammades att Microsoft-supporten inte kommer ge hjälp med förgrenade agent versioner.

* Amazon Linux 2017.09 (x 64)
* CentOS Linux 6 (x86/x64) och 7 (x 64)  
* Oracle Linux 6 och 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) och 7 (x 64)
* Debian GNU/Linux 8 och 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) och 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x 64)

>[!NOTE]
>OpenSSL 1.1.0 stöds bara på x86_x64 plattformar (64-bitars) och OpenSSL tidigare än 1.x inte stöds på valfri plattform.
>

### <a name="agent-prerequisites"></a>Krav för klientagenten

Följande tabell visar de paket som krävs för distributioner som stöds Linux som agenten ska installeras på.

|Nödvändigt paket |Beskrivning |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0.x eller 1.1.x |
|Curl | cURL webbklienten | 7.15.5 |
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standard syslog-daemon på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för syslog-händelseinsamling. Om du vill samla in syslog-data från den här versionen av dessa distributioner bör rsyslog-daemon installeras och konfigureras för att ersätta sysklog.

## <a name="tls-12-protocol"></a>TLS 1.2-protokollet
Om du vill se till att skydda data under överföring till Azure Monitor-loggar, rekommenderar vi starkt att du kan konfigurera att agenten ska du använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har påträffats sårbara och de fungerar fortfarande för närvarande för att tillåta bakåtkompatibilitet kompatibilitet, de arbetar **rekommenderas inte**.  Mer information [skickar data på ett säkert sätt med hjälp av TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Krav på brandvägg
Informationen nedan proxy och brandvägg konfigurationsuppgifter som efterfrågas för Linux och Windows-agenten att kommunicera med Azure Monitor-loggar.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |  
|*.azure-automation.net |Port 443 |Utgående|Ja |  

Brandväggen information krävs för Azure Government finns i [hantering av Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten att använda runbooks i din miljö, måste den ha åtkomst till portnumret och URL: erna som beskrivs i [konfigurera nätverket för den Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Windows och Linux-agenten har stöd för kommunikation antingen via en proxyserver eller Log Analytics-gateway till Azure Monitor med hjälp av HTTPS-protokollet.  Både anonyma och grundläggande autentisering (användarnamn/lösenord) stöds.  För Windows-agenten är ansluten direkt till tjänsten proxykonfigurationen har angetts under installationen eller [efter distributionen](agent-manage.md#update-proxy-settings) från Kontrollpanelen eller med PowerShell.  

För Linux-agenten proxyservern har angetts under installationen eller [efter installationen](agent-manage.md#update-proxy-settings) genom att ändra konfigurationsfilen proxy.conf.  Konfigurationsvärdet för proxyn för Linux-agenten har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Om proxyservern inte kräver att autentisera, kräver Linux-agenten fortfarande ett pseudo/användarlösenord. Detta kan vara valfri användarnamn eller lösenord.

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | https |
|Användare | Valfritt användarnamn för proxy-autentisering |
|lösenord | Lösenord för proxyautentisering |
|proxyhost | Adressen eller FQDN för proxy server/Log Analytics-gateway |
|port | Valfritt portnumret för proxy server/Log Analytics-gateway |

Exempel: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken som ”\@” i ditt lösenord kan du får ett anslutningsfel för proxy eftersom värdet tolkas felaktigt.  Undvik problemet genom att koda lösenordet i URL: en med ett verktyg som [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Installera och konfigurera agenten 
Ansluta datorer i din Azure-prenumeration eller hybridmiljö direkt med Azure Monitor-loggar kan utföras med hjälp av olika metoder beroende på dina krav. Följande tabell visar varje metod för att avgöra vilken som fungerar bäst i din organisation.

|Källa | Metod | Beskrivning|
|-------|-------------|-------------|
|Azure VM| -Log Analytics VM-tillägg för [Windows](../../virtual-machines/extensions/oms-windows.md) eller [Linux](../../virtual-machines/extensions/oms-linux.md) med Azure CLI eller med en Azure Resource Manager-mall<br>- [Manuellt från Azure portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | Tillägget Log Analytics-agenten installeras på virtuella Azure-datorer och registrerar dem i en befintlig Azure Monitor-arbetsyta.|
| Windows-hybriddator|- [Manuell installation](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installera Microsoft Monitoring agent från kommandoraden eller med hjälp av en automatiserad metod, till exempel Azure Automation DSC [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), eller med en Azure Resource Manager-mall om du har distribuerat Microsoft Azure Stack i ditt datacenter.| 
| Linux-hybriddator| [Manuell installation](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installera agenten för Linux som anropar ett wrapper-skript som finns på GitHub. | 
| System Center Operations Manager|[Integrera Operations Manager med Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurera integrering mellan Operations Manager och Azure Monitor-loggar att vidarebefordra insamlade data från Windows-datorer som rapporterar till en hanteringsgrupp.|  

## <a name="next-steps"></a>Nästa steg

* Granska [datakällor](../../azure-monitor/platform/agent-data-sources.md) att förstå datakällorna som är tillgängliga för att samla in data från din Windows- eller Linux-system. 

* Lär dig mer om [logga frågor](../../azure-monitor/log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar. 

* Lär dig mer om [övervakningslösningar](../../azure-monitor/insights/solutions.md) som lägger till funktioner i Azure Monitor och också samla in data till Log Analytics-arbetsytan.
