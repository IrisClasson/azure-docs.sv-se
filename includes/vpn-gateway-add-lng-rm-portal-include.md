---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9c18a2c74d03a636a0865f3008eb421ab8d7412d
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781497"
---
1. Klicka på **+Skapa en resurs** i portalen.
2. I sökrutan skriver du **Lokal nätverksgateway** och trycker sedan på **Retur** för att söka. En lista med resultat returneras. Klicka på **Lokal nätverksgateway** och sedan på knappen **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

   ![Skapa den lokala nätverksgatewayen](./media/vpn-gateway-add-lng-rm-portal-include/local-network-gateway.png "Skapa den lokala nätverksgatewayen")

3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

   - **Namn:** Ange ett namn för objektet för din lokala nätverksgateway.
   - **IP-adress:** Det här är den offentliga IP-adressen för VPN-enheten som du vill att Azure ska ansluta till. Ange en giltig offentlig IP-adress. Om du inte har IP-adressen just nu kan du använda de värden som visas i exemplet. Du behöver dock gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för din VPN-enhet. Azure kommer annars inte kunna ansluta.
   - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd egna värden här om du vill ansluta till din lokala plats. Använd inte de värden som visas i exemplet*.
   - **Konfigurera BGP-inställningar:** Använd endast när du konfigurerar BGP. Annars ska detta inte väljas.
   - **Prenumeration:** Kontrol lera att rätt prenumeration visas.
   - **Resursgrupp:** Välj den resurs grupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
   - **Plats:** Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

4. När du är klar med att ange värden klickar du på knappen **skapa** för att skapa gatewayen.
