---
title: 'Snabbstart: Skapa en offentlig grundläggande lastbalanserare med hjälp av Azure-portalen'
titlesuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en offentlig grundläggande lastbalanserare med Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 9819111c8264493648233f40252db4fb4410aaf1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274090"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Snabbstart: Skapa en Basic-lastbalanserare med hjälp av Azure-portalen

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande begäranden sprids över virtuella datorer. Du kan använda Azure-portalen för att skapa en lastbalanserare och balansera trafik över virtuella datorer. Den här snabbstarten visar hur du skapar och konfigurerar en lastbalanserare, serverdelsservrar och nätverksresurser på prisnivån Grundläggande.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

Logga in på [Azure-portalen](https://portal.azure.com) för att genomföra alla uppgifter i den här snabbstarten.

## <a name="create-a-basic-load-balancer"></a>Skapa en grundläggande lastbalanserare

Först skapar du en Basic-lastbalanserare med hjälp av portalen. Det namn och den offentliga IP-adress som du skapar konfigureras automatiskt som lastbalanserarens klientdel.

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Lastbalanserare**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Välj din prenumeration.    |    
    | Resource group         | Välj **Skapa ny** och skriv *MyResourceGroupLB* i textrutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Västeuropa**.                                        |
    | type          | Välj **Offentligt**.                                        |
    | SKU           | Välj **Grundläggande**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | *MyPublicIP*   |
    | Tilldelning| Statisk|

3. På fliken **Granska + skapa** klickar du på **Skapa**.   


## <a name="create-back-end-servers"></a>Skapa serverdelsservrar

Sedan skapar du ett virtuellt nätverk och två virtuella datorer för serverdelspoolen för din grundläggande lastbalanserare. 

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Uppe till vänster i portalen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
   
1. I fönsterrutan **Skapa virtuellt nätverk** skriver eller väljer du dessa värden:
   
   - **Namn**: Skriv *MyVNet*.
   - **ResourceGroup**: I listrutan **Välj befintlig** väljer du **MyResourceGroupLB**. 
   - **Undernät** > **namn**: Skriv *MyBackendSubnet*.
   
1. Välj **Skapa**.

   ![Skapa ett virtuellt nätverk](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Uppe till vänster i portalen väljer du **Skapa en resurs** > **Beräkning** > **Windows Server 2016 Datacenter**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumeration** > **Resursgrupp**: I listrutan väljer du **MyResourceGroupLB**.
   - **Instansinformation** > **Namn på virtuell dator**: Skriv *MyVM1*.
   - **Instansinformation** > **Tillgänglighetsalternativ**: 
     1. I listrutan väljer du **Tillgänglighetsuppsättning**. 
     2. Välj **Skapa ny**, skriv *MyAvailabilitySet* och välj **OK**.
  
1. Välj fliken **Nätverk** eller välj **Nästa: Diskar** och sedan **Nästa: Nätverk**. 
   
   Kontrollera att följande har valts:
   - **Virtuellt nätverk**: **MyVnet**
   - **Undernät**: **MyBackendSubnet**
   - **Offentlig IP-adress**: **MyVM1-ip**
   
   För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
   1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
   1. Skriv *MyNetworkSecurityGroup* och välj **OK**. 
   
1. Välj fliken **Hantering** eller **Nästa** > **Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
   
1. Välj **Granska + skapa**.
   
1. Granska inställningarna och välj sedan **Skapa**. 

1. Följ stegen för att skapa en andra virtuell dator med namnet *MyVM2*, med en **Offentlig IP-adress** angiven till *MyVM2-ip* och alla andra inställningar på samma sätt som MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Skapa NSG-regler för de virtuella datorerna

I det här avsnittet skapar du regler för nätverkssäkerhetsgrupper (NSG) för de virtuella datorerna för att tillåta inkommande Internet- (HTTP) och fjärrskrivbordsanslutningar (RDP).

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **MyNetworkSecurityGroup** i resursgruppen **MyResourceGroupLB**.
   
1. Under **Inställningar** väljer du **Inkommande säkerhetsregler** och sedan **Lägg till**.
   
1. I dialogrutan **Lägg till ingående säkerhetsregel** för HTTP-regeln skriver eller väljer du följande:
   
   - **Källa**: Välj **Service Tag** (Tjänsttagg).  
   - **Källtjänsttagg**: Välj **Internet**. 
   - **Målportintervall**: Skriv *80*.
   - **Protokoll**: Välj **TCP**. 
   - **Åtgärd**: Välj **Tillåt**.  
   - **Prioritet**: Skriv *100*. 
   - **Namn**: Skriv *MyHTTPRule*. 
   - **Beskrivning**: Skriv *tillåt HTTP*. 
   
1. Välj **Lägg till**. 
   
   ![Skapa en NSG-regel](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Upprepa stegen för den inkommande RDP-regeln med följande värden som skiljer sig:
   - **Målportintervall**: Skriv *3389*.
   - **Prioritet**: Skriv *200*. 
   - **Namn**: Skriv *MyRDPRule*. 
   - **Beskrivning**: Skriv *Tillåt RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Skapa resurser för lastbalanseraren

I det här avsnittet konfigurerar du inställningarna för lastbalanseraren för en serverdelsadresspool, en hälsoavsökning och en belastningsutjämningsregel.

### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

Lastbalanseraren använder en serverdelsadresspool för att distribuera trafik till de virtuella datorerna. Serverdelsadresspoolen innehåller IP-adresserna för de gränssnitt för virtuella nätverk (NIC) som är anslutna till lastbalanseraren. 

**Så här skapar du en serverdelsadresspool som innehåller VM1 och VM2:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Serverdelspooler** och sedan **Lägg till**.
   
1. På sidan **Lägg till serverdelspool** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv *MyBackEndPool*.
   - **Associerad med**: I listrutan väljer du **Tillgänglighetsuppsättning**.
   - **Tillgänglighetsuppsättning**: Välj **MyAvailabilitySet**.
   
1. Välj **Lägg till en IP-konfiguration för målnätverk**. 
   1. Lägg till varje virtuell dator (**MyVM1** och **MyVM2**) som du skapade till serverdelspoolen.
   2. När du har lagt till varje dator öppnar du listrutan och väljer dess **Nätverks-IP-konfiguration**. 
   
1. Välj **OK**.
   
   ![Lägga till serverdelsadresspoolen](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. På sidan **Serverdelspooler** expanderar du **MyBackendPool** och kontrollerar att både **VM1** och **VM2** visas.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att lastbalanseraren ska övervaka VM-status använder du en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. 

**Så här skapar du en hälsoavsökning för att övervaka de virtuella datorernas hälsa:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.
   
1. På sidan **Lägg till en hälsoavsökning**  skriver eller väljer du följande värden:
   
   - **Namn**: Skriv *MyHealthProbe*.
   - **Protokoll**: I listrutan väljer du **HTTP**. 
   - **Port**: Skriv *80*. 
   - **Sökväg**: Acceptera */* för standard-URI. Du kan ersätta det här värdet med valfri URI. 
   - **Intervall**: Skriv *15*. Intervallet är antalet sekunder mellan avsökningsförsök.
   - **Tröskelvärde för Ej felfri**: Skriv *2*. Det här värdet är det antal avsökningsfel i följd som ska inträffa innan en virtuell dator anses vara felaktig.
   
1. Välj **OK**.
   
   ![Lägga till en avsökning](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar hur trafiken ska distribueras till de virtuella datorerna. Regeln definierar IP-konfigurationen på klientdelen för inkommande trafik, serverdels-IP-poolen för att ta emot trafik samt nödvändiga käll- och målportar. 

Lastbalanserarregeln med namnet **MyLoadBalancerRule** avlyssnar port 80 i klientdelen **LoadBalancerFrontEnd**. Regeln skickar nätverkstrafik till serverdelsadresspoolen **MyBackEndPool**, även det med port 80. 

**Så här skapar du belastningsutjämningsregeln:**


1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Belastningsutjämningsregler** och väljer sedan **Lägg till**.
   
1. På sidan **Lägg till belastningsutjämningsregel** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv *MyLoadBalancerRule*.
   - **Klientdels-API-adress:** Skriv *LoadBalancerFrontend*.
   - **Protokoll**: Välj **TCP**.
   - **Port**: Skriv *80*.
   - **Serverdelsport**: Skriv *80*.
   - **Serverdelspool**: Välj **MyBackendPool**.
   - **Hälsoavsökning**: Välj **MyHealthProbe**. 
   
1. Välj **OK**.
   
   ![Lägga till en lastbalanserarregel](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Du använder den offentliga IP-adressen för att testa lastbalanseraren på de virtuella datorerna. 

På portalen går du till sidan **Översikt** för **MyLoadBalancer** och letar upp dess offentliga IP-adress under **Offentlig IP-adress**. Hovra över adressen och kopiera den genom att välja ikonen **Kopiera**. 

### <a name="install-iis-on-the-vms"></a>Installera IIS på de virtuella datorerna

Installera IIS (Internet Information Services) på de virtuella datorerna för att testa lastbalanseraren.

**Så här ansluter du via fjärrskrivbord (RDP) till den virtuella datorn:**

1. På portalen väljer du **Alla resurser** på menyn till vänster. I resurslistan väljer du **MyVM1** i resursgruppen **MyResourceGroupLB**.
   
1. På sidan **Översikt** väljer du **Anslut** och sedan **Ladda ned RDP-fil**. 
   
1. Öppna den RDP-fil som du laddade ned och välj **Anslut**.
   
1. På skärmen Windows-säkerhet väljer du **Fler alternativ** och sedan **Använd ett annat konto**. 
   
   Ange ett användarnamn och ett lösenord, och välj **OK**.
   
1. Svara **Ja** på eventuella certifikatfrågor. 
   
   Den virtuella datorns skrivbord öppnas i ett nytt fönster. 
   
**Så här installerar du IIS**

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myVM1** i resurs gruppen *myResourceGroupSLB* i resurs gruppen.
2. Välj **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
5. Logga in på den virtuella datorn med de autentiseringsuppgifter som du angav när du skapade den virtuella datorn. När du gör det startar en fjärrskrivbordssession med den virtuella datorn *myVM1*.
6. Navigera till **Windows Administrationsverktyg**>**Windows PowerShell** på serverdatorn.
7. I PowerShell-fönstret kör du följande kommandon för att installera IIS.servern, ta bort standardfilen iisstart.htm och lägga till en ny iisstart.htm-fil som visar namnet på den virtuella datorn:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Stäng RDP-sessionen med *myVM1*.
7. Upprepa steg 1 till 6 för att installera IIS och den uppdaterade filen iisstart.htm på *myVM2*.
   
1. Upprepa stegen för den virtuella datorn **MyVM2** men ställ in målservern på **MyVM2**.

### <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Öppna en webbläsare och klistra in lastbalanserarens offentliga IP-adress i webbläsarens adressfält. IIS-webbserverns standardsida bör visas i webbläsaren.

![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se hur lastbalanseraren distribuerar trafik över båda virtuella datorer som kör din app, kan du framtvinga uppdatering av webbläsaren.
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort lastbalanseraren och alla relaterade resurser när du inte längre behöver dem öppnar du resursgruppen **MyResourceGroupLB** och väljer **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en lastbalanserare på nivån Grundläggande. Du skapade och konfigurerade en resursgrupp, nätverksresurser, serverdelsservrar, en hälsoavsökning och regler för att använda med lastbalanseraren. Du installerade IIS på de virtuella datorerna och använde det för att testa lastbalanseraren. 

Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
