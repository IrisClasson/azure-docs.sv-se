---
title: Snabb start för striims
description: Kom igång snabbt med Striims och Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 945bcd03bc3bf13517836e7a5624bd5142782183
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208560"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Installations guide för striims Azure SQL DW Marketplace

Den här snabb starten förutsätter att du redan har en befintlig instans av SQL Data Warehouse.

Sök efter Striims på Azure Marketplace och välj alternativet Striims for data integration to SQL Data Warehouse (mellanlagrad) 

![Installera Striims][install]

Konfigurera den virtuella datorn Striims med angivna egenskaper, med Striims kluster namn, lösen ord och administratörs lösen ord

![Konfigurera Striims][configure]

När du har distribuerat klickar \<VM Name> du på masternode i Azure Portal, klickar på Anslut och kopierar inloggningen med hjälp av lokalt konto för virtuell dator 

![Anslut Striims till SQL Data Warehouse][connect]

Ladda ned sqljdbc42. jar från <https://www.microsoft.com/en-us/download/details.aspx?id=54671> till din lokala dator. 

Öppna ett kommando rads fönster och ändra de kataloger dit du laddade ned JDBC-jar-filen. SCP-filen till din virtuella Striims-dator, hämtar adressen och lösen ordet från Azure Portal

![Kopiera jar-fil till den virtuella datorn][copy-jar]

Öppna ett annat kommando rads fönster eller Använd ett SSH-verktyg för ssh i Striims-klustret

![SSH till klustret][ssh]

Kör följande kommandon för att flytta JDBC jar-filen till Striims-lib-katalogen och starta och stoppa servern.

   1. sudo su
   2. CD-katalogen/tmp
   3. MV sqljdbc42. jar/opt/Striim/lib
   4. systemctl stoppa striims-Node
   5. systemctl Stop striims-DBMS
   6. systemctl starta striims-DBMS
   7. systemctl starta striims-Node

![Starta Striims-klustret][start-striim]

Öppna din favorit webbläsare och gå till \<DNS Name> : 9080

![Gå till inloggnings skärmen][navigate]

Logga in med det användar namn och lösen ord som du angav i Azure Portal och välj önskad guide för att komma igång, eller gå till sidan appar för att börja använda gränssnittet dra och släpp

![Logga in med autentiseringsuppgifter för servern][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
