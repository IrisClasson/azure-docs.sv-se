---
title: Säkerhets aviseringar för miljöer i Azure DevTest Labs
description: Den här artikeln visar hur du visar säkerhets aviseringar för en miljö i DevTest Labs och vidtar lämpliga åtgärder.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9eea06066cfca5f67d920456f16e2eb7893dce39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483983"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Säkerhets aviseringar för miljöer i Azure DevTest Labs
Som labb användare kan du nu Visa Azure Security Center aviseringar för dina labb miljöer. Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp. [Lär dig mer om säkerhets aviseringar i Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Krav
För närvarande kan du bara Visa säkerhets aviseringar för PaaS-miljöer (Platform as a Service) som distribueras i labbet. Om du vill testa eller använda den här funktionen [distribuerar du en miljö i labbet](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Visa säkerhets aviseringar för en miljö

1. På Start sidan för ditt labb väljer du **säkerhets aviseringar** på den vänstra menyn. Du bör se antalet säkerhets aviseringar (hög, medel och låg). Läs mer om [hur aviseringar klassificeras](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Säkerhets aviseringar – översikt](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Högerklicka på tre punkter (...) i den sista kolumnen och välj **Visa säkerhets aviseringar**. 

    ![Visa säkerhetsaviseringar](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Du hittar mer information om rekommendationerna för aviseringar och rådgivare. Lär dig mer om [att hantera och svara på säkerhets aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Visa säkerhetsaviseringar](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Nästa steg
Mer information om miljöer finns i följande artiklar:

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer](devtest-lab-configure-use-public-environments.md)
