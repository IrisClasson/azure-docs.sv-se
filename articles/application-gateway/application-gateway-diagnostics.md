---
title: Övervaka åtkomstloggar, Prestandaloggar, backend-hälsotillstånd och mått för Azure Application Gateway
description: Lär dig hur du aktiverar och hanterar åtkomstloggar och Prestandaloggar för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: amitsriva
ms.openlocfilehash: a8b0ee159b1c4a4072ce5a86f9fb925744a415b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048714"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway

Med Azure Application Gateway kan övervaka du resurser på följande sätt:

* [Backend-hälsotillstånd](#back-end-health): Application Gateway ger möjlighet att övervaka hälsotillståndet för servrarna i backend-adresspooler via Azure-portalen och genom PowerShell. Du kan också hitta hälsotillståndet för backend-adresspooler via prestanda diagnostikloggar.

* [Loggar](#diagnostic-logging): Loggar kan prestanda, åtkomst och andra data sparas eller konsumeras från en resurs för övervakning.

* [Mått](#metrics): Application Gateway har för närvarande sju mått för att visa prestandaräknare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Backend-hälsotillstånd

Application Gateway ger möjlighet att övervaka hälsotillståndet för enskilda medlemmar i backend-adresspooler via portalen, PowerShell och kommandoradsgränssnittet (CLI). Du hittar också det aggregerade hälsotillståndet för backend-adresspooler via prestanda diagnostikloggar. 

Backend-hälsorapporten visar utdata för Application Gateway-hälsoavsökning till backend-instanser. När avsökningen lyckas och backend-servern kan ta emot trafik, betraktas den som felfri. Annars anses vara felaktig.

> [!IMPORTANT]
> Om det finns en nätverkssäkerhetsgrupp (NSG) i en Application Gateway-undernät, öppnar du portintervall 65503 65534 på Application Gateway-undernätet för inkommande trafik. Intervallet måste anges för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte initiera alla ändringar på dessa slutpunkter.


### <a name="view-back-end-health-through-the-portal"></a>Visa backend-hälsotillstånd via portalen

Backend-hälsotillstånd tillhandahålls automatiskt i portalen. Välj i en befintlig Programgateway **övervakning** > **serverdelens hälsotillstånd**. 

Varje medlem i backend adresspoolen visas på den här sidan (om det är ett nätverkskort, IP- eller FQDN). Backend-poolnamn, port, namn på backend-http-inställningar och hälsostatusen visas. Giltiga värden för hälsostatus är **felfri**, **ohälsosamma**, och **okänd**.

> [!NOTE]
> Om du ser statusen backend-hälsotillstånd **okänd**, se till att åtkomst till backend-servern inte blockeras av en NSG-regel, en användardefinierad väg (UDR) eller en anpassad DNS i det virtuella nätverket.

![Backend-hälsotillstånd][10]

### <a name="view-back-end-health-through-powershell"></a>Visa backend-hälsotillstånd via PowerShell

Följande PowerShell-kod visar hur du visar backend-hälsotillstånd genom att använda den `Get-AzApplicationGatewayBackendHealth` cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Visa backend-hälsotillstånd via Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultat

Följande kodavsnitt visar ett exempel på svaret:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Diagnostikloggar

Du kan använda olika typer av loggar i Azure för att hantera och felsöka programgatewayer. Du kan komma åt vissa av de här loggarna via portalen. Alla loggar kan extraheras från Azure Blob storage och visas i olika verktyg, till exempel [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md), Excel och Power BI. Du kan läsa mer om de olika typerna av loggar i listan nedan:

* **Aktivitetsloggen**: Du kan använda [Azure-aktivitetsloggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) (tidigare känt som driftloggar och granskningsloggar) att visa alla åtgärder som skickas till din Azure-prenumeration och deras status. Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.
* **Åtkomstlogg**: Du kan använda den här loggen visar Application Gateway åtkomstmönster och analyserar viktig information. Detta inkluderar anroparens IP, begärd URL, svarsfördröjning, returkod och byte in och ut. En åtkomstlogg samlas in var 300 sekund. Den här loggfilen innehåller en post per instans av Application Gateway. Application Gateway-instans kan identifieras av egenskapen instanceId.
* **Prestandaloggen**: Du kan använda den här loggfilen för att visa hur Application Gateway-instanser utför. Den här loggfilen innehåller prestandainformation om för varje instans, inklusive totalt antal begäranden som hanteras, genomflöde i byte, totalt antal begäranden hanteras, antalet misslyckade förfrågningar och antal felfria och defekta backend-instanser. En prestandalogg som samlas in var 60: e sekund.
* **Brandväggslogg**: Du kan använda den här loggfilen för att visa förfrågningar som loggas via identifiering eller förhindra läget för en Programgateway med brandväggen för webbaserade program.

> [!NOTE]
> Loggarna är endast tillgänglig för resurser som har distribuerats i Azure Resource Manager-distributionsmodellen. Du kan inte använda loggar för resurser i den klassiska distributionsmodellen. En bättre förståelse av de två modellerna, finns i den [förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md) artikeln.

Du har tre alternativ för att lagra dina loggar:

* **Lagringskonto**: Storage-konton är bäst för loggar när loggar lagras under en längre period och granskat när det behövs.
* **Händelsehubbar**: Händelsehubbar är ett bra alternativ för att integrera med andra säkerhet och händelsehantering (SEIM) hanteringsverktyg för att få meddelanden om dina resurser.
* **Azure Monitor-loggar**: Azure Monitor-loggar är bäst för allmän realtidsövervakning av ditt program eller leta efter trender.

### <a name="enable-logging-through-powershell"></a>Aktivera loggning med PowerShell

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Du måste aktivera åtkomst och prestanda som loggning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning:

1. Anteckna resurs-ID:t för det lagringskonto där loggdata lagras. Det här värdet är i formatet: /subscriptions/\<subscriptionId\>/resourceGroups/\<Resursgruppsnamn\>/providers/Microsoft.Storage/storageAccounts/\<lagringskontonamn\>. Du kan använda valfritt lagringskonto i din prenumeration. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för storage-konto](./media/application-gateway-diagnostics/diagnostics1.png)

2. Observera din Programgateway resurs-ID som har aktiverats. Det här värdet är i formatet: /subscriptions/\<subscriptionId\>/resourceGroups/\<Resursgruppsnamn\>/providers/Microsoft.Network/applicationGateways/\<application gateway-namn \>. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för application gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Aktivera diagnostisk loggning med följande PowerShell-cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Aktivitetsloggar kräver inte ett separat lagringskonto. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

### <a name="enable-logging-through-the-azure-portal"></a>aktivera loggning via Azure Portal

1. Hitta din resurs i Azure-portalen och välj **diagnostikinställningar**.

   Tre loggar är tillgängliga för Application Gateway:

   * Åtkomstlogg
   * Prestandaloggen
   * Brandväggslogg

2. Om du vill börja samla in data, Välj **slå på diagnostik**.

   ![Aktivera diagnostik][1]

3. På sidan **Diagnostikinställningar** kan du göra inställningar för diagnostikloggarna. I det här exemplet lagras loggarna i Log Analytics. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![Börjar konfigurationsprocessen][2]

5. Skriv ett namn för inställningarna, kontrollerar du inställningarna och välj **spara**.

### <a name="activity-log"></a>Aktivitetslogg

Azure genererar aktivitetsloggen som standard. Loggarna bevaras i 90 dagar i arkivet för Azure-händelseloggar. Läs mer om de här loggarna genom att läsa den [visa händelser och aktivitetsloggen](../monitoring-and-diagnostics/insights-debugging-with-events.md) artikeln.

### <a name="access-log"></a>Åtkomstlogg

Åtkomst-loggen skapas endast om du har aktiverat det på varje Application Gateway-instans, enligt beskrivningen i föregående steg. Data lagras i lagringskontot som du angav när du har aktiverat loggning. Varje åtkomst till Application Gateway loggas i JSON-format, som visas i följande exempel för v1:

|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway-instans som hanteras av begäran.        |
|clientIP     | Ursprungliga IP-Adressen för begäran.        |
|clientPort     | Ursprungliga port för begäran.       |
|httpMethod     | HTTP-metod som används i begäran.       |
|requestUri     | URI för fick begäran.        |
|RequestQuery     | **Server-dirigerat**: Backend-poolen-instans som har skickats begäran.</br>**X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som användes för begäran. Det kan användas för att felsöka problem med trafik på backend servrarna. </br>**SERVER-STATUS**: HTTP-svarskoden som Application Gateway har fått från backend-servern.       |
|UserAgent     | Användaragent från HTTP-frågehuvudet.        |
|httpStatus     | HTTP-statuskoden som returneras till klienten från Programgatewayen.       |
|httpVersion     | HTTP-version för begäran.        |
|ReceivedBytes     | Storleken på paket togs emot i byte.        |
|SentBytes| Storleken på paketet som skickades i byte.|
|timeTaken| Lång tid (i millisekunder) som det tar för en begäran att bearbetas och svaret skickas. Detta beräknas som intervall från den tidpunkt när Application Gateway får den första byten i en HTTP-begäran till den tidpunkt när svaret skickar åtgärden har slutförts. Det är viktigt att notera att fältet Time-Taken vanligtvis innehåller den tid som begäran och svar-paket är på resande fot över nätverket. |
|sslEnabled| Om kommunikation till backend-adresspooler används för SSL. Giltiga värden är på och stänga av.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```
För Programgatewayen och WAF v2 visar loggarna lite mer information:

|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway-instans som hanteras av begäran.        |
|clientIP     | Ursprungliga IP-Adressen för begäran.        |
|clientPort     | Ursprungliga port för begäran.       |
|httpMethod     | HTTP-metod som används i begäran.       |
|requestUri     | URI för fick begäran.        |
|RequestQuery     | **Server-dirigerat**: Backend-poolen-instans som har skickats begäran.</br>**X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som användes för begäran. Det kan användas för att felsöka problem med trafik på backend servrarna. </br>**SERVER-STATUS**: HTTP-svarskoden som Application Gateway har fått från backend-servern.       |
|UserAgent     | Användaragent från HTTP-frågehuvudet.        |
|httpStatus     | HTTP-statuskoden som returneras till klienten från Programgatewayen.       |
|httpVersion     | HTTP-version för begäran.        |
|ReceivedBytes     | Storleken på paket togs emot i byte.        |
|SentBytes| Storleken på paketet som skickades i byte.|
|timeTaken| Lång tid (i millisekunder) som det tar för en begäran att bearbetas och svaret skickas. Detta beräknas som intervall från den tidpunkt när Application Gateway får den första byten i en HTTP-begäran till den tidpunkt när svaret skickar åtgärden har slutförts. Det är viktigt att notera att fältet Time-Taken vanligtvis innehåller den tid som begäran och svar-paket är på resande fot över nätverket. |
|sslEnabled| Om kommunikation till backend-adresspooler används för SSL. Giltiga värden är på och stänga av.|
|sslCipher| Chiffersvit som används för SSL-kommunikation (om SSL är aktiverat).|
|sslProtocol| SSL-protokollet som används (om SSL är aktiverat).|
|serverRouted| Backend-servern som application gateway dirigerar begäran till.|
|serverStatus| HTTP-statuskod för backend-servern.|
|serverResponseLatency| Svarstid för svaret från backend-servern.|
|host| Adress som anges i värdhuvudet i begäran.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "52.231.230.101"
    }
}
```

### <a name="performance-log"></a>Prestandaloggen

Prestandaloggen skapas endast om du har aktiverat det på varje Application Gateway-instans, enligt beskrivningen i föregående steg. Data lagras i lagringskontot som du angav när du har aktiverat loggning. Loggdata för prestanda har genererats i 1-minutersintervall. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|instanceId     |  Application Gateway-instans för vilka data som genereras. Det finns en rad per instans för en Programgateway med flera instanser.        |
|HealthyHostCount     | Antal felfria värdar i backend-poolen.        |
|unHealthyHostCount     | Antal defekta värdar i backend poolen.        |
|requestCount     | Antal begäranden som hanteras.        |
|svarstid | Genomsnittlig svarstid (i millisekunder) för förfrågningar från instansen till backend-server som hanterar begäranden. |
|failedRequestCount| Antal misslyckade begäranden.|
|Dataflöde| Genomsnittligt dataflöde sedan senaste loggen, mätt i byte per sekund.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Svarstid beräknas från den tidpunkt när den första byten i HTTP-begäran tas emot till den tidpunkt då den sista byten i HTTP-svar skickas. Det är summan av bearbetningstiden som Application Gateway plus nätverkskostnad till backend-servern är den tid som backend-servern tar för att bearbeta begäran.

### <a name="firewall-log"></a>Brandväggslogg

Brandväggsloggen skapas endast om du har aktiverat det för varje application gateway, enligt beskrivningen i föregående steg. Den här loggfilen kräver också att brandväggen för webbaserade program har konfigurerats på en Programgateway. Data lagras i lagringskontot som du angav när du har aktiverat loggning. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway-instans för vilken brandvägg genereras data. Det finns en rad per instans för en Programgateway med flera instanser.         |
|clientIp     |   Ursprungliga IP-Adressen för begäran.      |
|clientPort     |  Ursprungliga port för begäran.       |
|requestUri     | URL för fick begäran.       |
|ruleSetType     | Ange för regeln. Tillgängliga värdet är OWASP.        |
|ruleSetVersion     | Regeluppsättning version som används. Tillgängliga värden är 2.2.9 och 3.0.     |
|ruleId     | Regel-ID för den utlösande händelsen.        |
|meddelande     | Användarvänligt meddelande för utlösande händelsen. Mer information finns i informationsavsnittet.        |
|åtgärd     |  Åtgärder som vidtas för begäran. Tillgängliga värden är blockerade och tillåts.      |
|webbplats     | Plats för vilken loggen har genererats. För närvarande kan visas endast globala eftersom regler är globala.|
|detaljer     | Information om den utlösande händelsen.        |
|details.message     | Beskrivning av regeln.        |
|details.data     | Specifika data hittades i begäran som matchar regeln.         |
|details.file     | Konfigurationsfil som innehåller regeln.        |
|details.Line     | Radnumret i konfigurationsfilen som utlöste händelsen.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg**: Hämta information från aktivitetsloggen via Azure PowerShell, Azure CLI, Azure REST API eller Azure-portalen. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Med [innehållspaketet Azure aktivitetsloggar för Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysera dina data på förkonfigurerade instrumentpaneler, som du både kan anpassa och använda i befintligt skick.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visa och analysera åtkomst, prestanda och loggar från brandväggen

[Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md) kan samla in prestandaräknare och händelseloggen filerna från Blob storage-kontot. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analysera loggar för åtkomst via GoAccess

Vi har publicerat en Resource Manager-mall som installerar och kör populära [GoAccess](https://goaccess.io/) logga analyzer för Åtkomstloggar för Application Gateway. GoAccess ger värdefull HTTP-trafik statistik, till exempel unika besökare, begärt filer, värdar, operativsystem, webbläsare, HTTP-statuskoder och mycket mer. Mer information finns i den [Readme-filen i mallmappen för Resource Manager-i GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser där du kan visa prestandaräknare i portalen. Följande mått är tillgängliga för Application Gateway:

- **Aktuella anslutningar**
- **Misslyckade förfrågningar**
- **Felfri värd-antal**

   Du kan filtrera på en per backend-pool regelbundet visar felfria/defekta värdar i en specifik serverdelspool.


- **Svarsstatus**

   Svaret status kod distribution kan kategoriseras ytterligare för att visa svar på 2xx, 3xx, 4xx eller 5xx kategorier.

- **Dataflöde**
- **Totalt antal begäranden**
- **Defekt antal värdar per backendpool**

   Du kan filtrera på en per backend-pool regelbundet visar felfria/defekta värdar i en specifik serverdelspool.

Bläddra till en application gateway under **övervakning** Välj **mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

I följande bild kan se du ett exempel med tre mått som visas för de senaste 30 minuterna:

[![](media/application-gateway-diagnostics/figure5.png "Måttvy")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

En aktuell lista över mått finns i [stöds mått med Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Varningsregler

Du kan börja Varningsregler baserat på mått för en resurs. En avisering kan till exempel anropa en webhook eller e-administratör om dataflödet för application gateway är ovan, under eller med ett tröskelvärde för en angiven tidsperiod.

I följande exempel visar hur du skapar en varningsregel som skickar ett e-postmeddelande till en administratör efter dataflöde överträdelser ett tröskelvärde:

1. Välj **Lägg till metrisk varning** att öppna den **Lägg till regel** sidan. Du kan också nå den här sidan från sidan mått.

   ![Knappen ”Lägg till metrisk varning”][6]

2. På den **Lägg till regel** , fylla i namn, villkor, och meddela avsnitt och välj **OK**.

   * I den **villkor** Väljaren, Välj något av fyra värden: **Större än**, **är större än eller lika med**, **mindre än**, eller **mindre än eller lika med**.

   * I den **Period** Väljaren, välja en viss från fem minuter till sex timmar.

   * Om du väljer **e-postägare, deltagare och läsare**, e-postmeddelandet kan vara dynamiska baserat på de användare som har åtkomst till resursen. I annat fall kan du ange en kommaavgränsad lista över användare i den **administratören email(s)** box.

   ![Lägg till regel sida][7]

Om tröskelvärdet överskrids, kommer ett e-postmeddelande som liknar det i följande bild:

![E-post för haft intrång tröskelvärde][8]

En lista över aviseringar visas när du har skapat en metrisk varning. Den innehåller en översikt över alla aviseringsregler.

![Listan över aviseringar och regler][9]

Läs mer om aviseringar i [Varningsaviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Om du vill veta mer om webhooks och hur du kan använda dem med aviseringar, besök [konfigurera en webhook i en Azure metrisk varning](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera prestandaräknare och händelseloggar genom att använda [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera dina Azure-aktivitetsloggen med Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogginlägg.
* [Visa och analysera Azure-aktivitetsloggar i Power BI med mera](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
