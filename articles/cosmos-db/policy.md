---
title: Använd Azure Policy för att implementera styrning och kontroller för Azure Cosmos DB resurser
description: Lär dig hur du använder Azure Policy för att implementera styrning och kontroller för Azure Cosmos DB resurser.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: a1b1c01f7cf720690decd9c7aac5fb14b92121ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84431969"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Använd Azure Policy för att implementera styrning och kontroller för Azure Cosmos DB resurser

[Azure policy](../governance/policy/overview.md) hjälper till att upprätthålla organisations styrnings standarder, utvärdera resurs efterlevnad och implementera automatisk reparation. Vanliga användnings områden är säkerhet, kostnads hantering och konfigurations konsekvens.

Azure Policy innehåller inbyggda princip definitioner. Du kan skapa anpassade princip definitioner för scenarier som inte omfattas av de inbyggda princip definitionerna. Mer information finns i [Azure policy-dokumentationen](../governance/policy/overview.md) .

## <a name="assign-a-built-in-policy-definition"></a>Tilldela en inbyggd princip definition

Princip definitioner beskriver kraven på resursanvändningen och vilken påverkan som ska vidtas om ett villkor är uppfyllt. Princip _tilldelningar_ skapas från princip _definitioner_. Du kan använda inbyggda eller anpassade princip definitioner för dina Azure Cosmos DB-resurser. Princip tilldelningar är begränsade till en hanterings grupp för Azure, en Azure-prenumeration eller en resurs grupp och de tillämpas på resurserna i det valda omfånget. Alternativt kan du undanta vissa resurser från omfånget.

Du kan skapa princip tilldelningar med [Azure Portal](../governance/policy/assign-policy-portal.md)-, [Azure PowerShell](../governance/policy/assign-policy-powershell.md)-, [Azure CLI](../governance/policy/assign-policy-azurecli.md)-eller [arm-mall](../governance/policy/assign-policy-template.md).

Om du vill skapa en princip tilldelning från en inbyggd princip definition för Azure Cosmos DB använder du stegen i [skapa en princip tilldelning med Azure Portal](../governance/policy/assign-policy-portal.md) artikeln.

I steget för att välja en princip definition anger `Cosmos DB` du i Sök fältet för att filtrera listan över tillgängliga inbyggda princip definitioner. Välj en av de tillgängliga inbyggda princip definitionerna och välj sedan **Välj** för att fortsätta skapa princip tilldelningen.

> [!TIP]
> Du kan också använda de inbyggda princip definitions namnen som visas i fönstret **tillgängliga definitioner** med Azure PowerShell-, Azure CLI-eller arm-mallar för att skapa princip tilldelningar.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Sök efter Azure Cosmos DB inbyggda princip definitioner":::

## <a name="create-a-custom-policy-definition"></a>Skapa en anpassad principdefinition

För vissa scenarier som inte omfattas av inbyggda principer kan du skapa [en anpassad princip definition](../governance/policy/tutorials/create-custom-policy-definition.md). Senare skapar du en princip _tilldelning_ från din anpassade princip _definition_.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Egenskaps typer och egenskaps-alias i princip regler

Använd [definitions stegen för anpassad princip](../governance/policy/tutorials/create-custom-policy-definition.md) för att identifiera resurs egenskaper och egenskaps-alias, vilket krävs för att skapa princip regler.

Om du vill identifiera Azure Cosmos DB ett alias för en speciell egenskap använder du namn området `Microsoft.DocumentDB` med en av de metoder som visas i artikeln anpassad princip definition steg.

#### <a name="use-the-azure-cli"></a>Använd Azure CLI:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Använd Azure PowerShell:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

De här kommandona visar listan över namn på egenskaps Ali Aset för Azure Cosmos DB egenskapen. Följande är ett utdrag från utdata:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Du kan använda något av dessa namn för egenskaps Ali Aset i [definitions reglerna för anpassade principer](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Följande är en exempel princip definition som kontrollerar om ett Azure Cosmos DB konto har kon figurer ATS för flera Skriv platser. Definitionen av den anpassade principen innehåller två regler: en för att kontrol lera den speciella typen av egenskaps Ali Aset och den andra för typens egenskap, i det här fallet fältet som lagrar inställningen för flera Skriv platser. Båda reglerna använder namnen på alias.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

Anpassade princip definitioner kan användas för att skapa princip tilldelningar precis som de inbyggda princip definitionerna används.

## <a name="policy-compliance"></a>Principefterlevnad

När princip tilldelningarna har skapats, utvärderar Azure Policy resurserna i tilldelningens omfång. Varje resurs _efterlevnad_ av principen utvärderas. Den _effekt_ som anges i principen tillämpas sedan på icke-kompatibla resurser.

Du kan granska resultatet och reparations informationen i [Azure Portal](../governance/policy/how-to/get-compliance-data.md#portal) eller via [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) eller [Azure Monitor loggarna](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

På följande skärm bild visas två exempel på princip tilldelningar.

En tilldelning baseras på en inbyggd princip definition som kontrollerar att Azure Cosmos DB-resurser endast distribueras till de tillåtna Azure-regionerna. Resource Compliance visar resultatet av princip utvärderingen (kompatibel eller icke-kompatibel) för resurser i omfång.

Den andra tilldelningen baseras på en anpassad princip definition. Den här tilldelningen kontrollerar att Cosmos DB-konton har kon figurer ATS för flera Skriv platser.

När princip tilldelningarna har distribuerats visas utvärderings resultaten i instrument panelen för efterlevnad. Observera att det kan ta upp till 30 minuter innan du distribuerar en princip tilldelning. Dessutom [kan genomsökningar av princip utvärderingen startas på begäran](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) omedelbart efter att du har skapat princip tilldelningarna.

Skärm bilden visar följande utvärderings resultat för omfångs Azure Cosmos DB-konton:

- Noll av två konton är kompatibla med en princip som Virtual Network (VNet)-filtrering måste konfigureras.
- Noll av två konton är kompatibla med en princip som kräver att kontot konfigureras för flera Skriv platser
- Noll av två konton är kompatibla med en princip som resurserna distribuerades till tillåtna Azure-regioner.

:::image type="content" source="./media/policy/compliance.png" alt-text="Resultat för Azure Policy tilldelningar visas":::

Information om hur du reparerar icke-kompatibla resurser finns i [så här åtgärdar du resurser med Azure policy](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Nästa steg

- [Granska exempel anpassade princip definitioner för Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), inklusive för de principer för flera Skriv platser och VNET-filtrering som visas ovan.
- [Skapa en princip tilldelning i Azure Portal](../governance/policy/assign-policy-portal.md)
- [Granska Azure Policy inbyggda princip definitioner för Azure Cosmos DB](./policy-samples.md)
