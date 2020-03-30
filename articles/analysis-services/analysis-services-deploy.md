---
title: Distribuera en modell till Azure Analysis Services med Visual Studio | Microsoft-dokument
description: Lär dig hur du distribuerar en tabellmodell till en Azure Analysis Services-server med hjälp av Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572885"
---
# <a name="deploy-a-model-from-visual-studio"></a>Distribuera en modell från Visual Studio

När du har skapat en server i din Azure-prenumeration är du redo att distribuera en tabellmodelldatabas till den. Du kan använda Visual Studio med Analysis Services-projekt för att skapa och distribuera ett tabellmodellprojekt som du arbetar med. 

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* **Analysis Services-server** i Azure. Läs mer i [Skapa en Azure Analysis Services-server](analysis-services-create-server.md).
* **Tabellmodellprojekt** i Visual Studio eller en befintlig tabellmodell på 1200- eller högre kompatibilitetsnivå. Har du aldrig skapat någon? Testa [Självstudier för Adventure Works Internetförsäljning – tabellmodell ](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Lokala gateway** – Om en eller flera datakällor finns lokalt i din organisations nätverk måste du installera en [lokal datagateway](analysis-services-gateway.md). Gatewayen är nödvändig för att din server i molnet ska kunna ansluta till dina lokala datakällor för att bearbeta och uppdatera data i modellen.

> [!TIP]
> Innan du distribuerar måste du kontrollera att du kan bearbeta data i dina tabeller. Klicka på > **Modellprocessprocess** > **bearbeta alla**i Visual Studio . **Model** Om bearbetningen misslyckas kan inte du distribuera.
> 
> 

## <a name="get-the-server-name"></a>Hämta servernamnet

Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Så här distribuerar du från Visual Studio

1. Högerklicka på projektet > **Egenskaper**i Visual Studio > **Solution Explorer**. Klistra sedan in servernamnet i **Distribution** > **Server.**   
   
    ![Klistra in servernamnet i egenskapen för distributionsservern](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. I **Solution Explorer** högerklickar du på **Egenskaper** och klickar sedan på **Distribuera**. Du kan uppmanas att logga in i Azure.
   
    ![Distribuera till en server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Distributionens status visas både i fönstret Utmatning och i Distribuera.
   
    ![Status för distribution](./media/analysis-services-deploy/aas-deploy-status.png)

Det var allt!


## <a name="troubleshooting"></a>Felsökning

Om distributionen misslyckas när metadata distribueras beror det sannolikt på att Visual Studio inte kunde ansluta till servern. Kontrollera att du kan ansluta till servern med hjälp av SSMS. Kontrollera egenskapen Distributionsserver för projektet är korrekt.

Om distributionen misslyckas för en tabell beror det förmodligen på att servern inte kunde ansluta till en datakälla. Om datakällan finns lokalt i din organisations nätverk måste du installera en [lokal datagateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en tabellmodell till servern är du redo att ansluta till den. Du kan [ansluta till den med SQL Server Management Studio (SSMS)](analysis-services-manage.md) för att hantera den. Och du kan [ansluta till den med ett klientverktyg](analysis-services-connect.md), till exempel Power BI, Power BI Desktop eller Excel, och börja skapa rapporter.

