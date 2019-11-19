---
title: Azure Event Grid säkerhet och autentisering
description: Beskriver Azure Event Grid och dess begrepp.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: dfa53acaf392e225873a40b05b8517de2f9780dc
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169565"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid säkerhet och autentisering 

Azure Event Grid har tre typer av autentisering:

* WebHook-händelseleverans
* Prenumerationer på händelser
* Anpassat ämne publicering

## <a name="webhook-event-delivery"></a>WebHook-händelseleverans

Webhooks är en av de många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar kan skickar Event Grid-tjänsten en HTTP-begäran till den konfigurera slutpunkten med händelsen i begärandetexten.

Liksom många andra tjänster som stöder webhooks måste Event Grid du bevisa ägarskapet för Webhook-slutpunkt innan den startar leverera händelser till denna slutpunkt. Det här kravet förhindrar att en obehörig användare överbelasta slutpunkten med händelser. När du använder någon av de tre Azure-tjänster som anges nedan, hanterar den här verifieringen automatiskt i Azure-infrastrukturen:

* Azure Logic Apps med [Event Grid koppling](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions med [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md)

Om du använder någon annan typ av slutpunkt, t.ex. en HTTP-utlösare baserade Azure-funktion, måste din slutpunkt kod att delta i en verifiering handskakning med Event Grid. Event Grid stöder två olika sätt att verifiera prenumerationen.

1. **ValidationCode handskakning (programmässiga)** : Om du styra källkoden för din slutpunkt för den här metoden rekommenderas. Vid tidpunkten för händelsen prenumeration har skapats skickar Event Grid en händelse för verifiering av prenumeration till din slutpunkt. Schemat för den här händelsen är ungefär som andra Event Grid-händelse. Datamängden i den här händelsen innehåller en `validationCode` egenskapen. Programmet kontrollerar att begäran om verifiering är för en förväntad händelse-prenumeration och ekar verifieringskoden till Event Grid. Den här mekanismen för handskakning stöds i alla Event Grid-versioner.

2. **ValidationURL handskakning (manuell)** : I vissa fall kan du inte åtkomst till källkoden för slutpunkten att implementera ValidationCode-handskakningen. Exempel: Om du använder en tjänst från tredje part (t.ex. [Zapier](https://zapier.com) eller [IFTTT](https://ifttt.com/)), du programmässigt kan inte svara med verifieringskoden.

   Från och med versionen 2018-05-01-preview, Event Grid har stöd för en manuell verifiering-handskakning. Om du skapar en händelseprenumeration med ett SDK eller verktyg som använder API-versionen 2018-05-01-preview eller senare, Event Grid skickar en `validationUrl` -egenskapen i datamängden i händelsen prenumeration verifiering. För att slutföra handskakningen hitta URL: en i händelsedata och manuellt skickar en GET-begäran till den. Du kan använda antingen en REST-klient eller webbläsaren.

   Den angivna webb adressen är giltig i 5 minuter. Under denna tid har tillståndet för etablering av händelseprenumerationen är `AwaitingManualAction`. Om du inte slutför den manuella verifieringen inom 5 minuter anges etablerings statusen till `Failed`. Du kommer behöva skapa händelseprenumeration igen innan du startar manuell verifiering.

    Den här autentiseringsmetoden kräver också att webhook-slutpunkten returnerar en HTTP-statuskod på 200 så att den vet att posten för verifierings händelsen accepterades innan den kan placeras i manuellt validerings läge. Med andra ord, om slut punkten returnerar 200 men inte returnerar ett verifierings svar program mässigt, överförs läget till det manuella validerings läget. Om verifierings-URL: en hämtas inom 5 minuter anses verifierings hand skakningen vara lyckad.

> [!NOTE]
> Det finns inte stöd för att använda självsignerade certifikat för verifiering. Använd ett signerat certifikat från en certifikat utfärdare (CA) i stället.

### <a name="validation-details"></a>Verifieringsinformation

* Vid tidpunkten för händelsen skapande/uppdatering av prenumeration publicerar Event Grid en händelse för verifiering av prenumerationen till mål-slutpunkten. 
* Händelsen innehåller ett huvudvärde ”aeg Händelsetyp: SubscriptionValidation”.
* Händelsemeddelandet har samma schema som andra Event Grid-händelser.
* Händelsetyp-egenskapen för händelsen är `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Dataegenskapen för händelsen innehåller en `validationCode` egenskap med en slumpmässigt genererad sträng. Till exempel ”validationCode: acb13...”.
* Händelse data innehåller också en `validationUrl`-egenskap med en URL för att manuellt verifiera prenumerationen.
* Matrisen innehåller endast händelsen verifiering. Andra händelser skickas i en separat begäran när du tillbaka echo verifieringskoden.
* SDK: er för EventGrid dataplanen har klasser som motsvarar prenumerationen verifiering händelsedata och verifieringssvaret för prenumerationen.

Ett exempel SubscriptionValidationEvent visas i följande exempel:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

För att bevisa ägarskapet för slutpunkten echo tillbaka verifieringskoden i egenskapen validationResponse som visas i följande exempel:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Du måste returnera en HTTP 200 OK svars status kod. HTTP 202 accepterades inte som ett giltigt verifierings svar för Event Grid prenumeration. Http-begäran måste slutföras inom 30 sekunder. Om åtgärden inte slutförs inom 30 sekunder avbryts åtgärden och du kan försöka igen efter 5 sekunder. Om alla försöken Miss lyckas behandlas det som ett fel i validerings hand skakningen.

Eller så kan du verifiera manuellt prenumerationen genom att skicka en GET-begäran till URL: en för verifiering. Händelse prenumerationen är i ett väntande tillstånd tills den har verifierats. Verifierings-URL: en använder port 553. Om brand Väggs reglerna blockerar port 553 kan regler behöva uppdateras för lyckad hand skakning.

Hantera prenumeration verifiering handskakningen exempelvis se ett [ C# exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Checklista

Om du ser ett fel meddelande under skapandet av händelse prenumerationen, till exempel "försöket att validera den angivna slut punkten https:\//Your-Endpoint-here misslyckades. Mer information finns på https:\//aka.ms/esvalidation ", det betyder att det är ett haveri i validerings hand skakningen. Lös felet genom att kontrollera följande aspekter:

* Har du kontroll över programkoden i mål-slutpunkten? Till exempel om du skapar en HTTP-utlösare baserade Azure-funktion, har du åtkomst till programkod för att göra ändringar i den?
* Om du har åtkomst till programkoden kan implementera ValidationCode baserat handskakning mekanism som visas i exemplet ovan.

* Om du inte har åtkomst till programkoden (till exempel, om du använder en tredjepartstjänst som stöder webhooks) kan använda du mekanismen för manuell handskakning. Kontrollera att du använder 2018-05-01-preview API-versionen eller senare (installera tillägget för Event Grid Azure CLI) för att ta emot validationUrl i händelsen verifiering. Slutför manuell verifiering handskakningen genom att hämta värdet för den `validationUrl` egenskapen och gå till URL: en i webbläsaren. Om verifieringen lyckas, bör du se ett meddelande i din webbläsare att valideringen har lyckats. Du ser att händelsen prenumerationens provisioningState är ”lyckades”. 

### <a name="event-delivery-security"></a>Säkerhet med händelser leverans

#### <a name="azure-ad"></a>Azure AD

Du kan skydda webhook-slutpunkten genom att använda Azure Active Directory för att autentisera och auktorisera Event Grid att publicera händelser till dina slut punkter. Du måste skapa ett Azure Active Directory-program, skapa en roll-och tjänst princip i ditt program som auktoriserar Event Grid och konfigurera händelse prenumerationen så att den använder Azure AD-programmet. [Lär dig hur du konfigurerar AAD med event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Frågeparametrar
Du kan skydda din webhook-slutpunkt genom att lägga till frågeparametrar webhook-URL när du skapar en händelseprenumeration. Ange ett av dessa frågeparametrar ska vara en hemlighet som en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token). Webhooken kan använda hemlighet för att identifiera händelsen kommer från Event Grid med giltig behörighet. Event Grid tas dessa Frågeparametrar i varje händelseleverans till webhooken.

När du redigerar händelseprenumerationen frågeparametrar inte visas eller returneras, såvida inte den [--inkludera-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parametern används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Slutligen är det viktigt att notera att Azure Event Grid endast stöder HTTPS webhook-slutpunkter.

## <a name="event-subscription"></a>Händelseprenumeration

Du måste bekräfta att du har åtkomst till händelsekällan och hanterare för att prenumerera på en händelse. Bevisar att du äger en WebHook har beskrivs i föregående avsnitt. Om du använder en händelsehanterare som inte är en WebHook (till exempel en event hub eller queue storage), måste skrivbehörighet till resursen. Den här behörighetskontrollen kan obehöriga användare från att skicka händelser till din resurs.

Du måste ha den **Microsoft.EventGrid/EventSubscriptions/Write** behörighet på den resurs som är händelsekällan. Du behöver den här behörigheten eftersom du skriver en ny prenumeration på omfånget för resursen. Den begärda resursen skiljer sig beroende på om du prenumerera på ett system ämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>System-avsnitt (Azure-tjänsten utgivare)

För system ämnen behöver du behörighet att skriva en händelseprenumeration definitionsområdet för resursen publiceringen av händelsen. Formatet för resursen är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Till exempel att prenumerera på en händelse på ett lagringskonto med namnet **MITTKONTO**, du måste ha behörighet för Microsoft.EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen

För anpassade ämnen behöver du behörighet att skriva en händelseprenumeration definitionsområdet för event grid-ämne. Formatet för resursen är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Till exempel att prenumerera på ett anpassat ämne med namnet **mytopic**, du måste ha behörighet för Microsoft.EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Anpassat ämne publicering

Anpassade ämnen använda signatur för delad åtkomst (SAS) eller nyckelautentisering. Vi rekommenderar SAS, men nyckelautentisering ger enkel programmering och är kompatibel med många befintliga webhook-utgivare. 

Du kan inkludera autentiseringsvärdet för i HTTP-huvudet. SAS, använda **aeg-sas-token** för huvudets värde. Nyckelautentisering, använda **aeg-sas-nyckeln** för huvudets värde.

### <a name="key-authentication"></a>Nyckelautentisering

Nyckelautentisering är den enklaste formen av autentisering. Använd formatet: `aeg-sas-key: <your key>`

Exempelvis kan skicka du en nyckel med:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid är resursen, en förfallotid och en signatur. Formatet för SAS-token är: `r={resource}&e={expiration}&s={signature}`.

Resursen är sökvägen för event grid-ämne som du skickar händelser. Till exempel är en giltig resurssökväg: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Du kan skapa signaturen från en nyckel.

Till exempel en giltig **aeg-sas-token** värdet är:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

I följande exempel skapas en SAS-token för användning med Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Hantering av åtkomstkontroll

Azure Event Grid kan du kontrollera åtkomstnivån som ges till olika användare att utföra olika hanteringsåtgärder, till exempel listan händelseprenumerationer, skapa nya och generera nycklar. Event Grid använder Azures rollbaserad åtkomstkontroll (RBAC).

### <a name="operation-types"></a>Åtgärdstyper

Event Grid har stöd för följande åtgärder:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

De tre sista åtgärderna returnera potentiellt hemlig information, som hämtar filtrerade utanför normal läsåtgärder. Vi rekommenderar att du begränsar åtkomsten till dessa åtgärder. 

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid erbjuder två inbyggda roller för att hantera prenumerationer på händelser. De är viktigt när du implementerar [händelse domäner](event-domains.md) eftersom de ger användare de behörigheter som de behöver för att prenumerera på ämnen i händelsedomän. Dessa roller fokuserar på händelseprenumerationer och bevilja inte åtkomst för åtgärder som att skapa ämnen.

Du kan [tilldela dessa roller till en användare eller grupp](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription-deltagare**: hantera Event Grid prenumerations åtgärder

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription-läsare**: läsa Event Grid prenumerationer

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Anpassade roller

Om du vill ange behörigheter som skiljer sig från de inbyggda rollerna kan skapa du anpassade roller.

Följande är exempel Event Grid rolldefinitioner som användarna kan vidta olika åtgärder. Dessa anpassade roller skiljer sig från de inbyggda rollerna eftersom de ger bredare åtkomst än bara händelseprenumerationer.

**EventGridReadOnlyRole.json**: Tillåt endast skrivskyddade åtgärder.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Tillåt åtgärder begränsade efter Tillåt men inte ta bort åtgärder.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: tillåter alla event grid-åtgärder.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Du kan skapa anpassade roller med [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md), och [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [om Event Grid](overview.md)
