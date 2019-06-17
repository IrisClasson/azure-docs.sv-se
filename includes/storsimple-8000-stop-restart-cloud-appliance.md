---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159711"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Så här stoppar och startar du en molninstallation

1. Om du vill stoppa en molninstallation går du till den virtuella datorn för molninstallationen.
    ![Virtuell dator i StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Klicka på **Stoppa** i kommandofältet.

    ![Virtuell dator i StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Virtuell dator i StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. När du stoppar en virtuell dator avallokeras den. När molninstallationen håller på att stoppas visas statusen **Frigör**. När molninstallationen har stoppats visas statusen **Stoppad (frigjord)** .

    ![Virtuell dator i StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. När en virtuell dator har stoppats startar du den genom att klicka på **Starta** (knappen blir tillgänglig). När molninstallationen har startats visas statusen **Startad**.

    ![Virtuell dator i StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Du kan stoppa och starta en molninstallation med följande cmdlets.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Så här startar du om en molninstallation

Om du vill starta om en molninstallation går du till den virtuella datorn för molninstallationen. Klicka på **Starta om** i kommandofältet. Bekräfta omstarten när du uppmanas att göra det. När molninstallationen är redo att användas visas statusen **Körs**.

![Virtuell dator i StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Du kan starta om en molninstallation med följande cmdlet.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

