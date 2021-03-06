---
title: Översikt över Azure Monitor Agent
description: Översikt över Azure Monitor Agent (AMA) som samlar in övervaknings data från gäst operativ systemet för virtuella datorer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e38d59ff1eb31dd5fc3ecf6b7df6b12504141d5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083173"
---
# <a name="azure-monitor-agent-overview-preview"></a>Översikt över Azure Monitor Agent (för hands version)
Azure Monitor agenten (AMA) samlar in övervaknings data från gäst operativ systemet på virtuella datorer och levererar det till Azure Monitor. Den här artikeln innehåller en översikt över Azure Monitor Agent, inklusive hur du installerar den och hur du konfigurerar data insamling.

## <a name="relationship-to-other-agents"></a>Förhållande till andra agenter
Azure Monitor Agent ersätter följande agenter som för närvarande används av Azure Monitor för att samla in gäst data från virtuella datorer:

- [Log Analytics-agent](log-analytics-agent.md) – skickar data till Log Analytics-arbetsytan och stöder Azure Monitor for VMS och övervaknings lösningar.
- [Diagnostiskt tillägg](diagnostics-extension-overview.md) – skickar data till Azure Monitor mått (endast Windows), Azure Event Hubs och Azure Storage.
- [Teleympkvistar-agenten](collect-custom-metrics-linux-telegraf.md) – skickar data till Azure Monitor mått (endast Linux).

Förutom att konsolidera den här funktionen i en enda agent ger Azure Monitor-agenten följande fördelar jämfört med befintliga agenter:

- Övervaknings omfång. Konfigurera samling centralt för olika uppsättningar med data från olika uppsättningar av virtuella datorer.  
- Linux multi-värdar: skicka data från virtuella Linux-datorer till flera arbets ytor.
- Windows-händelse filtrering: Använd XPATH-frågor för att filtrera vilka Windows-händelser som samlas in.
- Förbättrad tillägg hantering: Azure Monitor Agent använder en ny metod för att hantera utöknings barhet som är mer transparent och lättare än hanterings paket och Linux-plugin-program i de aktuella Log Analyticss agenterna.

### <a name="changes-in-data-collection"></a>Ändringar i data insamling
Metoderna för att definiera data insamling för befintliga agenter skiljer sig tydligt från varandra och var och en har utmaningar som åtgärdas med Azure Monitor Agent.

- Log Analytics agent hämtar konfigurationen från en Log Analytics arbets yta. Detta är enkelt att konfigurera, men svårt att definiera oberoende definitioner för olika virtuella datorer. Den kan bara skicka data till en Log Analytics-arbetsyta.

- Diagnostiskt tillägg har en konfiguration för varje virtuell dator. Detta är enkelt att definiera oberoende definitioner för olika virtuella datorer men svårt att hantera centralt. Den kan bara skicka data till Azure Monitor mått, Azure Event Hubs eller Azure Storage. För Linux-agenter krävs det att du måste skicka data till Azure Monitor mått med öppen källkod.

Azure Monitor Agent använder sig av [data insamlings regler (DCR)](data-collection-rule-overview.md) för att konfigurera data som ska samlas in från varje agent. Data insamlings regler möjliggör hantering av samlings inställningar i skala samtidigt som unika, begränsade konfigurationer för del mängder av datorer aktive ras. De är oberoende av arbets ytan och oberoende av den virtuella datorn, vilket gör att de kan definieras en gång och återanvändas på olika datorer och miljöer. Se [Konfigurera data insamling för Azure Monitor agenten (för hands version)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Aktuella begränsningar
Följande begränsningar gäller vid en offentlig för hands version av Azure Monitor agenten:

- Azure Monitor agenten har inte stöd för lösningar och insikter som Azure Monitor for VMs och Azure Security Center. Det enda scenario som stöds för närvarande är att samla in data med de data insamlings regler som du konfigurerar. 
- Data insamlings regler måste skapas i samma region som en Log Analytics arbets yta som används som mål.
- Endast virtuella Azure-datorer stöds för närvarande. Lokala virtuella datorer, skalnings uppsättningar för virtuella datorer, Arc för servrar, Azure Kubernetes-tjänster och andra beräknings resurs typer stöds inte för närvarande.
- Den virtuella datorn måste ha åtkomst till följande HTTPS-slutpunkter:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Samexistens med andra agenter
Azure Monitor agenten kan samverka med befintliga agenter så att du kan fortsätta att använda sina befintliga funktioner under utvärderingen eller migreringen. Detta är särskilt viktigt på grund av begränsningarna i offentlig för hands version i stöd för befintliga lösningar. Du bör vara försiktig när du samlar in dubblettdata eftersom detta kan skeva frågeresultat och leda till ytterligare avgifter för data inmatning och kvarhållning.

Azure Monitor for VMs använder exempelvis Log Analytics-agenten för att skicka prestanda data till en Log Analytics-arbetsyta. Du kan också ha konfigurerat arbets ytan för att samla in Windows-händelser och Syslog-händelser från agenter. Om du installerar Azure Monitor agenten och skapar en data insamlings regel för dessa händelser och prestanda data, resulterar det i dubbla data.


## <a name="costs"></a>Kostnader
Det kostar inget att Azure Monitor Agent, men du kan debiteras avgifter för inmatade data. Se [Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/) för information om Log Analytics data insamling och kvarhållning och för kund mått.

## <a name="data-sources-and-destinations"></a>Data källor och mål
I följande tabell visas de typer av data som du kan samla in med Azure Monitor-agenten med hjälp av regler för data insamling och var du kan skicka dessa data. Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en lista med insikter, lösningar och andra lösningar som använder Azure Monitor-agenten för att samla in andra typer av data.


Azure Monitor Agent skickar data till Azure Monitor mått eller en Log Analytics arbets yta som stöder Azure Monitor loggar.

| Datakälla | Mål | Beskrivning |
|:---|:---|:---|
| Prestanda        | Azure Monitor mått<br>Log Analytics-arbetsyta | Numeriska värden mäter prestanda för olika aspekter av operativ system och arbets belastningar. |
| Händelse loggar i Windows | Log Analytics-arbetsyta | Information som skickas till händelse loggnings systemet i Windows. |
| Syslog             | Log Analytics-arbetsyta | Information som skickas till händelse loggnings systemet i Linux. |


## <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande operativ system stöds för närvarande av Azure Monitor agenten.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14,04 LTS, 16,04 LTS, 18,04 LTS

> [!IMPORTANT]
> <sup>1</sup> För att dessa distributioner ska kunna skicka syslog-data måste du ta bort rsyslog och installera syslog-ng.


## <a name="security"></a>Säkerhet
Den Azure Monitor agenten behöver inte några nycklar utan kräver istället en [systemtilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Du måste ha en systemtilldelad hanterad identitet aktive rad på varje virtuell dator innan du distribuerar agenten.


## <a name="install-the-azure-monitor-agent"></a>Installera Azure Monitor Agent
Azure Monitor agenten implementeras som ett [Azure VM-tillägg](../../virtual-machines/extensions/overview.md) med informationen i följande tabell. 

| Egenskap | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. Monitor  | Microsoft. Azure. Monitor |
| Typ      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 0.9 |

Installera Azure Monitor agenten med någon av metoderna för att installera virtuella dator agenter, inklusive följande med hjälp av PowerShell eller CLI. Alternativt kan du installera agenten och konfigurera data insamling på virtuella datorer i din Azure-prenumeration med hjälp av portalen med proceduren som beskrivs i [Konfigurera data insamling för Azure Monitor agenten (för hands version)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --version 1.0 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -Version 1.0 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --version 0.9 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -Version 0.9 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Nästa steg

- [Skapa en data insamlings regel](data-collection-rule-azure-monitor-agent.md) för att samla in data från agenten och skicka den till Azure Monitor.
