---
title: Samla in data från insamlade i Log Analytics | Microsoft Docs
description: Insamlade är en Linux-daemon för öppen källkod som regelbundet samlar in data från program och system nivåinformation.  Den här artikeln innehåller information om att samla in data från insamlade i Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 1a6ccb42e00431c16ce7767f19d6cdc9cbb3cffe
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841701"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Samla in data från insamlade på Linux-agenter i Log Analytics
[Insamlade](https://collectd.org/) är en Linux-daemon för öppen källkod som regelbundet samlar in prestandamått från program och system nivåinformation. Exempelprogram är Java Virtual Machine (JVM), MySQL-Server och Nginx. Den här artikeln innehåller information om att samla in prestandadata från insamlade i Log Analytics.

En fullständig lista över tillgängliga plugin-program finns på [tabellen av plugin-program](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Översikt över insamlade](media/data-sources-collectd/overview.png)

Följande insamlade konfiguration ingår i Log Analytics-agenten för Linux för att vidarebefordra insamlade data till Log Analytics-agenten för Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Även om du använder en versionerna av insamlade innan 5.5 använder du följande konfiguration i stället.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Insamlade konfigurationen använder standard`write_http` plugin-program för att skicka mätvärden prestandadata via port 26000 till Log Analytics-agenten för Linux. 

> [!NOTE]
> Den här porten kan konfigureras till ett egendefinierat porten om det behövs.

Log Analytics-agenten för Linux också lyssnar på port 26000 för insamlade mått och konverterar dem till Log Analytics-schemat mått. Följande är Log Analytics-agenten för Linux-konfiguration `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Versioner som stöds
- Log Analytics stöder för närvarande insamlade version 4.8 och senare.
- Log Analytics-agenten för Linux v1.1.0-217 eller högre krävs för insamlade mått samling.


## <a name="configuration"></a>Konfiguration
Följande är de grundläggande stegen för att konfigurera insamling av insamlade data i Log Analytics.

1. Konfigurera insamlade för att skicka data till Log Analytics-agenten för Linux med hjälp av plugin-programmet write_http.  
2. Konfigurera Log Analytics-agenten för Linux för att lyssna efter insamlade data på rätt port.
3. Starta om insamlade och Log Analytics-agenten för Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurera insamlade för att vidarebefordra data 

1. Att vidarebefordra insamlade data till Log Analytics-agenten för Linux, `oms.conf` måste läggas till i insamlades konfigurationskatalogen. Mål för den här filen är beroende av Linux-distribution på din dator.

    Om din insamlade config katalog finns i /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Om din insamlade config katalog finns i /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Du behöver ändra taggar i för insamlade versioner före 5.5 `oms.conf` enligt ovan.
    >

2. Kopiera collectd.conf till den önskade arbetsytan omsagent konfigurationskatalogen.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Starta om insamlade och Log Analytics-agenten för Linux med följande kommandon.

    sudo service insamlade omstart sudo /opt/microsoft/omsagent/bin/service_control omstart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Insamlade mått till Log Analytics schemakonverteringen
Om du vill upprätthålla en modell med välbekanta infrastruktur mått som redan har samlats in av Log Analytics-agenten för Linux och den nya måtten som samlas in av insamlade följande schemamappning används:

| Insamlade mått fält | Log Analytics-fält |
|:--|:--|
| värd | Dator |
| Plugin-programmet | Ingen |
| plugin_instance | Instansnamn<br>Om **plugin_instance** är *null* sedan InstanceName = ”*_Total*” |
| typ | Objektnamn |
| type_instance | CounterName<br>Om **type_instance** är *null* sedan CounterName =**tom** |
| dsnames] | CounterName |
| dstypes | Ingen |
| värden] | CounterValue |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggsökningar](../../azure-monitor/log-query/log-query-overview.md) att analysera data som samlas in från datakällor och lösningar. 
* Använd [anpassade fält](../../log-analytics/log-analytics-custom-fields.md) att parsa data från syslog-poster i enskilda fält.

