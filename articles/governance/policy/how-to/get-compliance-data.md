---
title: Hämta information om efterlevnadsprinciper
description: Azure Policy-utvärderingar och effekterna avgör efterlevnad. Lär dig hur du hämtar information om kompatibiliteten för dina Azure-resurser.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384418"
---
# <a name="get-compliance-data-of-azure-resources"></a>Hämta efterlevnads data för Azure-resurser

En av de största fördelarna med Azure Policy är insikter och kontrollerar att den ger över resurser i en prenumeration eller [hanterings grupp](../../management-groups/overview.md) av prenumerationer. Den här kontrollen kan utföras på många olika sätt, till exempel förhindrar resurser som skapas på fel plats, tillämpa gemensam och enhetlig taggen användning, eller granskning befintliga resurser för lämpliga konfigurationer och inställningar. I samtliga fall genereras data av Azure Policy så att du kan förstå miljöns kompatibilitetstillstånd.

Det finns flera sätt att komma åt kompatibilitetsinformationen som genereras av din princip och initiativtilldelningar:

- Använda [Azure Portal](#portal)
- Via [kommando rads](#command-line) skript

Innan du tittar på metoder för att rapportera om efterlevnad, låt oss titta på när kompatibilitetsinformation uppdateras och frekvens och händelser som utlöser en utvärderingscykel för datorprincip.

> [!WARNING]
> Om kompatibilitetstillstånd rapporteras som **ej registrerat**, verifierar du att **Microsoft. PolicyInsights** Resource Provider är registrerad och att användaren har rätt ROLLBASERAD åtkomst kontroll (RBAC)-behörighet enligt beskrivningen i [RBAC i Azure policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Utvärderingen utlösare

Resultatet av en slutförd utvärderings cykel finns i `Microsoft.PolicyInsights` Resource Provider via `PolicyStates` och `PolicyEvents` åtgärder. Mer information om åtgärder för Azure Policy insikter REST API finns i [Azure policy insikter](/rest/api/policy-insights/).

Utvärderingar av tilldelade principer och initiativ inträffa till följd av olika händelser:

- En princip eller ett initiativ har nyligen tilldelats ett omfång. Det tar cirka 30 minuter innan tilldelningen börjar tillämpas på den definierade omfattningen. När den används omvärderingscykeln på börjar för resurser inom detta omfång mot den nyligen tilldelade princip eller ett initiativ och, beroende på de effekter som används av principen eller initiativ, resurser markeras som kompatibla eller inkompatibla. En stor princip eller ett initiativ utvärderas mot en stor omfattning av resurser kan ta tid. Det är därför fördefinierade förväntar när omvärderingscykeln på slutförs. När testet är klart är uppdaterade kompatibilitetsresultat tillgängliga i portalen och SDK: er.

- En princip eller ett initiativ som redan har tilldelats till ett scope har uppdaterats. Utvärderingscykel för datorprincip och val av tidpunkt för det här scenariot är desamma som för en ny tilldelning till ett omfång.

- En resurs har distribuerats till ett omfång med en tilldelning via Resource Manager, REST, Azure CLI eller Azure PowerShell. I det här scenariot händelsen effekt (lägga till, granska, neka, distribuera) och kompatibel statusinformation för enskilda resursen blir tillgänglig i portalen och SDK: er ungefär 15 minuter senare. Den här händelsen orsakar inte en utvärdering av andra resurser.

- Utvärderingscykel för standard efterlevnad. En gång per dygn omvärderas tilldelningar automatiskt. En stor princip eller av många resurser kan ta tid, så det är fördefinierade förväntar när utvärderingen migreringscykel slutförs. När testet är klart är uppdaterade kompatibilitetsresultat tillgängliga i portalen och SDK: er.

- Resurs leverantören för [gäst konfigurationen](../concepts/guest-configuration.md) har uppdaterats med information om efterlevnad av en hanterad resurs.

- Genomsökning på begäran

### <a name="on-demand-evaluation-scan"></a>På begäran utvärderingssökning

En utvärderingssökning för en prenumeration eller resursgrupp kan startas med ett anrop till REST API. Skanningen är en asynkron process. Därför behöver inte REST-slutpunkt för att starta genomsökningen vänta tills genomsökningen är klar att svara. Det ger i stället en URI för att fråga efter statusen för den begärda utvärderingen.

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourRG}` – Ersätt med namnet på din resurs grupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

Genomsökningen har stöd för utvärdering av resurser i en prenumeration eller i en resursgrupp. Starta en sökning efter omfattning med ett REST API **post** -kommando med följande URI-strukturer:

- Prenumeration

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Resursgrupp

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Anropet returnerar status **202** . Som ingår i svars huvudet är en **plats** egenskap med följande format:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` har genererats statiskt för det begärda omfånget. Om ett scope redan kör en genomsökning på begäran, inte en ny genomsökning har startats. I stället får den nya begäran samma `{ResourceContainerGUID}` **plats** -URI för status. Ett REST API **Get** -kommando till **platsen** URI returnerar en **202 som godkänts** medan utvärderingen pågår. När utvärderings genomsökningen har slutförts returneras statusen **200 OK** . Brödtexten i en slutförd genomsökning är ett JSON-svar med statusen:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Så här fungerar efterlevnad

I en tilldelning är en resurs **icke-kompatibel** om den inte följer policy-eller initiativ regler.
I följande tabell visar hur olika effekter som fungerar med villkorsutvärderingen för resultatet av kompatibilitetstillståndet-princip:

| Resurstillstånd | Verkan | Principutvärdering | Kompatibilitetstillstånd |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE.
Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

Anta att du har en resursgrupp – ContsoRG, med vissa lagringskonton (markerat i rött) som exponeras för offentliga nätverk.

![Storage-konton som är exponerade för offentliga nätverk](../media/getting-compliance-data/resource-group01.png)

I det här exemplet behöver du vara försiktig med säkerhetsrisker. Nu när du har skapat en principtilldelning utvärderas för alla lagringskonton i resursgruppen ContosoRG. Den granskar de tre icke-kompatibla lagrings kontona, vilket innebär att deras tillstånd ändras till **icke-kompatibel.**

![Granskas icke-kompatibla storage-konton](../media/getting-compliance-data/resource-group03.png)

Utöver **kompatibla** och **icke-kompatibla**har principer och resurser tre andra tillstånd:

- **Konflikt**: det finns två eller fler principer med motstridiga regler. Till exempel två principer att lägga till samma tagg med olika värden.
- **Inte startat**: utvärderings cykeln har inte startat för principen eller resursen.
- **Inte registrerad**: den Azure policy Resource providern har inte registrerats eller så har det inloggade kontot inte behörighet att läsa efterlevnadsprinciper.

Azure Policy använder fälten **typ** och **namn** i definitionen för att avgöra om en resurs är en matchning. När resursen matchar, betraktas den som tillämplig och har statusen antingen **kompatibel** eller **icke-kompatibel**. Om antingen **typ** eller **namn** är den enda egenskapen i definitionen anses alla resurser vara tillämpliga och utvärderas.

Procent andelen kompatibilitet bestäms genom att dela upp **kompatibla** resurser av de _totala resurserna_.
_Totalt antal resurser_ definieras som summan av de **kompatibla**, **icke-kompatibla**och **motstridiga** resurserna. De övergripande kompatibilitets numren är summan av distinkta resurser som är **kompatibla** med summan av alla distinkta resurser. I bilden nedan finns det 20 distinkta resurser som är tillämpliga och endast en är **icke-kompatibel**. Övergripande resurskompatibilitet är 95% (19 av 20).

![Exempel på sidan efterlevnad av principer](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

Azure-portalen visar en grafisk upplevelse av att visualisera och förstå tillståndet för efterlevnad i din miljö. På **princip** sidan innehåller **översikts** alternativet information om tillgängliga omfång för efterlevnad av både principer och initiativ. Tillsammans med kompatibilitetsstatus och antal per tilldelning innehåller den ett diagram som visar efterlevnad under de senaste sju dagarna. Sidan **efterlevnad** innehåller ungefär samma information (förutom diagrammet), men innehåller ytterligare alternativ för filtrering och sortering.

![Exempel på sidan Azure Policy efterlevnad](../media/getting-compliance-data/compliance-page.png)

Eftersom en princip eller ett initiativ kan tilldelas till olika omfång, innehåller tabellen omfattningen för varje uppgift och vilken sorts definition som har tilldelats. Det finns också antalet icke-kompatibla resurser och icke-kompatibla principer för varje tilldelning. När du klickar på en princip eller ett initiativ i tabellen innehåller en närmare titt på kompatibilitet för den specifika tilldelningen.

![Exempel på sidan Azure Policy information om efterlevnad](../media/getting-compliance-data/compliance-details.png)

I listan över resurser på fliken **kompatibilitet** visas utvärderings status för befintliga resurser för den aktuella tilldelningen. Fliken är som standard **icke-kompatibel**, men kan filtreras.
Händelser (tillägg, granskning, neka, distribution) som utlöses av begäran om att skapa en resurs visas på fliken **händelser** .

> [!NOTE]
> För en AKS Engine-princip är resursen som visas resurs gruppen.

![Exempel på Azure Policy Compliance Events](../media/getting-compliance-data/compliance-events.png)

För resurser i [resurs leverantörs läge](../concepts/definition-structure.md#resource-provider-modes) går du till fliken **Resource Compliance (Resource Compliance** ) och markerar resursen eller högerklickar på raden och väljer **Visa kompatibilitetsinformation** öppnar komponenten Kompatibilitetsrapport. På den här sidan finns också flikar för att se de principer som har tilldelats den här resursen, händelser, komponent händelser och ändrings historik.

![Exempel på information om efterlevnad av Azure Policy-komponenter](../media/getting-compliance-data/compliance-components.png)

Tillbaka på sidan Resource Compliance (resurser) högerklickar du på den rad i händelsen som du vill samla in mer information om och väljer **Visa aktivitets loggar**. Aktivitetsloggsidan öppnas och är redan filtrerat till search som visar information för tilldelningen och händelser. Aktivitetsloggen innehåller ytterligare kontext och information om dessa händelser.

![Exempel på aktivitets logg för Azure Policy regelefterlevnad](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Förstå bristande efterlevnad

<a name="change-history-preview"></a>

När en resurs bedöms vara **icke-kompatibel**finns det många möjliga orsaker. Om du vill ta reda på orsaken till att en resurs är **icke-kompatibel** eller om du vill ha en ändrings ansvarig, kontrollerar du att det [inte är kompatibelt](./determine-non-compliance.md)

## <a name="command-line"></a>Kommandorad

Samma information som är tillgänglig i portalen kan hämtas med REST API (inklusive med [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell och Azure CLI (för hands version).
Fullständig information om REST API finns i [Azure policy Insights](/rest/api/policy-insights/) -referensen. Referenssidor för REST API har en grön ”prova”-knapp på varje åtgärd som gör att du kan prova direkt i webbläsaren.

Använd ARMClient eller ett liknande verktyg för att hantera autentisering till Azure för REST API exempel.

### <a name="summarize-results"></a>Sammanfatta resultat

Sammanfattning kan utföras av behållare, definitionen eller uppgiften med REST-API. Här är ett exempel på en sammanfattning på prenumerations nivån med Azure Policy Insight- [Sammanfattning för prenumeration](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Utdata innehåller en sammanfattning av prenumerationen. I exemplet nedan är den summerade kompatibiliteten under **Value. Results. nonCompliantResources** och **Value. Results. nonCompliantPolicies**. Den här förfrågan innehåller ytterligare information, inklusive varje tilldelning som består av icke-kompatibla siffrorna och definitionsinformation för varje tilldelning. Varje princip objekt i hierarkin innehåller en **queryResultsUri** som kan användas för att få ytterligare information på den nivån.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Fråga för resurser

I exemplet ovan tillhandahåller **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** en exempel-URI för alla icke-kompatibla resurser för en speciell princip definition. När du tittar på **$filter** -värdet är IsCompliant lika med (EQ) till false, PolicyAssignmentId har angetts för princip definitionen och sedan själva PolicyDefinitionId. Orsaken för att inkludera PolicyAssignmentId i filtret är PolicyDefinitionId kan finnas i flera principen eller initiativtilldelningar med olika omfång. Genom att ange både PolicyAssignmentId och PolicyDefinitionId kan vara vi explicit i resultaten som vi letar efter. Tidigare användes för PolicyStates vi **senaste**, vilket automatiskt ställer in ett **från** -och **till** -tidsfönster för de senaste 24 timmarna.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Svaret exemplet nedan har varit tas bort till en enda icke-kompatibel resurs av utrymmesskäl. Detaljerad svaret har flera olika typer av data om resursen, principen eller initiativ och tilldelningen. Observera att du kan också se vilka tilldelning parametrar skickades till principdefinitionen.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visa händelser

När en resurs skapas eller uppdateras, genereras en utvärderingsresultat av principen. Resultat kallas _princip händelser_. Använd följande URI: N om du vill visa den senaste Principhändelser som är associerade med prenumerationen.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Mer information om hur du frågar princip händelser finns i artikeln referens för [Azure policy händelser](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell-modulen för Azure Policy finns på PowerShell-galleriet som [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Med hjälp av PowerShellGet kan du installera modulen med `Install-Module -Name Az.PolicyInsights` (kontrol lera att du har de senaste [Azure PowerShellna](/powershell/azure/install-az-ps) installerade):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modulen har följande cmdletar:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exempel: Hämta status sammanfattning för den översta tilldelade principen med det högsta antalet icke-kompatibla resurser.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exempel: Hämta posten tillståndet för de nyligen utvärderat resurs (standard är efter tidsstämpel i fallande ordning).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exempel: Hämtning av information om alla inkompatibla virtuella nätverksresurser.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exempel: Hämta händelser relaterade till icke-kompatibla virtuella nätverksresurser som inträffade efter ett visst datum.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Fältet **PrincipalOid** kan användas för att hämta en speciell användare med Azure PowerShell cmdlet `Get-AzADUser`. Ersätt **{principalOid}** med svaret som du fick från föregående exempel.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Om du har en [Log Analytics arbets yta](../../../log-analytics/log-analytics-overview.md) med `AzureActivity` från [Aktivitetslogganalys-lösningen](../../../azure-monitor/platform/activity-log-collect.md) som är bunden till din prenumeration kan du också Visa inkompatibla resultat från utvärderings cykeln med hjälp av enkla Kusto-frågor och `AzureActivity` tabellen. Med information i Azure Monitor loggar kan aviseringar konfigureras för att se om de inte uppfyller kraven.


![Azure Policy kompatibilitet med hjälp av Azure Monitor loggar](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).