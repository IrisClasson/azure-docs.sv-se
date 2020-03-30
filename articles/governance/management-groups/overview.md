---
title: Organisera dina resurser med hanteringsgrupper - Azure-styrning
description: Läs om hanteringsgrupperna, hur behörigheterna fungerar och hur du använder dem.
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.date: 12/18/2019
ms.topic: overview
ms.openlocfilehash: 319f48d4d0f8ce8501fecb74282760340b597188
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240942"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Ordna resurser med hanteringsgrupper i Azure

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsvillkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har. Alla prenumerationer i en hanteringsgrupp måste ha förtroende för samma Azure Active Directory-klientorganisation.

Du kan till exempel tillämpa principer för en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa virtuella datorer (VM). Den här principen tillämpas för alla hanteringsgrupper, prenumerationer och resurser under hanteringsgruppen genom att endast tillåta att virtuella datorer skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarki för hanteringsgrupper och prenumerationer

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering. Följande diagram visar ett exempel på att skapa en hierarki för styrning med hjälp av hanteringsgrupper.

![Exempel på ett hierarkiträd för hanteringsgrupp](./media/tree.png)

Du kan till exempel skapa en hierarki som tillämpar en princip som begränsar VM-platser till regionen USA, västra i gruppen med namnet ”Produktion”. Den här principen ärver på alla EA-prenumerationer (Enterprise Agreement) som är underordnade den hanteringsgruppen och gäller för alla virtuella datorer under dessa prenumerationer. Den här säkerhetsprincipen kan inte ändras av resursen eller prenumerationsägaren, vilket leder till bättre styrning.

Ett annat scenario där du kan använda hanteringsgrupper är för att ge användaråtkomst till flera prenumerationer. Genom att flytta flera prenumerationer under hanteringsgruppen kan du skapa en tilldelning av [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) till den. Detta gör även att alla prenumerationer ärver åtkomsten.
Genom att tilldela till hanteringsgruppen kan du ge användarna åtkomst till allt de behöver istället för att skapa skript för RBAC för flera prenumerationer.

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper

- 10 000 hanteringsgrupper kan användas i en enskild katalog.
- Ett träd för hanteringsgruppen har stöd för upp till sex nivåer.
  - Den här gränsen omfattar inte rotnivån eller prenumerationsnivån.
- Varje hanteringsgrupp och prenumeration har endast stöd för ett överordnat element.
- Varje hanteringsgrupp kan ha många underordnade element.
- Alla prenumerationer och hanteringsgrupper ingår i en hierarki i varje katalog. Läs [Viktiga fakta om rothanteringsgruppen](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>En rothanteringsgrupp för varje katalog

Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas för rothanteringsgruppen.
Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. [Den globala administratören för Azure Active Directory måste först utöka sin behörighet](../../role-based-access-control/elevate-access-global-admin.md) till rollen Administratör för användaråtkomst för rotgruppen. När behörigheten har utökats kan administratören hantera hierarkin och tilldela önskad RBAC-roll till andra kataloganvändare eller grupper. Som administratör kan du utse ditt eget konto till ägare av rothanteringsgruppen.

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om rothanteringsgruppen

- Som standard är rothanteringsgruppens visningsnamn **Klientorganisationens rotgrupp**. ID:t är samma som ID:t för Azure Active Directory.
- Om du vill ändra visningsnamnet måste ditt konto ha tilldelats rollen Ägare eller Deltagare i rothanteringsgruppen. Se [Ändra namnet på en hanteringsgrupp](manage.md#change-the-name-of-a-management-group) för att uppdatera namnet på en hanteringsgrupp.
- Rothanteringsgruppen kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper.  
- Alla prenumerationer och hanteringsgrupper är underordnade rothanteringsgruppen i katalogen.
  - Alla resurser i katalogen är underordnade rothanteringsgruppen för global hantering.
  - Nya prenumerationer tilldelas som standard till rothanteringsgruppen när de skapas.
- Alla Azure-kunder kan se rothanteringsgruppen, men alla kunder får inte hantera rothanteringsgruppen.
  - Alla som har åtkomst till en prenumeration kan se kontexten för den aktuella prenumerationens placering i hierarkin.  
  - Det är inte någon som får åtkomst till rothanteringsgruppen som standard. Globala administratörer för Azure Active Directory är de enda användarna som kan ge sig själva åtkomst.  Efter att ha fått åtkomst till rothanteringsgruppen kan de globala administratörerna tilldela önskade RBAC-roller till andra användare för att hantera den.  

> [!IMPORTANT]
> Alla tilldelningar av användaråtkomst eller principtilldelning för rothanteringsgruppen **gäller för alla resurser inom katalogen**.
> Alla kunder bör därför utvärdera behovet av objekt som definierats för det här området.
> Användaråtkomst och principtilldelningar bör endast vara ”måsten” i den här omfattningen.  

## <a name="initial-setup-of-management-groups"></a>Initial konfiguration av hanteringsgrupper

När användarna börjar använda hanteringsgrupper måste de genomföra en initial konfigurationsprocess. Det första steget är att rothanteringsgruppen skapas i katalogen. När den här gruppen har skapats blir alla befintliga prenumerationer i katalogen underordnade rothanteringsgruppen. Den här processen är till för att kontrollera att det endast finns en hanteringsgrupphierarki i en katalog. Hierarkin i katalogen gör det möjligt för administrativa kunder att använda global åtkomst och principer som andra kunder i katalogen inte kan kringgå. Allt som tilldelas på roten gäller för hela hierarkin, vilket omfattar alla hanteringsgrupper, prenumerationer, resursgrupper och resurser i Azure Active Directory-klientorganisationen.

## <a name="trouble-seeing-all-subscriptions"></a>Problem med att visa alla prenumerationer

Vissa kataloger som började använda hanteringsgrupper tidigt under förhandsgranskningen, innan den 25 juni 2018, märkte av ett problem där alla prenumerationer inte fanns i hierarkin. Processen för att lägga till prenumerationer i hierarkin implementerades efter det att en roll- eller principtilldelning utfördes på katalogens rothanteringsgrupp. 

### <a name="how-to-resolve-the-issue"></a>Så här löser du problemet

Det finns två alternativ som du kan använda för att lösa problemet.

1. Ta bort alla roll- och principtilldelningar från rothanteringsgruppen
   1. När du tar bort princip- och rolltilldelningar från rothanteringsgruppen återfyller tjänsten alla prenumerationer till hierarkin nästa dagcykel.  Den här principen är till för att kontrollera att det inte har getts någon oavsiktlig åtkomst eller principtilldelning till alla prenumerationer i klientorganisationen.
   1. Det bästa sättet att genomföra processen utan att påverka tjänsterna är att tilldela roll- eller principtilldelningar på en nivå lägre än rothanteringsgruppen. Sedan kan du ta bort alla tilldelningarna från rotomfånget.
1. Direktanropa API:t och påbörja återfyllningsprocessen
   1. Alla kunder i katalogen kan anropa API:erna *TenantBackfillStatusRequest* eller *StartTenantBackfillRequest*. När API:n StartTenantBackfillRequest anropas påbörjas den ursprungliga konfigurationsprocessen och alla prenumerationer flyttas till hierarkin. Processen gör även att alla nya prenumerationer blir underordnade rothanteringsgruppen. Den här processen kan utföras utan att ändra några tilldelningar på rotnivån. Genom att anropa API:et godkänner du att alla principer eller åtkomsttilldelningar på roten kan tillämpas på alla prenumerationer.

Om du har frågor om återfyllningsprocessen kan du kontakta: managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Åtkomst till hanteringsgrupp

Azure-hanteringsgrupper har stöd för [Azures rollbaserade åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) för alla resursåtkomster och rolldefinitioner.
Dessa behörigheter ärvs av underordnade resurser som finns i hierarkin. Alla RBAC-roller kan tilldelas en hanteringsgrupp som ärver ned hierarkin till resurserna.
RBAC-rollen VM-deltagare kan till exempel tilldelas till en hanteringsgrupp. Rollen har ingen åtgärd för hanteringsgruppen, men ärver av alla virtuella datorer under hanteringsgruppen.

Följande diagram visar listan över roller och åtgärder som stöds för hanteringsgrupper.

| RBAC-rollnamn             | Skapa | Byt namn | Flytta** | Ta bort | Tilldela åtkomst | Tilldela princip | Läsa  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Ägare                       | X      | X      | X      | X      | X             | X             | X     |
|Deltagare                 | X      | X      | X      | X      |               |               | X     |
|MG-deltagare*             | X      | X      | X      | X      |               |               | X     |
|Läsare                      |        |        |        |        |               |               | X     |
|MG-läsare*                  |        |        |        |        |               |               | X     |
|Deltagare för resursprincip |        |        |        |        |               | X             |       |
|Administratör för användaråtkomst   |        |        |        |        | X             | X             |       |

*: MG-deltagare och MG-läsare tillåter endast användare att utföra dessa åtgärder inom hanteringsgruppsomfånget.  
**: Rolltilldelningar i rothanteringsgruppen behöver inte flytta en prenumeration eller hanteringsgrupp till och från den.  Läs [Hantera dina resurser med hanteringsgrupper](manage.md) för mer information om att flytta objekt inom hierarkin.

## <a name="custom-rbac-role-definition-and-assignment"></a>Anpassad DEFINITION och tilldelning av RBAC-roll

Anpassat RBAC-rollstöd för hanteringsgrupper är för närvarande i förhandsversion med vissa [begränsningar](#limitations).  Du kan definiera hanteringsgruppers omfattning i rolldefinitionens tilldelningsbara omfattning.  Den anpassade RBAC-rollen är sedan tillgänglig för tilldelning för den hanteringsgruppen och alla hanteringsgrupper, prenumerationer, resursgrupper och resurser under den. Den här anpassade rollen ärvs nedåt i hierarkin precis som en inbyggd roll.    

### <a name="example-definition"></a>Exempeldefinition
[Definiera och skapa en anpassad roll](../../role-based-access-control/custom-roles.md) ändras inte med inkludering av hanteringsgrupper. Använd den fullständiga sökvägen för att definiera hanteringsgruppen **/providers/Microsoft.Management/managementgroups/{groupId}**. 

Använd hanteringsgruppens ID och inte hanteringsgruppens visningsnamn. Det här vanliga felet inträffar eftersom båda är anpassade definierade fält när de skapar en hanteringsgrupp. 

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problem med att bryta rolldefinitionen och tilldelningshierarkisökvägen
Rolldefinitioner kan tilldelas omfång var som helst i hanteringsgruppshierarkin. En rolldefinition kan definieras i en överordnad hanteringsgrupp medan den faktiska rolltilldelningen finns på den underordnade prenumerationen. Eftersom det finns en relation mellan de två objekten visas ett felmeddelande när du försöker separera tilldelningen från dess definition. 

Till exempel: Låt oss titta på en liten del av en hierarki för ett visuellt objekt. 

![underträd](./media/subtree.png)

Anta att det finns en anpassad roll definierad i marknadsföringshanteringsgruppen. Den anpassade rollen tilldelas sedan på de två kostnadsfria utvärderingsprenumerationerna.  

Om vi försöker flytta en av dessa prenumerationer till att vara underordnad produktionshanteringsgruppen, bryter det här steget vägen från prenumerationsrolltilldelning till rolldefinitionen för marknadsföringshanteringsgrupp. I det här fallet får du ett felmeddelande om att flytten inte är tillåten eftersom det kommer att bryta den här relationen.  

Det finns ett par olika alternativ för att åtgärda det här scenariot:
- Ta bort rolltilldelningen från prenumerationen innan prenumerationen flyttas till en ny överordnad MG.
- Lägg till prenumerationen i rolldefinitionens assignable-scope.
- Ändra det assignable omfånget inom rolldefinitionen. I exemplet ovan kan du uppdatera de assignable scopesna från marknadsföring till rothanteringsgrupp så att definitionen kan nås av båda grenarna i hierarkin.   
- Skapa ytterligare en anpassad roll som ska definieras i den andra grenen.  Den här nya rollen kräver att rolltilldelningen ändras även på prenumerationen.  

### <a name="limitations"></a>Begränsningar  
Det finns begränsningar som finns när du använder anpassade roller i hanteringsgrupper. 

 - Du kan bara definiera en hanteringsgrupp i de assignable omfången för en ny roll.  Den här begränsningen finns för att minska antalet situationer där rolldefinitioner och rolltilldelningar kopplas från.  Detta händer när en prenumeration eller hanteringsgrupp med en rolltilldelning flyttas till en annan förälder som inte har rolldefinitionen.   
 - RBAC Data Plane-åtgärder tillåts inte definieras i anpassade roller i hanteringsgruppen.  Den här begränsningen finns på plats eftersom det finns ett svarstidsproblem med RBAC-åtgärder som uppdaterar dataplanresursleverantörerna. Det här latensproblemet bearbetas och dessa åtgärder inaktiveras från rolldefinitionen för att minska eventuella risker.
 - Azure Resource Manager validerar inte hanteringsgruppens existens i rolldefinitionens assignable scope.  Om det finns ett stavfel eller ett felaktigt hanteringsgrupp-ID i listan skapas rolldefinitionen fortfarande.   

## <a name="moving-management-groups-and-subscriptions"></a>Flytta hanteringsgrupper och prenumerationer 

För att en hanteringsgrupp eller prenumeration ska vara underordnad en annan hanteringsgrupp måste tre regler utvärderas som sanna.

Om du gör flytten behöver du: 

-  Skrivbehörighet för hanteringsgrupp och skrivbehörighet för rolltilldelning för den underordnade prenumerationen eller hanteringsgruppen.
   - Det inbyggda rollexempeln **Ägare**
- Skrivbehörighet för ledningsgruppen för ledningsgruppen för hantering av ledningsgruppen för ledningsgruppen.
   - Inbyggt rollexempel: **Ägare**, **Deltagare**, **Management Group Contributor**
- Skrivbehörighet för hanteringsgrupper i den befintliga överordnade hanteringsgruppen.
   - Inbyggt rollexempel: **Ägare**, **Deltagare**, **Management Group Contributor**

**Undantag**: Om målet eller den befintliga överordnade hanteringsgruppen är rothanteringsgruppen gäller inte behörighetskraven. Eftersom rothanteringsgruppen är standardlandningsplatsen för alla nya hanteringsgrupper och prenumerationer behöver du inte behörigheter för att flytta ett objekt.

Om ägarrollen i prenumerationen ärvs från den aktuella hanteringsgruppen är dina flyttmål begränsade. Du kan bara flytta prenumerationen till en annan hanteringsgrupp där du har rollen Ägare. Du kan inte flytta den till en hanteringsgrupp där du är en deltagare eftersom du skulle förlora äganderätten till prenumerationen. Om du är direkt tilldelad ägarrollen för prenumerationen (inte ärvd från hanteringsgruppen) kan du flytta den till en hanteringsgrupp där du är deltagare. 

## <a name="audit-management-groups-using-activity-logs"></a>Granska hanteringsgrupper med hjälp av aktivitetsloggar

Hanteringsgrupper kan användas i [Azure-aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md). Du kan söka efter alla händelser i en hanteringsgrupp från samma centrala plats som andra Azure-resurser.  Du kan till exempel se alla ändringar för rolltilldelningar eller principtilldelningar som gjorts i en viss hanteringsgrupp.

![Aktivitetsloggar med hanteringsgrupper](media/al-mg.png)

När du vill fråga hanteringsgrupper utanför Microsoft Azure-portalen är målområdet för hanteringsgrupper: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](manage.md)
- [Granska hanteringsgrupper i Azure PowerShell-resursmodulen](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST API](/rest/api/resources/managementgroups)
- [Granska hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)
