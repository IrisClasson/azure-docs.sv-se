---
title: Replikera en virtuell Azure-dator till en annan Azure-region
description: Denna snabbstart innehåller de steg som krävs för att replikera en virtuell Azure-dator i en Azure-region till en annan region.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 623c45cbc82139090d893b8501c7d363c4443d67
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456204"
---
# <a name="replicate-an-azure-vm-to-another-azure-region"></a>Replikera en virtuell Azure-dator till en annan Azure-region

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

I denna snabbstart beskrivs hur du replikerar en virtuell Azure-dator till en annan Azure-region.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Den här artikeln är avsedd att guida en ny användare genom Azure Site Recovery-gränssnittet med standardalternativen och minsta anpassning. Om du vill ha mer information om de olika inställningarna som kan anpassas går du till [självstudiekursen för aktivering av replikering för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Aktivera replikering för virtuella Azure-datorer

1. I Azure-portalen klickar du på **Virtuella datorer** och väljer den virtuella dator som du vill replikera.
2. I **Åtgärder** klickar du på **Haveriberedskap**.
3. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion som du ska replikera till.
4. Acceptera de andra standardinställningarna för denna Snabbstart.
5. Klicka på **Aktivera replikering**. Ett jobb startas som aktiverar replikering för den virtuella datorn.

    ![aktivera replikering](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Kontrollera inställningarna

När replikeringen har slutförts kan du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. I den virtuella datormenyn, klickar du på **Haveriberedskap**.
2. Du kan kontrollera replikeringshälsan, återställningspunkter som har skapats samt käll- och målregioner på kartan.

   ![Replikeringsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Rensa resurser

Den virtuella datorn i den primära regionen slutar replikera när du inaktiverar replikering för den:

- Källans replikeringsinställningar rensas automatiskt.
- Site Recovery-faktureringen för den virtuella datorn stoppas också.

Stoppa replikering på följande sätt:

1. Välj VM.
2. Klicka på **Inaktivera replikering** i **Haveriberedskap**.

   ![Inaktivera replikering](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten replikerar du en enda virtuell dator till en sekundär region. Nu kan du utforska fler alternativ och testa att replikera en uppsättning virtuella Azure-datorer med hjälp av en återställningsplan.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md)
