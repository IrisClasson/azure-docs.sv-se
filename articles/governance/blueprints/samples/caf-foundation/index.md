---
title: Exempel på CAF Foundation-skiss – översikt
description: Översikt över och arkitektur för Cloud Adoption Framework (CAF) for Azure Foundation-skissexemplet.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: b8bd0015d5f95fc61806197ca21fdaeeb3452700
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852352"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Översikt över Microsoft Cloud Adoption Framework for Azure Foundation-skissexemplet

Microsoft Cloud Adoption Framework for Azure (CAF) Foundation-skissen distribuerar en uppsättning huvudresurser för infrastruktur samt principkontroller som behövs för ditt första Azure-program i produktionsklass. Den här grundskissen baseras på det rekommenderade mönster som finns i CAF.

## <a name="architecture"></a>Arkitektur

CAF Foundation-skissexemplet distribuerar rekommenderade infrastrukturresurser i Azure som kan användas av organisationer för att etablera de grundkontroller som de behöver för att hantera sin molnegendom. Det här exemplet distribuerar och framtvingar resurser, principer och mallar som gör det möjligt för en organisation att på ett säkert sätt komma igång med Azure.

:::image type="content" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="CAF Foundation, bild som beskriver vad som installeras som en del av CAF-vägledningen för skapandet av en grund för att komma igång med Azure" border="false":::

Den här implementeringen omfattar flera Azure-tjänster som används för att tillhandahålla en säker, fullständigt övervakad och företagsfärdig grund. Den här miljön består av:

- En [Azure Key Vault](../../../../key-vault/general/overview.md)-instans som används som värd för hemligheter används för de virtuella datorerna som är distribuerade i miljön för delade tjänster
- [Log Analytics](../../../../azure-monitor/overview.md) distribueras för att se till att alla åtgärder och tjänster loggas till en central plats direkt när du påbörjar din säkra distribution till [lagringskonton](../../../../storage/common/storage-introduction.md) för diagnostisk loggning
- Distribution av [Azure Security Center](../../../../security-center/security-center-intro.md) (standardversionen) ger skydd mot hot för dina migrerade arbetsbelastningar
- Skissen definierar och distribuerar även [Azure-principer](../../../policy/overview.md) för 
  - Taggning (CostCenter) som tillämpas på resursgrupper
  - Tillägg av resurser i resursgrupper med CostCenter-taggen
  - Tillåten Azure-region för resurser och resursgrupper
  - Tillåtna lagringskonto-SKU:er (välj vid distribution)
  - Tillåtna SKU:er för virtuella Azure-datorer (välj vid distribution)
  - Krav på att Network Watcher ska distribueras 
  - Krav på säker överföringskryptering för Azure Storage-konto
  - Nekande av resurstyper (välj vid distribution)  
- Initiativ
  - Aktivera övervakning i Azure Security Center (över 100 principdefinitioner)

Alla dessa element följer beprövade metoder som finns publicerade i [Referensarkitekturer i Azure Architecture Center](/azure/architecture/reference-architectures/).

> [!NOTE]
> CAF Foundation utgör en grundläggande arkitektur för arbetsbelastningar.
> Du måste fortfarande distribuera arbetsbelastningar bakom den här grundläggande arkitekturen.

Mer information finns i [Microsoft Cloud Adoption Framework for Azure – redo](/azure/cloud-adoption-framework/ready/).

## <a name="next-steps"></a>Nästa steg

Du har läst översikten och arkitekturen för CAF Foundation-skissexemplet.

> [!div class="nextstepaction"]
> [CAF Foundation-skiss – distributionssteg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
