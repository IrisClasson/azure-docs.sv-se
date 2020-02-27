---
title: Filtrera inkommande Internet trafik med Azure Firewall DNAT med portalen
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall DNAT via Azure-portalen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/26/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1528087ced54ddcab2e3dd44b65fb3411cae3004
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621792"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Självstudie: filtrera inkommande Internet trafik med Azure Firewall DNAT med hjälp av Azure Portal

Du kan konfigurera Azure Firewall destination Network Address Translation (DNAT) för att översätta och filtrera inkommande Internet trafik till dina undernät. När du konfigurerar DNAT ställs åtgärden för NAT-regelsamling in på **Dnat**. Varje regel i NAT-regelsamlingen kan sedan användas till att översätta brandväggens offentliga IP-adress och port till en privat IP-adress och port. DNAT-regler lägger implicit till en motsvarande nätverksregel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken. Mer information om regelbearbetningslogik för Azure Firewall finns i [Regelbearbetningslogik för Azure Firewall](rule-processing.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * skapa en standardväg
> * Konfigurera en DNAT-regel
> * Testa brandväggen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För den här självstudien skapar du två peerkopplade virtuella nätverk:

- **VN-Hub** – brandväggen finns i det här virtuella nätverket.
- **VN-Spoke** – arbetsbelastningsservern finns i det här virtuella nätverket.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på startsidan för Azure Portal, klicka på **Resursgrupper** och sedan på **Lägg till**.
3. I fältet **Resursgruppsnamn** anger du **RG-DNAT-Test**.
4. I fältet **Prenumeration** väljer du din prenumeration.
5. I fältet **Resursgruppsplats** väljer du en plats. Alla efterföljande resurser du skapar måste finnas på samma plats.
6. Klicka på **Skapa**.

## <a name="set-up-the-network-environment"></a>Konfigurera nätverksmiljön

Skapa först de virtuella nätverken och peerkoppla dem sedan.

### <a name="create-the-hub-vnet"></a>Skapa det virtuella Hub-nätverket

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Virtuella nätverk**.
3. Klicka på **Lägg till**.
4. Som **Namn** anger du **VN-Hub**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**.

     Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
     > [!NOTE]
     > Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. För **adress intervall**skriver du **10.0.1.0/26**.
11. Använd övriga standardinställningar och klicka på **Skapa**.

### <a name="create-a-spoke-vnet"></a>Skapa ett virtuellt spoke-nätverk

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Virtuella nätverk**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** anger du **VN-Spoke**.
5. I fältet **Adressutrymme** skriver du **192.168.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn** anger du **SN-Workload**.

    Servern kommer att finnas i det här undernätet.
10. I fältet **Adressintervall** skriver du **192.168.1.0/24**.
11. Använd övriga standardinställningar och klicka på **Skapa**.

### <a name="peer-the-vnets"></a>Peerkoppla de virtuella nätverken

Peerkoppla nu de två virtuella nätverken.

#### <a name="hub-to-spoke"></a>Hub till spoke

1. Klicka nu på det virtuella nätverket **VN-Hub**.
2. Under **Inställningar** klickar du på **Peering-sessioner**.
3. Klicka på **Lägg till**.
4. Ange **Peer-HubSpoke** som namn.
5. Välj **VN-Spoke** för det virtuella nätverket.
6. Klicka på **OK**

#### <a name="spoke-to-hub"></a>Spoke till hub

1. Klicka på det virtuella nätverket **VN-Spoke**.
2. Under **Inställningar** klickar du på **Peering-sessioner**.
3. Klicka på **Lägg till**.
4. Ange **Peer-SpokeHub** som namn.
5. Välj **VN-Hub** för det virtuella nätverket.
6. Klicka på **Tillåt vidarebefordrad trafik**.
7. Klicka på **OK**

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell arbetsbelastningsdator och placera den i undernätet **SN-Workload**.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Compute** klickar du på **Virtuella datorer**.
3. Klicka på **Lägg till**, klicka på **Windows Server**, klicka på **Windows Server 2016 Datacenter** och sedan på **Skapa**.

**Grundläggande inställningar**

1. I fältet **Namn** skriver du **Srv-Workload**.
5. Ange ett användarnamn och lösenord.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** klickar du på **Använd befintlig** och väljer sedan **RG-DNAT-Test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Klicka på **OK**

**Storlek**

1. Välj en lämplig storlek för den virtuella testdator som ska köra Windows Server. Exempelvis **B2ms** (8 GB RAM-minne, 16 GB lagring).
2. Klicka på **Välj**.

**Inställningar**

1. Under **Nätverk**, i fältet **Virtuellt nätverk**, väljer du **VN-Spoke**.
2. I fältet **Undernät** väljer du **SN-Workload**.
3. Klicka på **Offentlig IP-adress** och klicka sedan på **Ingen**.
4. I fältet **Välj offentliga inkommande portar** väljer du **Inga offentliga inkommande portar**. 
2. Lämna övriga standardvärden och klicka på **OK**.

**Sammanfattning**

Granska sammanfattningen och klicka sedan på **Skapa**. Det här kan ta några minuter.

När distributionen är klar antecknar du den privata IP-adressen för den virtuella datorn. Den används senare när du konfigurerar brandväggen. Klicka på namnet på den virtuella datorn. Under **Inställningar** klickar du på **Nätverk** för att hitta den privata IP-adressen.

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

1. På portalens startsida klickar du på **Skapa en resurs**.
2. Klicka på **Nätverk**, och sedan efter **Aktuella** klickar du på **Visa alla**.
3. Klicka på **Brandvägg** och sedan på **Skapa**. 
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Name     |FW-DNAT-test|
   |Prenumeration     |\<din prenumeration\>|
   |Resursgrupp     |**Använd befintlig**: RG-DNAT-Test |
   |Plats     |Välj samma plats som tidigare|
   |Välj ett virtuellt nätverk     |**Använd befintlig**: VN-Hub|
   |Offentlig IP-adress     |**Skapa ny**. Den offentliga IP-adressen måste vara Standard SKU-typen.|

5. Klicka på **Granska + skapa**.
6. Granska sammanfattningen och klicka sedan på **Skapa** för att skapa brandväggen.

   Distributionen kan ta några minuter.
7. När distributionen är klar går du till resursgruppen **RG-DNAT-Test** och klickar på brandväggen **FW-DNAT-test**.
8. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>skapa en standardväg

För undernätet **SN-Workload** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Routningstabeller**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** skriver du **RT-FWroute**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer **RG-DNAT-Test**.
7. Välj samma plats som tidigare i fältet **Plats**.
8. Klicka på **Skapa**.
9. Klicka på **Uppdatera** och klicka sedan på routningstabellen **RT-FWroute**.
10. Klicka på **Undernät** och sedan på **Associera**.
11. Klicka på **Virtuellt nätverk** och välj sedan **VN-Spoke**.
12. I fältet **Undernät** klickar du på **SN-Workload**.
13. Klicka på **OK**
14. Klicka på **Vägar** och sedan på **Lägg till**.
15. I fältet **Vägnamn** skriver du **FW-DG**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Klicka på **OK**

## <a name="configure-a-nat-rule"></a>Konfigurera en NAT-regel

1. Öppna **RG-DNAT-Test** och klicka på brandväggen **FW-DNAT-test**. 
2. På sidan **FW-DNAT-test**, under **Inställningar** klickar du på **Regler**. 
3. Klicka på **Lägg till NAT-regelsamling**. 
4. I fältet **Namn** skriver du **RC-DNAT-01**. 
5. I fältet **Prioritet** skriver du **200**. 
6. Under **Regler**, i fältet **Namn**, skriver du **RL-01**.
7. I fältet **Protokoll** väljer du **TCP**.
8. I fältet **Källadresser** skriver du *. 
9. I fältet **Måladresser** skriver du brandväggens offentliga IP-adress. 
10. I fältet **Målportar** skriver du **3389**. 
11. I fältet **Översatt adress** skriver du den privata IP-adressen för den virtuella datorn Srv-Workload. 
12. I fältet **Översatt port** skriver du **3389**. 
13. Klicka på **Lägg till**. 

## <a name="test-the-firewall"></a>Testa brandväggen

1. Anslut ett fjärrskrivbord till brandväggens offentliga IP-adress. Du ska vara ansluten till den virtuella datorn **Srv-Workload**.
2. Stäng fjärrskrivbordet.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **RG-DNAT-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * skapa en standardväg
> * Konfigurera en DNAT-regel
> * Testa brandväggen

Därefter kan du övervaka Azure Firewall-loggarna.

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
