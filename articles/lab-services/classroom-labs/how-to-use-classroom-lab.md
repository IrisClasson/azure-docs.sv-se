---
title: Få åtkomst till ett klassrumslabb i Azure Lab Services | Microsoft Docs
description: I självstudien får du åtkomst till virtuella datorer i ett klassrumslabb som har konfigurerats av en lärare.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/17/2018
ms.author: spelluru
ms.openlocfilehash: 4b137396dd6a8ff924c9380aeb87a81b95f91414
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466230"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Få åtkomst till ett klassrumslabb i Azure Lab Services
Artikeln beskriver hur du får åtkomst till ett klassrumslabb, ansluter till den virtuella datorn i labbet och stoppar den virtuella datorn. 

## <a name="view-all-the-classroom-labs"></a>Visa alla klassrumslabb

1. Gå till **registrerings-URL:en** som du fick av läraren. 
2. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
3. När registreringen är klar kontrollerar du att du ser virtuella datorer för de labb som du har åtkomst till. 

    ![Visa alla labb](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Ansluta till den virtuella datorn i ett klassrumslabb

1. Välj **Anslut** i panelen som representerar den virtuella dator som du vill ha åtkomst till i labbet.

    ![Visa alla labb](../media/how-to-use-classroom-lab/connect-button.png)
2. Det tar några minuter innan den virtuella datorn är klar.

    ![Få den virtuella datorn att bli klar](../media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Spara RDP-filen på hårddisken och öppna den. 
    
    ![Spara RDP-filen](../media/how-to-use-classroom-lab/save-rdp-file.png)
4. Använd det **användarnamn** och **lösenord** som du fick av läraren och logga in på datorn. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Stoppa den virtuella datorn i ett klassrumslabb

Välj **Stoppa** i panelen som representerar klassrumslabbet. När den virtuella datorn har stoppats aktiveras knappen **Starta** i panelen. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](how-to-manage-classroom-labs.md)
- [Konfigurera ett labb](../tutorial-create-custom-lab.md)

 