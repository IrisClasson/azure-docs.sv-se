---
title: Anpassa brandväggsregler för webbaserade program i Azure Application Gateway – Azure CLI
description: Den här artikeln innehåller information om hur du anpassar brandväggsregler för webbaserade program i Application Gateway med Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 70377e6414b41669b6d3e991d24136a3cc4270be
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618645"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Anpassa brandväggsregler för webbaserade program via Azure CLI

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Dessa skydd tillhandahålls av OWASP Open Web Application Security Project () Core regeln ange (CRS). Vissa regler kan leda till falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regelgrupper och -regler. Läs mer på den specifika regelgrupper och regler, [lista över web application firewall tillhandahållna CRS-regelgrupper och -regler](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

Följande kodexempel visar hur du kan visa regler och regelgrupper som kan konfigureras.

### <a name="view-rule-groups"></a>Visa regelgrupper

I följande exempel visas hur du visar regelgrupper:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Följande utdata är ett trunkerat svar från föregående exempel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Visa regler i en grupp

I följande exempel visas hur du visar regler i en angiven regelgrupp:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Följande utdata är ett trunkerat svar från föregående exempel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Inaktivera regler

I följande exempel inaktiverar regler `910018` och `910017` på application gateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Obligatoriska regler

I följande lista innehåller villkor som gör att WAF för att blockera begäran i Förhindringsläge (i identifieringsläge loggas som undantag). Dessa kan inte vara konfigurerad eller inaktiverad:

* Det gick inte att parsa begärandetexten resulterar i begäran blockeras, såvida inte brödtext inspektion stängs av (XML, JSON, formulärdata)
* Datalängden för begäran brödtext (utan att några filer) är större än den konfigurerade gränsen
* Brödtext (inklusive filer) är större än gränsen
* Ett internt fel inträffade i WAF-motorn

CRS 3.x specifika:

* Inkommande avvikelseidentifiering poäng överskred tröskeln

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler för kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
