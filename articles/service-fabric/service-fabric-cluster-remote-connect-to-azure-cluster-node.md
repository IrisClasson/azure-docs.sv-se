---
title: Fjärrans luta till en Azure Service Fabric-klusternod
description: Lär dig hur du fjärransluter till en skalnings uppsättnings instans (en Service Fabric klusternod).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75458327"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärrans luta till en instans av en skalnings uppsättning för virtuell dator eller en klusternod
I ett Service Fabric kluster som körs i Azure kan varje typ av klusternod som du definierar [ställa in en virtuell dator separat skala](service-fabric-cluster-nodetypes.md).  Du kan fjärrans luta till angivna skalnings uppsättnings instanser (klusternoder).  Till skillnad från virtuella datorer med en instans, har skalnings uppsättnings instanser inte sina egna virtuella IP-adresser. Detta kan vara svårt när du söker efter en IP-adress och port som du kan använda för att fjärrans luta till en angiven instans.

Utför följande steg för att hitta en IP-adress och port som du kan använda för att fjärrans luta till en angiven instans.

1. Hämta inkommande NAT-regler för Remote Desktop Protocol (RDP).

    Varje nodtyp som definierats i klustret har vanligt vis en egen virtuell IP-adress och en dedikerad belastningsutjämnare. Som standard heter belastningsutjämnaren för en nodtyp med följande format: *lb-{Cluster-Name}-{Node-Type}*; till exempel *lb-till-kluster-klient*del. 
    
    På sidan för belastningsutjämnaren i Azure Portal väljer du **Inställningar**  >  **inkommande NAT-regler**: 

    ![Ingående NAT-regler för belastnings utjämning](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Följande skärm bild visar de ingående NAT-reglerna för en nodtyp med namnet FrontEnd: 

    ![Ingående NAT-regler för belastnings utjämning](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    För varje nod visas IP-adressen i **mål** kolumnen, kolumnen **mål** som visar skalnings uppsättnings instansen och **tjänst** kolumnen innehåller port numret. För fjärr anslutning allokeras portarna till varje nod i stigande ordning som börjar med port 3389.

    Du kan också hitta inkommande NAT-regler i `Microsoft.Network/loadBalancers` avsnittet i Resource Manager-mallen för klustret.
    
2. Om du vill bekräfta den inkommande porten till mål Port mappningen för en nod kan du klicka på dess regel och titta på **mål Port** svärdet. Följande skärm bild visar den inkommande NAT-regeln för noden **klient del (instans 1)** i föregående steg. Observera att även om Port numret för (inkommande) är 3390 mappas mål porten till port 3389, porten för RDP-tjänsten på målet.  

    ![Mål Port mappning](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Som standard är mål porten port 3389 för Windows-kluster som mappar till RDP-tjänsten på målnoden. För Linux-kluster är mål porten port 22, som mappar till SSH-tjänsten (Secure Shell).

3. Fjärrans luta till den angivna noden (skalnings uppsättnings instans). Du kan använda det användar namn och lösen ord som du angav när du skapade klustret eller andra autentiseringsuppgifter som du har konfigurerat. 

    Följande skärm bild visar hur du använder Anslutning till fjärrskrivbord för att ansluta till noden **FrontEnd (instans 1)** i ett Windows-kluster:
    
    ![Anslutning till fjärrskrivbord](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    På Linux-noder kan du ansluta med SSH (följande exempel återanvänder samma IP-adress och port för det kortfattat):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


För nästa steg kan du läsa följande artiklar:
* Se [översikten över funktionen "distribuera överallt" och en jämförelse med Azure-hanterade kluster](service-fabric-deploy-anywhere.md).
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Uppdatera RDP-portens intervall värden](./scripts/service-fabric-powershell-change-rdp-port-range.md) på virtuella kluster datorer efter distributionen
* [Ändra administratörens användar namn och lösen ord](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) för virtuella kluster datorer

