---
title: Översikt över Azure Management – Azure-styrning
description: Översikt över hanteringsområdena för Azure-program och resurser med länkar till innehåll på Azure-hanteringsverktyg.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980908"
---
# <a name="overview-of-management-services-in-azure"></a>Översikt över hanterings tjänster i Azure

Styrning i Azure är en del av Azure Management. Den här artikeln beskriver de olika hanteringsområdena för att distribuera och underhålla dina resurser i Azure.

Hantering syftar på uppgifter och processer som krävs för att underhålla företagsprogram samt resurser som stöder dessa program. Azure har många tjänster och verktyg som arbetar tillsammans för att tillhandahålla fullständig hantering. Dessa tjänster är inte bara för resurser i Azure, utan även i andra moln och lokalt. Det första steget i utforma en komplett hanteringsmiljö är att förstå de olika verktygen och hur de fungerar tillsammans.

Följande diagram visar de olika hanteringsområdena som krävs för att underhålla alla program och resurser. Dessa olika områden kan betraktas som en livscykel. Varje område krävs i kontinuerlig följd under en resurs livslängd. Den här resursen livscykeln börjar med den första distributionen, fortsätter under drift, och slutligen när dras tillbaka.

![Hanterings ämnes områden i Azure](../monitoring/media/management-overview/management-capabilities.png)

Ingen enskild Azure-tjänst uppfyller kraven för ett visst område. I stället realiseras var och en av flera tjänster arbetar ihop. Vissa tjänster, till exempel Application Insights tillhandahåller riktade övervakningsfunktioner för webbprogram. Andra, som Azure Monitor loggar, lagrar hanterings data för andra tjänster. Den här funktionen kan du analysera data av olika typer som samlas in av olika tjänster.

Följande avsnitt beskriver i korta ordalag de olika hanteringsområdena och tillhandahåller länkar till detaljerat innehåll om de viktigaste Azure-tjänsterna.

## <a name="monitor"></a>Övervaka

Övervakning är syftar till insamling och analys av data för att granska prestanda, hälsotillstånd och tillgänglighet för dina resurser. En effektiv övervakningsstrategi hjälper dig att förstå hur komponenter och för att förbättra din drifttid med meddelanden. Läs en översikt över övervakning som täcker de olika tjänsterna som används vid [övervakning av Azure-program och resurser](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurera

Konfigurera refererar till den första distributionen och konfigurationen av resurser och löpande underhåll.
Automatisering av dessa uppgifter kan du eliminera redundans och minimera tiden och ansträngning och ökar din Precision och effektivitet. [Azure Automation](../automation/automation-intro.md) innehåller många tjänster för att automatisera konfigurationsåtgärder. Medan runbooks hantera Processautomatisering, kan konfiguration och hantering av uppdateringar hantera konfigurationer.

## <a name="govern"></a>Styrning

Styrning tillhandahåller mekanismer och processer för att behålla kontrollen över dina program och resurser i Azure. Det omfattar att planera initiativ och att fatta beslut om strategiska prioriteringar.
Styrning i Azure implementeras främst genom två tjänster. [Azure Policy](./policy/overview.md) kan du skapa, tilldela och hantera principdefinitioner för att tillämpa regler för dina resurser. Den här funktionen gör att dessa resurser i enlighet med företagets standarder. Med [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kan du spåra moln användning och utgifter för dina Azure-resurser och andra moln leverantörer.

## <a name="secure"></a>Säkert

Hantera säkerheten för dina resurser och data. Ett säkerhetsprogram innebär att bedöma hot, samla in och analysera data och kompatibilitet för program och resurser. Säkerhetsanalys för säkerhetsövervakning och hotanalys tillhandahålls av [Azure Security Center](../security-center/security-center-intro.md), som ger enhetlig säkerhetshantering och Avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Se [introduktion till Azure Security](../security/fundamentals/overview.md) omfattande information och vägledning om hur du skyddar Azure-resurser.

## <a name="protect"></a>Skydda

Skydd refererar till att hålla dina program och data som är tillgängliga även med avbrott som ligger utanför din kontroll. Skydd i Azure tillhandahålls av två tjänster. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) tillhandahåller säkerhetskopiering och återställning av data, antingen i molnet eller lokalt. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) tillhandahåller verksamhetskontinuitet och omedelbar återställning vid ett haveri.

## <a name="migrate"></a>Migrera

Migrering refererar till att överföra arbetsbelastningar som körs lokalt till Azure-molnet.
[Azure Migrate](../migrate/migrate-overview.md) är en tjänst som hjälper dig att bedöma lämpligheten för migrering av lokala virtuella datorer till Azure. Azure Site Recovery migrerar virtuella datorer [från den lokala](../site-recovery/migrate-tutorial-on-premises-azure.md) eller [från Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) hjälper dig att migrera databaskällor till Azure-Dataplattformar.
