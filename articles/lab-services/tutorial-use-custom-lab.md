---
title: Få åtkomst till ett labb i Azure DevTest Labs | Microsoft Docs
description: I den här självstudien får du åtkomst till labbet som har skapats med Azure DevTest Labs, göra anspråk på virtuella datorer, använda dem och sedan släppa anspråket.
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
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: b5abb8d4aad7c58bf673aa578255efe12d32ad4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083591"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Självstudier: Komma åt ett labb i Azure DevTest Labs
I den här självstudien använder du det labb som skapades i [Självstudie: Skapa ett labb i Azure DevTest Labs](tutorial-create-custom-lab.md).

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Göra anspråk på en virtuell dator (VM) i ett labb
> * Anslut till VM:en
> * Släppa anspråk på en virtuell dator

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="access-the-lab"></a>Åtkomst till labbet

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla resurser** på menyn till vänster. 
3. Välj **DevTest Labs** som resurstyp. 
4. Välj labbet. 

    ![Välja labbet](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Göra anspråk på en virtuell dator

1. I listan **Virtuella datorer som kan tas i anspråk** väljer du **...** (tre punkter) och väljer **Ta dator i anspråk**.

    ![Göra anspråk på virtuell dator](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Bekräfta att du ser den virtuella datorn i listan **Mina virtuella datorer**.

    ![Mina virtuella datorer](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Anslut till VM:en

1. Välj din virtuella dator i listan. Du ser en **Virtuell dator-sida** för din VM. Välj **Anslut** i verktygsfältet.

    ![Ansluta till den virtuella datorn](./media/tutorial-use-custom-lab/connect-button.png)
2. Spara den nedladdade **RDP**-filen på hårddisken och använd den för att ansluta till den virtuella datorn. Ange användarnamnet och lösenordet du angav när den virtuella datorn skapades i föregående avsnitt. 

    > [!NOTE] 
    > Om du vill ansluta till en virtuell Linux-dator måste SSH- och/eller RDP-åtkomst aktiveras för den virtuella datorn. Anvisningar för hur du ansluter till en virtuell Linux-dator via RDP finns i [Installera och konfigurera Fjärrskrivbord för att ansluta till en virtuell Linux-dator i Azure](../virtual-machines/linux/use-remote-desktop.md). 


## <a name="unclaim-the-vm"></a>Släppa anspråk på en virtuell dator
När du är klar med den virtuella datorn släpper du anspråket på den virtuella datorn genom att följa dessa steg: 

1. På sidan för den virtuella datorn väljer du **Släpp krav** i verktygsfältet. 

    ![Släppa anspråk på en virtuell dator](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Den virtuella datorn stängs av innan kravet släpps. Du kan se status för den här åtgärden i meddelanden.  
3. Gå tillbaka till DevTest-labbsidan genom att klicka på namnet på ditt labb i adressfältmenyn längst upp. 
    
    ![Navigera tillbaka till labbet](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Bekräfta att du ser den virtuella datorn i listan över **Virtuella datorer som kan tas i anspråk** längst ned.

    
## <a name="next-steps"></a>Nästa steg
Den här självstudiekursen visade hur du använder ett labb som har skapats med hjälp av Azure DevTest Labs. Läs mer om åtkomst till och användning av virtuella datorer i ett labb i 

> [!div class="nextstepaction"]
> [Anvisningar: Använda virtuella datorer i ett labb](devtest-lab-add-vm.md)

