---
title: Konfigurera trafikroutningsmetod för undernät med Azure Traffic Manager
description: I den här artikeln förklarar vi hur du konfigurerar Traffic Manager-trafik från användares undernät till specifika slutpunkter.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: da2d4816f3f7a99ac2d213d72d7e801cf630e165
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304941"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Dirigera trafik till specifika slutpunkter baserat på användares undernät med Traffic Manager

Den här artikeln beskriver hur du konfigurerar trafikroutningsmetoden för undernät. Med trafikroutningsmetoden för **undernät** kan du mappa en uppsättning IP-adressintervall till specifika slutpunkter. När en begäran tas emot av Traffic Manager undersöks käll-IP-adressen för begäran och slutpunkten som är kopplad till den returneras.

I de här självstudierna används undernätsroutning, vilket innebär att trafik dirigeras antingen till en intern webbplats eller en produktionswebbplats beroende på IP-adressen för användarens fråga.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil för trafikroutning som baseras på användarens undernät
> * Lägg till VM-slutpunkter i Traffic Manager-profilen
> * Se hur Traffic Manager fungerar i praktiken

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:

- två grundläggande webbplatser som körs i olika Azure-regioner – **USA, östra** (fungerar som intern webbplats) och **Europa, västra** (fungerar som produktionswebbplats).
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Europa, västra**.

De virtuella testdatorerna används för att illustrera hur Traffic Manager dirigerar trafik till den interna webbplatsen eller produktionswebbplatsen baserat på undernätet som användarfrågan kommer från.

### <a name="sign-in-to-azure"></a>Logga in till Azure

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Att skapa två webbplatser omfattar följande steg:

1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser

I det här avsnittet skapar du två virtuella datorer *myIISVMEastUS* och *myIISVMWestEurope* i den **USA, östra** och **Västeuropa** Azure-regioner.

1. I övre vänstra hörnet på Azure portal, Välj **skapa en resurs** > **Compute** > **Windows Server 2019 Datacenter**.
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Prenumeration** > **Resursgrupp**: Välj **Skapa nytt** och skriv sedan **myResourceGroupTM1**.
   - **Instansinformation** > **Namn på virtuell dator**: Typ *myIISVMEastUS*.
   - **Information om-instansen** > **Region**:  Välj **USA, östra**.
   - **Administratörskontot** > **användarnamn**:  Ange ett valfritt användarnamn.
   - **Administratörskontot** > **lösenord**:  Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regler för inkommande portar** > **offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Regler för inkommande portar** > **Välj ingående portar**: Välj **RDP** och **HTTP** i pull/ned-ruta.

3. Välj den **Management** fliken eller välj **nästa: Diskar** och sedan **Nästa: Nätverk**, sedan **nästa: Hantering av**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myIISVMWestEurope*, med en **resursgrupp** namn på *myResourceGroupTM2*, ett **plats**av *Västeuropa*, och de andra inställningarna identiskt *myIISVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet ska du installera IIS-servern på två virtuella datorer – *myIISVMEastUS* & *myIISVMWestEurope*, och sedan uppdaterar webbplatsen standardsidan. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
7. Starta Windows PowerShell på den virtuella datorn *myIISVMEastUS*, och använder följande kommandon för att installera IIS-servern och uppdatera standard htm-fil.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Stäng RDP-anslutning med *myIISVMEastUS* VM.
9. Upprepa steg 1 – 6 med genom att skapa en RDP-anslutning med den virtuella datorn *myIISVMWestEurope* inom den *myResourceGroupTM2* resursgrupp för att installera IIS och anpassa dess standardwebbsidan.
10. Starta Windows PowerShell på *myIISVMWestEurope* virtuell dator och använder följande kommandon för att installera IIS-servern och uppdatera standard htm-fil.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet ska du konfigurera DNS-namn för IIS-servrar – *myIISVMEastUS* och *myIISVMWestEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Upprepa steg 1 – 3, för den virtuella datorn med namnet *myIISVMWestEurope* som finns i den *myResourceGroupTM2* resursgrupp.

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

I det här avsnittet skapar du en virtuell dator (*myVMEastUS* och *myVMWestEurope*) i varje Azure-region (**USA, östra** och **Västeuropa**). Du använder dessa virtuella datorer för att testa hur Traffic Manager dirigerar trafik för användare baserat på undernätet för användarens fråga.

1. I övre vänstra hörnet på Azure portal, Välj **skapa en resurs** > **Compute** > **Windows Server 2019 Datacenter**.
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Prenumeration** > **Resursgrupp**: Välj **myResourceGroupTM1**.
   - **Instansinformation** > **Namn på virtuell dator**: Typ *myVMEastUS*.
   - **Information om-instansen** > **Region**:  Välj **USA, östra**.
   - **Administratörskontot** > **användarnamn**:  Ange ett valfritt användarnamn.
   - **Administratörskontot** > **lösenord**:  Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regler för inkommande portar** > **offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Regler för inkommande portar** > **Välj ingående portar**: Välj **RDP** i pull/ned-ruta.

3. Välj den **Management** fliken eller välj **nästa: Diskar** och sedan **Nästa: Nätverk**, sedan **nästa: Hantering av**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myVMWestEurope*, med en **resursgrupp** namn på *myResourceGroupTM2*, ett **plats** av *Västeuropa*, och de andra inställningarna identiskt *myVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som gör det möjligt att returnera specifika slutpunkter baserat på käll-IP-adressen för begäran.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetoden för **undernät**.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Befintlig** och ange *myResourceGroupTM1*. |
    | |                              |
    |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorer som kör IIS servrar – *myIISVMEastUS* & *myIISVMWestEurope* att dirigera användartrafik baserat på undernätet för användarens fråga.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Type                    | Azure-slutpunkt                                   |
    | Namn           | myInternalWebSiteEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |  Inställningar för undernätsroutning    |   Lägg till IP-adressen för *myVMEastUS* testa virtuell dator. Alla användare-frågor från den här virtuella datorn dirigeras till den *myInternalWebSiteEndpoint*.    |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myProdWebsiteEndpoint* för den offentliga IP-adressen *myIISVMWestEurope ip* som är associerad med den IIS-servern virtuell dator med namnet  *myIISVMWestEurope*. För **routning undernätsinställningar**, Lägg till IP-adressen för testet VM - *myVMWestEurope*. Alla användarfrågor från den här virtuella testdatorn dirigeras till slutpunkten – *myProdWebsiteEndpoint*.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet testar du hur Traffic Manager dirigerar användartrafik från ett visst undernät till en specifik slutpunkt. Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:

1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Se hur Traffic Manager fungerar i praktiken:
    - Från den Virtuella testdatorn (*myVMEastUS*) som finns i den **USA, östra** region i en webbläsare, bläddrar du till DNS-namnet på Traffic Manager-profilen.
    - Från den Virtuella testdatorn (*myVMWestEurope*) som finns i den **Västeuropa** region i en webbläsare, bläddrar du till DNS-namnet på Traffic Manager-profilen.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen

I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Eftersom den virtuella datorn *myVMEastUS* IP-adress som är associerad med slutpunkten *myInternalWebsiteEndpoint*, webbläsaren startar Test-webbplatsservern - *myIISVMEastUS*.

7. Anslut sedan till den virtuella datorn *myVMWestEurope* i **Västeuropa** med steg 1-5 och bläddra till domännamnet för Traffic Manager-profil från den här virtuella datorn. Eftersom den virtuella datorn *myVMWestEurope* IP-adress som är associerad med slutpunkten *myProductionWebsiteEndpoint*, webbläsaren startar Test-webbplatsservern - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen

När resursgrupperna inte längre behövs kan du ta bort dem (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
