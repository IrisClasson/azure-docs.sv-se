---
title: Har en klassisk måttavisering meddela en icke-Azure-system med en webhook
description: Lär dig att dirigera om Azure måttaviseringar till andra, icke-Azure-system.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345792"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Har en klassisk måttavisering meddela en icke-Azure-system med en webhook
Du kan använda webhooks för att dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade åtgärder. Du kan använda en webhook på en avisering och dirigerar den till tjänster som skickar SMS-meddelanden, logga buggar, för att meddela ett team via chatt eller meddelandetjänster eller för olika åtgärder. 

Den här artikeln beskriver hur du anger en webhook för en Azure metrisk varning. Den visar även hur nyttolast för HTTP-POST till en webhook som ser ut. Information om installation och schemat för en Azure aktivitet log avisering (varning vid händelser), finns i [anropa en webhook i en Azure aktivitetsloggavisering](alerts-log-webhook.md).

Azure-aviseringar använder HTTP POST för att skicka aviseringar innehållet i JSON-format till en webhook-URI som du anger när du skapar aviseringen. Schemat har definierats senare i den här artikeln. URI: N måste vara en giltig HTTP eller HTTPS-slutpunkt. Azure skickar en post per begäran när en avisering har aktiverats.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurera webhooks via Azure portal
Lägga till eller uppdatera webhooken URI, i den [Azure-portalen](https://portal.azure.com/)går du till **skapa/uppdatera aviseringar**.

![Lägg till en varningsregel-fönstret](./media/alerts-webhooks/Alertwebhook.png)

Du kan också konfigurera en avisering för att publicera till en webhook-URI: N med hjälp av [Azure PowerShell-cmdlets](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), ett [plattformsoberoende CLI](../../azure-monitor/platform/cli-samples.md#work-with-alerts), eller [Azure Monitor REST API: er](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan autentisera med hjälp av tokenbaserad auktorisering. Webhooken sparas URI med ett token-ID. Exempel: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Nyttolast-schema
POST-åtgärd innehåller följande JSON-nyttolast och schemat för alla mått-baserade aviseringar:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Fält | Obligatorisk | Fast uppsättning värden | Anteckningar |
|:--- |:--- |:--- |:--- |
| status |Y |Aktiverat, löst |Status för aviseringen baserat på villkor du anger. |
| context |Y | |I aviseringssammanhanget. |
| timestamp |Y | |Den tid då aviseringen utlöstes. |
| id |Y | |Varje varningsregeln har ett unikt ID. |
| name |Y | |Aviseringens namn. |
| description |Y | |En beskrivning av aviseringen. |
| conditionType |Y |Mått, händelse |Två typer av aviseringar som stöds: mått- och. Måttaviseringar baseras på en måttvillkor. Aviseringar baseras på en händelse i aktivitetsloggen. Använd det här värdet om du vill kontrollera om aviseringen är baserad på ett mått eller på en händelse. |
| condition |Y | |Specifika fält att söka baserat på den **conditionType** värde. |
| metricName |För aviseringar för mått | |Namnet på det mått som definierar vad regeln övervakar. |
| metricUnit |För aviseringar för mått |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |Den enhet som tillåts i måttet. Se [tillåtna värden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |För aviseringar för mått | |Det faktiska värdet för det mått som orsakade aviseringen. |
| threshold |För aviseringar för mått | |Tröskelvärdet då aviseringen har aktiverats. |
| windowSize |För aviseringar för mått | |Tidsperioden som används för att övervaka Aviseringsaktivitet baserat på tröskelvärdet. Värdet måste vara mellan 5 minuter och 1 dag. Värdet måste vara i ISO 8601-format för varaktighet. |
| timeAggregation |För aviseringar för mått |Genomsnittlig, senaste, högsta, Minimum, None, totalt |Hur ska de data som samlas in kombineras med tiden. Standardvärdet är medelvärde. Se [tillåtna värden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |För aviseringar för mått | |Den operator som används för att jämföra den aktuella måttdata till det angivna tröskelvärdet. |
| subscriptionId |Y | |Azure-prenumerations-ID. |
| resourceGroupName |Y | |Namnet på resursgruppen för resursen som påverkas. |
| resourceName |Y | |Resursnamnet för resursen som påverkas. |
| resourceType |Y | |Resurstypen för resurser som påverkas. |
| resourceId |Y | |Resurs-ID för resursen som påverkas. |
| resourceRegion |Y | |Den region eller plats för resurser som påverkas. |
| portalLink |Y | |En direktlänk till sammanfattningssidan portal resurs. |
| properties |N |Valfri |En uppsättning nyckel/värde-par som innehåller information om händelsen. Till exempel `Dictionary<String, String>`. För egenskapsfältet är valfritt. I ett anpassat gränssnitt eller logic app-baserade arbetsflödet, kan användarna ange nyckel/värde-par som kan skickas via nyttolasten. Ett annat sätt att skicka anpassade egenskaper till webhooken är via webhooken URI (som frågeparametrar). |

> [!NOTE]
> Du kan ange den **egenskaper** fältet genom att använda [Azure Monitor REST API: er](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure-aviseringar och webhooks i videon [integrera Azure-aviseringar med PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Lär dig hur du [köra skript i Azure Automation (runbooks) på Azure-aviseringar](https://go.microsoft.com/fwlink/?LinkId=627081).
* Lär dig hur du [använder en logikapp för att skicka ett SMS-meddelande via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Lär dig hur du [använder en logikapp för att skicka ett Slack-meddelande från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Lär dig hur du [använder en logikapp för att skicka ett meddelande till en Azure-kö från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

