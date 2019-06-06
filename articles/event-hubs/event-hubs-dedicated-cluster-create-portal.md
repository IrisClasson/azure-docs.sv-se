---
title: Azure Snabbstart – skapa ett dedikerat Event Hubs-kluster med Azure-portalen | Microsoft Docs
description: I den här snabbstarten har du lära dig hur du skapar ett Azure Event Hubs-kluster med Azure-portalen.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/02/2019
ms.author: xurui
ms.openlocfilehash: 269ecca8683229a56d40dfacc354abbd7ce10762
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688580"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Snabbstart: Skapa ett dedikerat Event Hubs-kluster med Azure-portalen 
Event Hubs-kluster ger en enda klient distributioner för kunder med strömmande behoven. Det här erbjudandet har en garanterad serviceavtal på 99,99% och är endast tillgänglig på vår dedikerade prisnivå. En [Event Hubs-kluster](event-hubs-dedicated-overview.md) skicka flera miljoner händelser per sekund med en garanterad kapacitet och subsecond fördröjning. Namnområden och event hubs som skapats i ett kluster innehåller alla funktioner på standard-erbjudandet och mycket mer, men utan några ingående begränsningar. Dedikerad erbjudandet omfattar även populära [Event Hubs Capture](event-hubs-capture-overview.md) funktionen utan extra kostnad, så att du kan automatiskt batch- och log dataströmmar till [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) eller [ Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Dedikerade kluster etablerats och faktureras per **kapacitetsenheter (Cu: er)** , en förallokerad mängden Processortid och minnesresurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 Cu: er för varje kluster. I denna Snabbstart vägleder vi dig genom att skapa ett 1 Kapacitetsenhet Event Hubs-kluster via Azure portal.

> [!NOTE]
> Den här självbetjäning är för närvarande tillgängligt i förhandsversionen av [Azure-portalen](https://aka.ms/eventhubsclusterquickstart). Om du har frågor om dedikerade erbjudandet kan kontakta den [Event Hubs-teamet](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här snabbstarten behöver du följande:

- Ett Azure-konto. Om du inte har någon, [Köp ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar. Den här funktionen stöds inte med ett kostnadsfritt Azure-konto. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 uppdatering 3 (version 15.3, 26730.01) eller senare.
- [SDK för .NET Standard](https://dotnet.microsoft.com/download) version 2.0 eller senare.
- [Skapade resursgruppen](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Skapa ett Event Hubs Dedicated-kluster
Ett Event Hubs-kluster tillhandahåller en unik omfångsbehållare där du kan skapa ett eller flera namnområden. I den här förhandsvisningsfasen av en Portal för självbetjäning kan du skapa 1 CU-kluster i utvalda regioner. Om du behöver ett kluster större än 1 Kapacitetsenhet, kan du skicka en supportförfrågan för Azure att skala upp ditt kluster när den har skapandet.

Om du vill skapa ett kluster i resursgruppen med hjälp av Azure portal, gör du följande:

1. Följ [den här länken](https://aka.ms/eventhubsclusterquickstart) att skapa ett kluster på Azure-portalen. Välj däremot **alla tjänster** från det vänstra navigeringsfönstret, skriver in ”Event Hubs-kluster” i sökfältet och välj ”Event Hubs-kluster” i listan över resultat.
2. På den **Skapa kluster** konfigurerar du följande:
    1. Ange en **namn för klustret**. Systemet kontrollerar omedelbart om namnet är tillgängligt.
    2. Välj den **prenumeration** som du vill skapa klustret.
    3. Välj den **resursgrupp** som du vill skapa klustret.
    4. Välj en **plats** för klustret. Om din önskade region är nedtonad, det är tillfälligt kapacitet och du kan skicka en [supportförfrågan](#submit-a-support-request) till Event Hubs-teamet.
    5. Välj den **nästa: Taggar** längst ned på sidan. Du kan behöva vänta några minuter på att systemet ska bli klart med att etablera resurserna.

        ![Skapa Event Hubs-kluster - grunderna sidan](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. På den **taggar** konfigurerar du följande:
    1. Ange en **namn** och en **värdet** för taggen du vill lägga till. Det här steget är **valfritt**.  
    2. Välj den **granska + skapa** knappen.

        ![Skapa Event Hubs-kluster - sidan taggar](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. På den **granska + skapa** , granska informationen och välj **skapa**. 

    ![Skapa Event Hubs-kluster sidan – Granska + Skapa sida](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Skapa ett namnområde och en händelsehubb i ett kluster

1. Att skapa ett namnområde i ett kluster på den **Event Hubs-kluster** för klustret, väljer **+ Namespace** på den översta menyn.

    ![Sidan för hantering av kluster – lägga till knappen för namnområde](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. På sidan Skapa en namnrymd, gör du följande:
    1. Ange ett **namn för namnrymden**.  Systemet kontrollerar om namnet är tillgängligt.
    2. Namnområdet ärver följande egenskaper:
        1. Prenumerations-ID:t
        2. Resursgrupp
        3. Location
        4. Klusternamn
    3. Välj **skapa** att skapa namnområdet. Nu kan du hantera klustret.  

        ![Skapa namnområde i kluster-sidan](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. När namnområdet har skapats kan du [skapar en event hub](event-hubs-create.md#create-an-event-hub) som du normalt skapar en i ett namnområde. 


## <a name="submit-a-support-request"></a>Skicka en supportförfrågan

Om du vill ändra storlek på klustret efter skapandet eller om din önskade region inte är tillgänglig skicka en supportförfrågan genom att följa dessa steg:

1. I [Azure-portalen](https://portal.azure.com)väljer **hjälp + support** menyn till vänster.
2. Välj **+ ny supportbegäran** Support-menyn.
3. Gör följande på supportsidan:
    1. För **ärendetypen**väljer **teknisk** från den nedrullningsbara listan.
    2. I fältet **Prenumeration** väljer du din prenumeration.
    3. För **Service**väljer **Mina tjänster**, och välj sedan **Händelsehubbar**.
    4. För **Resource**, väljer klustret om den redan finns, annars väljer **allmän fråga/resursen inte tillgänglig**.
    5. För **problemtyp**väljer **kvot**.
    6. För **problemet undertyp**, väljer du något av följande värden från den nedrullningsbara listan:
        1. Välj **begäran för dedikerade SKU** att begära för att funktionen ska användas i din region.
        2. Välj **begäran om att skala upp eller skala ned dedikerade kluster** om du vill skala eller ned din dedikerade kluster. 
    7. För **ämne**, Beskriv problemet.

        ![Supportsidan för biljett](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Ta bort ett dedikerat kluster
 
1. Om du vill ta bort klustret, Välj **ta bort** på den översta menyn. Observera att klustret kommer att debiteras för minst 4 timmars användning när du har skapat. 
2. Ett meddelande visas bekräftar din vill ta bort klustret.
3. Skriv den **namnet på klustret** och välj **ta bort** ta bort klustret.

    ![Ta bort klustret sida](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Nästa steg
I den här artikeln har skapat du ett Event Hubs-kluster. Stegvisa instruktioner för att skicka och ta emot händelser från en event hub och samla in händelser till en Azure storage eller Azure Data Lake Store, finns i följande Självstudier:

- [Skicka och ta emot händelser på .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Aktivera Event Hubs Capture med hjälp av Azure portal](event-hubs-capture-enable-through-portal.md)
- [Använd Azure Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
