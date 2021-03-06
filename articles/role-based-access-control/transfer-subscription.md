---
title: Överföra en Azure-prenumeration till en annan Azure AD-katalog (för hands version)
description: Lär dig hur du överför en Azure-prenumeration och kända relaterade resurser till en annan Azure Active Directory (Azure AD)-katalog.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 07/01/2020
ms.author: rolyon
ms.openlocfilehash: 664687d096a3a9c6ce9a6c7de0025604e046b0a1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029985"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory-preview"></a>Överföra en Azure-prenumeration till en annan Azure AD-katalog (för hands version)

> [!IMPORTANT]
> Följande steg för att överföra en prenumeration till en annan Azure AD-katalog finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Organisationer kan ha flera Azure-prenumerationer. Varje prenumeration är associerad med en viss Azure Active Directory-katalog (Azure AD). För att förenkla hanteringen kan du vilja överföra en prenumeration till en annan Azure AD-katalog. När du överför en prenumeration till en annan Azure AD-katalog överförs inte vissa resurser till mål katalogen. Till exempel tas alla roll tilldelningar och anpassade roller i Azure rollbaserad åtkomst kontroll (Azure RBAC) bort **permanent** från käll katalogen och överförs inte till mål katalogen.

I den här artikeln beskrivs de grundläggande steg som du kan följa för att överföra en prenumeration till en annan Azure AD-katalog och återskapa några av resurserna efter överföringen.

## <a name="overview"></a>Översikt

Överföring av en Azure-prenumeration till en annan Azure AD-katalog är en komplex process som måste planeras noggrant och köras. Många Azure-tjänster kräver säkerhets objekt (identiteter) för att kunna användas normalt eller till och med hantera andra Azure-resurser. Den här artikeln försöker omfatta de flesta Azure-tjänster som är beroende av säkerhets objekt, men är inte omfattande.

> [!IMPORTANT]
> Att överföra en prenumeration kräver stillestånds tid för att slutföra processen.

Följande diagram visar de grundläggande steg som du måste följa när du överför en prenumeration till en annan katalog.

1. Förbered för överföring

1. Överföra faktureringsägarskap för en Azure-prenumeration till ett annat konto

1. Återskapa resurser i mål katalogen, till exempel roll tilldelningar, anpassade roller och hanterade identiteter

    ![Överför prenumerations diagram](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Bestämma om en prenumeration ska överföras till en annan katalog

Här följer några orsaker till varför du kanske vill överföra en prenumeration:

- På grund av ett företags sammanslagning eller förvärv, vill du hantera en förvärvad prenumeration i din primära Azure AD-katalog.
- Någon i din organisation har skapat en prenumeration och vill konsolidera hanteringen till en viss Azure AD-katalog.
- Du har program som är beroende av ett visst prenumerations-ID eller URL och det är inte enkelt att ändra program konfigurationen eller koden.
- En del av din verksamhet har delats i ett separat företag och du måste flytta några av dina resurser till en annan Azure AD-katalog.
- Du vill hantera vissa resurser i en annan Azure AD-katalog för säkerhets isolering.

Att överföra en prenumeration kräver stillestånds tid för att slutföra processen. Beroende på ditt scenario kan det vara bättre att bara återskapa resurserna och kopiera data till mål katalogen och prenumerationen.

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Förstå effekten av överföring av en prenumeration

Flera Azure-resurser är beroende av en prenumeration eller en katalog. Beroende på din situation visar följande tabell de kända effekterna av överföring av en prenumeration. Genom att utföra stegen i den här artikeln kan du återskapa några av de resurser som fanns före prenumerations överföringen.

> [!IMPORTANT]
> I det här avsnittet visas de kända Azure-tjänster eller-resurser som är beroende av din prenumeration. Eftersom resurs typer i Azure ständigt håller på att utvecklas kan det finnas ytterligare beroenden som inte visas här, vilket kan orsaka en större ändring i din miljö. 

| Tjänst eller resurs | Påverkas | Återställnings bara | Påverkas du? | Det här kan du göra |
| --------- | --------- | --------- | --------- | --------- |
| Rolltilldelningar | Ja | Yes | [Visa lista över rolltilldelningar](#save-all-role-assignments) | Alla roll tilldelningar tas bort permanent. Du måste mappa användare, grupper och tjänstens huvud namn till motsvarande objekt i mål katalogen. Du måste återskapa roll tilldelningarna. |
| Anpassade roller | Ja | Yes | [Lista anpassade roller](#save-custom-roles) | Alla anpassade roller tas bort permanent. Du måste återskapa de anpassade rollerna och eventuella roll tilldelningar. |
| Systemtilldelade hanterade identiteter | Ja | Yes | [Visa lista över hanterade identiteter](#list-role-assignments-for-managed-identities) | Du måste inaktivera och återaktivera hanterade identiteter. Du måste återskapa roll tilldelningarna. |
| Användare som tilldelats hanterade identiteter | Ja | Yes | [Visa lista över hanterade identiteter](#list-role-assignments-for-managed-identities) | Du måste ta bort, återskapa och bifoga de hanterade identiteterna till lämplig resurs. Du måste återskapa roll tilldelningarna. |
| Azure Key Vault | Ja | Yes | [Visa lista Key Vault åtkomst principer](#list-other-known-resources) | Du måste uppdatera klient-ID: t som är associerat med nyckel valvena. Du måste ta bort och lägga till nya åtkomst principer. |
| Azure SQL-databaser med Azure AD-autentisering | Yes | Inga | [Kontrol lera Azure SQL-databaser med Azure AD-autentisering](#list-other-known-resources) |  |  |
| Azure Storage och Azure Data Lake Storage Gen2 | Ja | Yes |  | Du måste återskapa alla ACL: er. |
| Azure Data Lake Storage Gen1 | Ja |  |  | Du måste återskapa alla ACL: er. |
| Azure Files | Ja | Yes |  | Du måste återskapa alla ACL: er. |
| Azure File Sync | Ja | Yes |  |  |
| Azure Managed Disks | Yes | Ej tillämpligt |  |  |
| Azure Container Services för Kubernetes | Ja | Yes |  |  |
| Azure Active Directory Domain Services | Yes | Inga |  |  |
| Appregistreringar | Ja | Ja |  |  |

Om du använder kryptering i vila för en resurs, till exempel ett lagrings konto eller en SQL-databas, som har ett beroende av ett nyckel valv som inte finns i samma prenumeration som överförs, kan det leda till ett oåterkalleligt scenario. Om du har den här situationen bör du vidta åtgärder för att använda ett annat nyckel valv eller tillfälligt inaktivera Kundhanterade nycklar för att undvika det här oåterkalleliga scenariot.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

- [Bash i Azure Cloud Shell](/azure/cloud-shell/overview) eller [Azure CLI](https://docs.microsoft.com/cli/azure)
- Konto administratör för den prenumeration som du vill överföra i käll katalogen
- [Ägar](built-in-roles.md#owner) roll i mål katalogen

## <a name="step-1-prepare-for-the-transfer"></a>Steg 1: Förbered för överföring

### <a name="sign-in-to-source-directory"></a>Logga in på käll katalogen

1. Logga in på Azure som administratör.

1. Hämta en lista över dina prenumerationer med kommandot [AZ Account List](/cli/azure/account#az-account-list) .

    ```azurecli
    az account list --output table
    ```

1. Använd [AZ konto uppsättning](https://docs.microsoft.com/cli/azure/account#az-account-set) för att ange den aktiva prenumeration som du vill överföra.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>Installera resurs-Graph-tillägget

 Med tillägget resurs diagram kan du använda [diagram kommandot AZ](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) för att söka efter resurser som hanteras av Azure Resource Manager. Du kommer att använda det här kommandot i senare steg.

1. Använd [listan med AZ-tillägg](https://docs.microsoft.com/cli/azure/extension#az-extension-list) för att se om du har installerat *resurs diagram* tillägget.

    ```azurecli
    az extension list
    ```

1. Annars installerar du *resurs-Graph-* tillägget.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Spara alla roll tilldelningar

1. Använd [AZ roll tilldelnings lista](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-list) för att visa en lista över alla roll tilldelningar (inklusive ärvda roll tilldelningar).

    För att göra det enklare att granska listan kan du exportera utdata som JSON, TSV eller en tabell. Mer information finns i [lista roll tilldelningar med Azure RBAC och Azure CLI](role-assignments-list-cli.md).

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Spara listan med roll tilldelningar.

    När du överför en prenumeration tas alla roll tilldelningar bort **permanent** , så det är viktigt att spara en kopia.

1. Granska listan över roll tilldelningar. Det kan finnas roll tilldelningar som du inte behöver i mål katalogen.

### <a name="save-custom-roles"></a>Spara anpassade roller

1. Använd den [AZ roll definitions listan](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-list) för att visa en lista över dina anpassade roller. Mer information finns i [skapa eller uppdatera anpassade Azure-roller med hjälp av Azure CLI](custom-roles-cli.md).

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Spara varje anpassad roll som du behöver i mål katalogen som en separat JSON-fil.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Gör kopior av filerna för anpassade roller.

1. Ändra varje kopia om du vill använda följande format.

    Du kommer att använda de här filerna senare för att återskapa de anpassade rollerna i mål katalogen.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Ange mappningar för användare, grupp och tjänst huvud namn

1. Baserat på din lista över roll tilldelningar bestämmer du de användare, grupper och tjänstens huvud namn som du ska mappa till i mål katalogen.

    Du kan identifiera typen av huvud namn genom att titta på `principalType` egenskapen i varje roll tilldelning.

1. Om det behövs skapar du alla användare, grupper eller tjänst huvud namn du behöver i mål katalogen.

### <a name="list-role-assignments-for-managed-identities"></a>Lista roll tilldelningar för hanterade identiteter

Hanterade identiteter uppdateras inte när en prenumeration överförs till en annan katalog. Det innebär att alla befintliga systemtilldelade eller användarspecifika hanterade identiteter bryts. Efter överföringen kan du återaktivera alla systemtilldelade hanterade identiteter. För användare som tilldelats hanterade identiteter måste du skapa dem på nytt och bifoga dem i mål katalogen.

1. Granska [listan över Azure-tjänster som har stöd för hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) för att notera var du kan använda hanterade identiteter.

1. Använd [AZ AD SP-lista](/cli/azure/identity?view=azure-cli-latest#az-identity-list) för att visa en lista över tilldelade och användarspecifika hanterade identiteter.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. I listan över hanterade identiteter fastställer du vilka som är systemtilldelade och vilka som är tilldelade till användaren. Du kan använda följande kriterier för att fastställa typen.

    | Kriterie | Hanterad identitets typ |
    | --- | --- |
    | `alternativeNames`egenskapen innehåller`isExplicit=False` | Systemtilldelad |
    | `alternativeNames`Egenskapen omfattar inte`isExplicit` | Systemtilldelad |
    | `alternativeNames`egenskapen innehåller`isExplicit=True` | Tilldelade användare |

    Du kan också använda [AZ Identity List](https://docs.microsoft.com/cli/azure/identity#az-identity-list) för att bara lista användare tilldelade hanterade identiteter. Mer information finns i [skapa, lista eller ta bort en användardefinierad hanterad identitet med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md).

    ```azurecli
    az identity list
    ```

1. Hämta en lista över `objectId` värdena för dina hanterade identiteter.

1. Sök i listan över roll tilldelningar för att se om det finns några roll tilldelningar för dina hanterade identiteter.

### <a name="list-key-vaults"></a>Visa en lista över nyckel valv

När du skapar ett nyckel valv knyts det automatiskt till standard Azure Active Directory klient-ID: t för den prenumeration som den skapas i. Alla åtkomstprincipposter knyts också till detta klient-ID. Mer information finns i [flytta en Azure Key Vault till en annan prenumeration](../key-vault/general/move-subscription.md).

> [!WARNING]
> Om du använder kryptering i vila för en resurs, till exempel ett lagrings konto eller en SQL-databas, som har ett beroende av ett nyckel valv som inte finns i samma prenumeration som överförs, kan det leda till ett oåterkalleligt scenario. Om du har den här situationen bör du vidta åtgärder för att använda ett annat nyckel valv eller tillfälligt inaktivera Kundhanterade nycklar för att undvika det här oåterkalleliga scenariot.

- Om du har ett nyckel valv ska du använda [AZ Key Vault show](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-show) för att visa en lista över åtkomst principerna. Mer information finns i [tillhandahålla Key Vault autentisering med en princip för åtkomst kontroll](../key-vault/key-vault-group-permissions-for-apps.md).

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Visa en lista över Azure SQL-databaser med Azure AD-autentisering

- Använd [AZ SQL Server AD-admin List](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) och [AZ Graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) -tillägget för att se om du använder Azure SQL-databaser med Azure AD-autentisering. Mer information finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL](../sql-database/sql-database-aad-authentication-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Lista ACL: er

1. Om du använder Azure Data Lake Storage Gen1 listar du de ACL: er som tillämpas på alla filer med hjälp av Azure Portal eller PowerShell.

1. Om du använder Azure Data Lake Storage Gen2 listar du de ACL: er som tillämpas på alla filer med hjälp av Azure Portal eller PowerShell.

1. Om du använder Azure Files listar du de ACL: er som används för alla filer.

### <a name="list-other-known-resources"></a>Lista andra kända resurser

1. Använd [AZ konto show](https://docs.microsoft.com/cli/azure/account#az-account-show) för att hämta ditt PRENUMERATIONS-ID.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Använd [AZ Graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) -tillägget för att visa andra Azure-resurser med kända Azure AD Directory-beroenden.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-billing-ownership"></a>Steg 2: överför fakturerings ägarskap

I det här steget överför du fakturerings ägarskapet för prenumerationen från käll katalogen till mål katalogen.

> [!WARNING]
> När du överför prenumerationens fakturerings ägarskap tas alla roll tilldelningar i käll katalogen bort **permanent** och kan inte återställas. Du kan inte gå tillbaka när du har överfört fakturerings ägarskapet för prenumerationen. Se till att slutföra de föregående stegen innan du utför det här steget.

1. Följ stegen i [överföra fakturerings ägarskapet för en Azure-prenumeration till ett annat konto](../cost-management-billing/manage/billing-subscription-transfer.md). Om du vill överföra prenumerationen till en annan Azure AD-katalog måste du markera kryss rutan **prenumeration för Azure AD-klient** .

1. När du har överfört ägarskapet går du tillbaka till den här artikeln för att återskapa resurserna i mål katalogen.

## <a name="step-3-re-create-resources"></a>Steg 3: återskapa resurser

### <a name="sign-in-to-target-directory"></a>Logga in på mål katalogen

1. Logga in som den användare som godkände överföringsbegäran i mål katalogen.

    Endast användaren i det nya kontot som godkände överföringsbegäran får åtkomst till att hantera resurserna.

1. Hämta en lista över dina prenumerationer med kommandot [AZ Account List](https://docs.microsoft.com/cli/azure/account#az-account-list) .

    ```azurecli
    az account list --output table
    ```

1. Använd [AZ konto uppsättning](https://docs.microsoft.com/cli/azure/account#az-account-set) för att ange den aktiva prenumeration som du vill använda.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Skapa anpassade roller
        
- Använd [AZ roll definition Create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create) för att skapa varje anpassad roll från de filer som du skapade tidigare. Mer information finns i [skapa eller uppdatera anpassade Azure-roller med hjälp av Azure CLI](custom-roles-cli.md).

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>Skapa rolltilldelningar

- Använd [AZ roll tilldelning skapa](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) för att skapa roll tilldelningar för användare, grupper och tjänstens huvud namn. Mer information finns i [lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure CLI](role-assignments-cli.md).

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Uppdatera systemtilldelade hanterade identiteter

1. Inaktivera och återaktivera systemtilldelade hanterade identiteter.

    | Azure-tjänst | Mer information | 
    | --- | --- |
    | Virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Skalningsuppsättningar för virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Övriga tjänster | [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Använd [AZ roll tilldelning skapa](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) för att skapa roll tilldelningar för systemtilldelade hanterade identiteter. Mer information finns i [tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Uppdatera användare tilldelade hanterade identiteter

1. Ta bort, återskapa och bifoga användarspecifika hanterade identiteter.

    | Azure-tjänst | Mer information | 
    | --- | --- |
    | Virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Skalningsuppsättningar för virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Övriga tjänster | [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Använd [AZ roll tilldelning skapa](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) för att skapa roll tilldelningar för användarspecifika hanterade identiteter. Mer information finns i [tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Uppdatera nyckel valv

I det här avsnittet beskrivs de grundläggande stegen för att uppdatera nyckel valven. Mer information finns i [flytta en Azure Key Vault till en annan prenumeration](../key-vault/general/move-subscription.md).

1. Uppdatera klient-ID: t som är associerat med alla befintliga nyckel valv i prenumerationen till mål katalogen.

1. Ta bort alla åtkomstprincipposter.

1. Lägg till nya åtkomst princip poster som är associerade med mål katalogen.

### <a name="update-acls"></a>Uppdatera ACL: er

1. Om du använder Azure Data Lake Storage Gen1 tilldelar du lämpliga ACL: er. Mer information finns i [skydda data som lagras i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Om du använder Azure Data Lake Storage Gen2 tilldelar du lämpliga ACL: er. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

1. Om du använder Azure Files tilldelar du lämpliga ACL: er.

### <a name="review-other-security-methods"></a>Granska andra säkerhets metoder

Även om roll tilldelningar tas bort under överföringen kan användare i det ursprungliga ägar kontot fortsätta att ha åtkomst till prenumerationen via andra säkerhets metoder, inklusive:

- Åtkomstnycklar för tjänster som Storage.
- [Hanterings certifikat](../cloud-services/cloud-services-certs-create.md) som ger användare administratörs åtkomst till prenumerations resurser.
- Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.

Om avsikten är att ta bort åtkomst från användare i käll katalogen så att de inte har åtkomst till mål katalogen, bör du överväga att rotera eventuella autentiseringsuppgifter. Användarna fortsätter att ha åtkomst efter överföringen tills autentiseringsuppgifterna har uppdaterats.

1. Rotera åtkomst nycklar för lagrings kontot. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md).

1. Om du använder åtkomst nycklar för andra tjänster som Azure SQL Database eller Azure Service Bus meddelanden, rotera åtkomst nycklar.

1. För resurser som använder hemligheter öppnar du inställningarna för resursen och uppdaterar hemligheten.

1. Uppdatera certifikatet för resurser som använder certifikat.

## <a name="next-steps"></a>Nästa steg

- [Överföra faktureringsägarskap för en Azure-prenumeration till ett annat konto](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Associera eller lägga till en Azure-prenumeration till Azure Active Directory-klienten](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
