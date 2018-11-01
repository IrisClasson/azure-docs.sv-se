---
title: Köra ett programåterställningstest för lokala datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig mer om hur du kör ett programåterställningstest från lokala datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b0344095cd7c9aedd360d44f2649f27dfd78cd30
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214417"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Köra ett programåterställningstest till Azure

I den här självstudien visar vi hur du kör ett programåterställningstest för en lokal dator till Azure med hjälp av ett redundanstest. Med ett test kan du verifiera din replikeringsstrategi utan dataförlust.

Den här är den fjärde kursen i en serie som illustrerar hur du ställer in haveriberedskap i Azure för lokala virtuella VMware-datorer eller virtuella Hyper-V-datorer.

Den här kursen förutsätter att du har slutfört de första tre självstudierna:
    - I den [första självstudien](tutorial-prepare-azure.md), konfigurerade vi Azure komponenter som krävs för katastrofåterställning för VMware.
    - I den [andra kursen](vmware-azure-tutorial-prepare-on-premises.md) förberedde vi lokala komponenter för katastrofåterställning och granskade förutsättningarna.
    - I den [tredje kursen](vmware-azure-tutorial.md) ställde vi in och aktiverade replikering för våra lokala VMware VM.
    - Självstudierna är utformade för att visa den **enklaste distributionsvägen för ett scenario**. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill veta mer om redundansteststegen kan du läsa [Anvisningsguiden](site-recovery-test-failover-to-azure.md).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Ställer in ett isolerat nätverk för redundanstestet
> * Förbereder för att ansluta till den virtuella Azure-datorn efter en redundansväxling
> * Kör ett redundanstest för en enstaka virtuell dator



## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera den virtuella datorns egenskaper innan du kör ett redundanstest, och se till att din [virtuella Hyper-V-dator](hyper-v-azure-support-matrix.md#replicated-vms) eller [virtuella VMware-dator](vmware-physical-azure-support-matrix.md#replicated-machines) uppfyller kraven för Azure.

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > och VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, tillgänglighetsuppsättning och hanterade diskinställningar.
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Textnätverket ska använda samma IP-adressintervall.
- Uppdatera DNS för testnätverket med IP-adressen som anges för DNS-VM i inställningarna i **Beräkning och nätverk**. Mer information finns i [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Köra ett redundanstest för en enstaka virtuell dator

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten, skapas en återställningspunkt från data.
3. En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

Kör redundanstestet på följande sätt:

1. I **Inställningar** > **Replikerade objekt** klickar du på >**+Testa redundans** för den virtuella datorn.
2. Välj återställningspunkten **Senast bearbetade** för den här självstudien. Då redundansväxlar den virtuella datorn till den senast tillgängliga tidpunkten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
3. I **Redundanstest** väljer du det Azure-målnätverk som de virtuella Azure-datorerna ska ansluta till efter redundans.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** >
   **Platsåterställningsjobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter en redundansväxling följer du kraven som sammanfattas i tabellen [här](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra redundans och återställning efter fel för lokala virtuella VMware-datorer](vmware-azure-tutorial-failover-failback.md).
> [Köra redundans och återställning efter fel för lokala virtuella Hyper-V-datorer](hyper-v-azure-failover-failback-tutorial.md).
