---
title: Azure Policy säkerhets bas linje för Azures säkerhets prestanda
description: Azure Policy säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7871099e3e05613bec29a4bfc0824dd9647431e7
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937546"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Azure Policy säkerhets bas linje för Azures säkerhets prestanda

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark](../../../security/benchmarks/overview.md) till Azure policy. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter **domänerna för regelefterlevnad** och **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure policy. **Kontroller** som inte är tillämpliga på Azure policy har uteslutits. Om du vill se hur Azure Policy helt mappar till Azures säkerhets mätning, se den [fullständiga Azure policy mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

En mappning av Azures säkerhets benchmark-kontroller till inbyggda princip definitioner via det inbyggda initiativet finns i [regler för efterlevnad: Azure Security benchmark](../samples/azure-security-benchmark.md).

Azure Policy använder _ägarskapet_ på ansvar i stället för _ansvar_. Mer information om _ägarskap_finns i [Azure policy princip definitioner](./definition-structure.md#type) och [delat ansvar i molnet](../../../security/fundamentals/shared-responsibility.md).


## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure policy använder aktivitets loggar som aktive ras automatiskt för att inkludera händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

* [Samla in plattforms loggar och mått med Azure Monitor](../../../azure-monitor/platform/diagnostic-settings.md)

* [Förstå loggning och olika logg typer i Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton. 

Du kan också aktivera en just-in-Time/bara-tillräcklig-åtkomst-lösning med hjälp av [Azure AD Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) privilegierade roller eller [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Så här aktiverar du MFA i Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd Azure Active Directory rollbaserad åtkomst kontroll (RBAC) för att kontrol lera åtkomsten till Azure policy.

* [RBAC-behörigheter i Azure Policy](../overview.md#rbac-permissions-in-azure-policy)

* [Konfigurera RBAC i Azure](../../../role-based-access-control/role-assignments-portal.md)


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med aktivitets loggar för att skapa aviseringar för när ändringar sker i Azure policy.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](../../../azure-monitor/platform/alerts-activity-log.md)


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi. Använd Azure Policy _ändra_ -effekter för att rapportera om och genomdriva efterlevnad och konsekvent etikett styrning.

* [Självstudie: skapa och hantera principer](../tutorials/create-and-manage.md)

* [Självstudie: hantera märkes styrning](../tutorials/govern-tags.md)


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända princip definitioner och princip tilldelningar enligt organisationens behov.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

* [Så här konfigurerar och hanterar du Azure Policy](../tutorials/create-and-manage.md)


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../../../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../../../security/benchmarks/security-baselines-overview.md)
