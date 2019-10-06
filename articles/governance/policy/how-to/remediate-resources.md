---
title: Åtgärda icke-kompatibla resurser
description: Den här guiden vägleder dig genom reparationen av resurser som inte är kompatibla med principer i Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 219a3c56f9e4e4c9e132fa759b017fac63ade766
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977980"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Åtgärda icke-kompatibla resurser med Azure Policy

Resurser som inte är kompatibla med en **deployIfNotExists** eller en **ändrings** princip kan försättas i ett kompatibelt tillstånd genom **reparation**. Reparationen utförs genom att instruera Azure Policy att köra **deployIfNotExists** -eller tag- **åtgärderna** för den tilldelade principen på dina befintliga resurser. Den här artikeln visar de steg som krävs för att förstå och utföra reparation med Azure Policy.

## <a name="how-remediation-security-works"></a>Hur fungerar säkerheten för reparation

När Azure Policy kör mallen i **deployIfNotExists** -princip definitionen används en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy skapar en hanterad identitet för varje tilldelning, men du måste ha information om vilka roller som ska bevilja den hanterade identiteten. Om den hanterade identitet saknas roller, visas det här felet under tilldelningen av principen eller ett initiativ. När du använder portalen beviljas Azure Policy automatiskt den hanterade identitet som listade roller när tilldelningen startas.

![Hanterad identitet - saknas roll](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Om en resurs som har ändrats av **deployIfNotExists** eller **ändrar** utanför omfånget för princip tilldelningen eller om mallen har åtkomst till egenskaper för resurser utanför omfånget för princip tilldelningen, måste tilldelningens hanterade identitet vara [ manuellt beviljad åtkomst](#manually-configure-the-managed-identity) eller reparations distributionen Miss kan.

## <a name="configure-policy-definition"></a>Konfigurera principdefinition

Det första steget är att definiera de roller som **deployIfNotExists** och **ändra** behöver i princip definitionen för att kunna distribuera innehållet i den inkluderade mallen. Under den **information** egenskapen, lägga till en **roleDefinitionIds** egenskapen. Den här egenskapen är en matris med strängar som matchar roller i din miljö. Ett fullständigt exempel finns i DeployIfNotExists- [exemplet](../concepts/effects.md#deployifnotexists-example) eller i [ändra exempel](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Egenskapen **roleDefinitionIds** använder det fullständiga resurs-ID: n och tar inte med den korta **roleName** för rollen. Använd följande kod för att hämta ID för rollen ”Bidragsgivar” i din miljö:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Konfigurera manuellt hanterad identitet

När du skapar en tilldelning med hjälp av portalen genererar Azure Policy båda den hanterade identiteten och ger den roller som definierats i **roleDefinitionIds**. Steg för att skapa den hanterade identitet och tilldela den behörigheter måste göras manuellt under följande förhållanden:

- När du använder SDK: N (till exempel Azure PowerShell)
- När en resurs utanför tilldelningsomfånget ändras av mallen
- När en resurs utanför tilldelningsomfånget läses av mallen

> [!NOTE]
> Azure PowerShell och .NET är de enda SDK: er som för närvarande stöd för den här funktionen.

### <a name="create-managed-identity-with-powershell"></a>Skapa hanterad identitet med PowerShell

Skapa en hanterad identitet vid tilldelningen av principen, **plats** måste definieras och **AssignIdentity** används. I följande exempel hämtas definitionen av den inbyggda principen **distribuera SQL DB transparent datakryptering**anger målresursgruppen och skapar sedan tilldelningen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Den `$assignment` variabeln innehåller nu ägar-ID för den hanterade identitet tillsammans med de standardvärden som returneras när du skapar en principtilldelning. Den kan nås via `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Bevilja definierat roller med PowerShell

Den nya hantera identiteten måste slutföra replikering via Azure Active Directory innan den kan beviljas de nödvändiga rollerna. När replikeringen är klar itererar följande exempel princip definitionen i `$policyDef` för **roleDefinitionIds** och använder [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) för att ge rollerna den nya hanterade identiteten.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Bevilja definierat roller via portalen

Det finns två sätt att ge en tilldelning hanterad identitet definierade roller med hjälp av portalen med hjälp av **åtkomstkontroll (IAM)** eller genom att redigera tilldelningen princip eller ett initiativ och klicka på **spara**.

Följ dessa steg om du vill lägga till en roll i tilldelningens hanterad identitet:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. Välj **Tilldelningar** till vänster på sidan Azure Policy.

1. Leta upp den tilldelningen som har en hanterad identitet och klicka på namnet.

1. Hitta den **tilldelnings-ID** egenskap på sidan Redigera. Tilldelnings-ID blir något som liknar:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Namnet på den hanterade identitet är den sista delen av tilldelning av resurs-ID, vilket är `2802056bfc094dfb95d4d7a5` i det här exemplet. Kopiera den här delen av tilldelning av resurs-ID.

1. Gå till resursen eller resurser överordnade behållaren (resursgrupp, prenumeration, hanteringsgrupp) som behöver rolldefinitionen la till manuellt.

1. Klicka på den **åtkomstkontroll (IAM)** länka på resurssidan och klicka på **+ Lägg till rolltilldelning** överst på sidan för kontroll av åtkomst.

1. Välj rätt roll som matchar en **roleDefinitionIds** från principdefinitionen.
   Lämna **tilldela åtkomst till** inställt på standardvärdet ”Azure AD användaren, gruppen eller programmet”. I den **Välj** rutan, klistra in eller Skriv delen av tilldelning av resurs-ID finns tidigare. När sökningen är klar klickar du på objektet med samma namn och välj ID och klicka på **spara**.

## <a name="create-a-remediation-task"></a>Skapa en uppgift för reparation

### <a name="create-a-remediation-task-through-portal"></a>Skapa en reparations uppgift via portalen

Under utvärderingen bestämmer princip tilldelningen med **deployIfNotExists** eller **ändra** effekter om det finns icke-kompatibla resurser. När icke-kompatibla resurser finns informationen tillhandahålls på den **reparation** sidan. Tillsammans med i listan över principer som har icke-kompatibla resurser är alternativet för att utlösa en **reparation uppgift**. Det här alternativet är vad som skapar en distribution från **deployIfNotExists** -mallen eller **ändra** -åtgärder.

Skapa en **reparation uppgiften**, Följ dessa steg:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter princip i alla tjänster](../media/remediate-resources/search-policy.png)

1. Välj **reparation** till vänster på sidan för Azure Policy.

   ![Välj reparation på princip Sidan](../media/remediate-resources/select-remediation.png)

1. Alla **deployIfNotExists** och **ändra** princip tilldelningar med icke-kompatibla resurser ingår i **principerna för att åtgärda** fliken och data tabellen. Klicka på en princip med resurser som är icke-kompatibla. Den **ny reparation uppgift** öppnas.

   > [!NOTE]
   > Ett annat sätt att öppna den **reparation uppgift** är att hitta och klicka på principen från den **efterlevnad** sidan och klicka sedan på den **skapa reparation uppgift** knappen.

1. På den **ny reparation uppgift** sidan, filtrera resurser för att åtgärda problemet med hjälp av den **omfång** ellipserna att välja underordnade resurser från där principen tilldelas (inklusive till enskild resurs objekt). Dessutom kan använda den **platser** listrutan filtreras ytterligare resurser. Endast de resurser som anges i tabellen ska åtgärdas.

   ![Åtgärda – Välj vilka resurser som ska åtgärdas](../media/remediate-resources/select-resources.png)

1. Starta reparation aktiviteten när resurserna som har filtrerats genom att klicka på **reparera**. Öppnar sidan principer för efterlevnad för den **reparation uppgifter** flik för att visa tillståndet för uppgifter förloppet.

   ![Åtgärds förlopp för reparations åtgärder](../media/remediate-resources/task-progress.png)

1. Klicka på den **reparation uppgift** från sidan princip för efterlevnad för att få information om förloppet. Filtrering som används för aktiviteten visas tillsammans med en lista över de resurser som åtgärdas.

1. Från den **reparation uppgift** högerklickar du på en resurs för att visa antingen reparation aktivitetens distribution eller resursen. I slutet av raden klickar du på **relaterade händelser** du vill ha information, till exempel ett felmeddelande.

   ![Åtgärda - resurs snabbmenyn för uppgift](../media/remediate-resources/resource-task-context-menu.png)

Resurser som distribueras via en **reparation uppgift** läggs till i **distribuerade resurser** fliken på policysidan för efterlevnad.

### <a name="create-a-remediation-task-through-azure-cli"></a>Skapa en reparations uppgift via Azure CLI

Om du vill skapa en **reparations uppgift** med Azure CLI använder du `az policy remediation`-kommandon. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med ditt **deployIfNotExists** eller **ändra** princip tilldelnings-ID.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparations kommandon och exempel finns i kommandona för att [Reparera AZ-principer](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Skapa en reparations uppgift via Azure PowerShell

Om du vill skapa en **reparations aktivitet** med Azure PowerShell använder du `Start-AzPolicyRemediation`-kommandona. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med ditt **deployIfNotExists** eller **ändra** princip tilldelnings-ID.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparations-cmdletar och exempel finns i modulen [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](getting-compliance-data.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).