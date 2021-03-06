---
title: Installera Uppdatering 0,6 på StorSimple virtuell matris | Microsoft Docs
description: Beskriver hur du använder StorSimple-webbgränssnittet för virtuella matriser för att tillämpa uppdateringar med hjälp av metoden Azure Portal och snabb korrigeringar
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 02b85cb90948f35cb6f6c855cfbe81fd58301de0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513594"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installera Uppdatering 0,6 på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera uppdatering 0,6 på din virtuella StorSimple-matris via det lokala webb gränssnittet och via Azure Portal. Du tillämpar program uppdateringarna eller hotfixarna för att hålla din StorSimple virtuella matris uppdaterad.

Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data. När volymerna eller resurserna är offline bör du också göra en manuell säkerhets kopiering av enheten.

> [!IMPORTANT]
>
> - Uppdatering 0,6 motsvarar **10.0.10293.0** program varu version på enheten. Information om vad som är nytt i den här uppdateringen finns i [viktig information om uppdatering 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Om du kör uppdatering 0,2 eller senare, rekommenderar vi att du installerar uppdateringarna via Azure Portal. Om du kör uppdatering 0,1 eller GA-program versioner måste du använda snabb korrigerings metoden via det lokala webb gränssnittet för att installera uppdatering 0,6.
>
> - Kom ihåg att om du installerar en uppdatering eller snabb korrigering startas enheten om. Med tanke på att den virtuella StorSimple-matrisen är en enskild Node-enhet avbryts alla i/O-åtgärder och enheten upplever drift stopp.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör uppdatering 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure Portal. Portal proceduren kräver att användaren skannar, laddar ned och sedan installerar uppdateringarna. Den här proceduren tar cirka 7 minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar går du till StorSimple Enhetshanteraren-tjänsten. Välj **enheter** och välj sedan och klicka på enheten som du precis har uppdaterat. Gå till **inställningar > hantera > enhets uppdateringar**. Den program varu version som visas ska vara **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Använd det lokala webb gränssnittet

Det finns två steg när du använder det lokala webb gränssnittet:

* Hämta uppdateringen eller snabb korrigeringen
* Installera uppdateringen eller snabb korrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

1. Starta Internet Explorer och gå till [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Om du använder Microsoft Update katalogen för första gången på datorn klickar du på **Installera** när du uppmanas att installera Microsoft Update Catalog-tillägget.

3. I rutan Sök i Microsoft Update-katalogen anger du Knowledge Base-numret för den snabb korrigering som du vill ladda ned. Ange **4023268** för uppdatering 0,6 och klicka sedan på **Sök**.
   
    Listan med snabb korrigeringar visas, till exempel **StorSimple Virtual Array uppdatering 0,6**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klicka på **Hämta**.

5. Du bör se fem filer som ska laddas ned. Ladda ned var och en av dessa filer till en mapp. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.
    ![Filer i paketet](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Du ser:
    -  En Microsoft Update fristående paket fil `WindowsTH-KB3011067-x64` . Den här filen används för att uppdatera enhetens program vara.
    - En paket fil för en Genève-övervaknings agent `GenevaMonitoringAgentPackageInstaller` . Den här filen används för att uppdatera övervaknings-och Diagnostics service-agenten (MDS). Dubbelklicka på CAB-filen. En _. msi_ visas. Markera filen, högerklicka på den och **extrahera** sedan filen. Du kan använda _MSI_ -filen för att uppdatera agenten.

        ![Extrahera MDS-agentens uppdaterings fil](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Du behöver inte uppdatera MDS-agenten om du kör StorSimple Update 0,5 (0.0.10293.0).

    - Tre filer som innehåller viktiga Windows-säkerhetsuppdateringar, `windows8.1-kb4012213-x64` ,, `windows8.1-kb3205400-x64` och `windows8.1-kb4019213-x64` .


### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

Innan uppdateringen eller snabb korrigeringen installerades kontrollerar du att uppdateringen eller snabb korrigeringen laddats ned lokalt på värden eller kan nås via en nätverks resurs.

Använd den här metoden för att installera uppdateringar på en enhet som kör GA eller uppdatering 0,1 program varu versioner. Den här proceduren tar cirka 12 minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

1. I det lokala webb gränssnittet går du till **Underhåll**  >  **program uppdatering**. Anteckna den program varu version som du kör. Om du kör **10.0.10290.0**behöver du inte uppdatera MDS-agenten i steg 6.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. I **Uppdatera fil Sök väg**anger du fil namnet för uppdateringen eller snabb korrigeringen. Du kan också bläddra till installations filen för uppdateringen eller hotfixen om den placeras på en nätverks resurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. En varning visas. Om den virtuella matrisen är en enskild nod enhet startar enheten om och det uppstår avbrott när uppdateringen har tillämpats. Klicka på kryss ikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användar gränssnittet är inte tillgängligt under denna varaktighet.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. När omstarten är klar tas du till **inloggnings** sidan. Kontrol lera att enhetens program vara har uppdaterats genom att gå till **Underhåll**  >  **program uppdatering**i det lokala webb gränssnittet. Den program varu version som visas ska vara **10.0.0.0.0.10293** för uppdatering 0,6.
   
   > [!NOTE]
   > Vi rapporterar program versioner på ett något annorlunda sätt i det lokala webb gränssnittet och Azure Portal. Till exempel kan de lokala Web UI-rapporterna **10.0.0.0.0.10293** och Azure Portal rapporterar **10.0.10293.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Hoppa över det här steget om du körde StorSimple Virtual Array uppdatering 0,5 (**10.0.10290.0**) innan du använde den här uppdateringen. Du har gjort en anteckning om program varu versionen i steg 1 innan du började uppdatera. Om du körde uppdatering 0,5 är MDS-agenten redan uppdaterad.

    Om du kör en program varu version före uppdateringen 0,5 är nästa steg att uppdatera MDS-agenten. På sidan **program uppdatering** går du till sökvägen för **uppdaterings filen** och bläddrar till `GenevaMonitoringAgentPackageInstaller.msi` filen. Upprepa steg 2-4. När den virtuella matrisen har startats om loggar du in på det lokala webb gränssnittet.

7. Upprepa steg 2-4 för att installera säkerhets korrigeringarna för Windows med hjälp av filer `windows8.1-kb4012213-x64` , `windows8.1-kb3205400-x64` och `windows8.1-kb4019213-x64` . Den virtuella matrisen startas om efter varje installation och du måste logga in på det lokala webb gränssnittet.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [du administrerar din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

