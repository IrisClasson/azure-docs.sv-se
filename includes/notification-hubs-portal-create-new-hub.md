---
title: inkludera fil
description: inkludera fil
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67509072"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **alla tjänster** på den vänstra menyn och välj sedan **Notification Hubs** i avsnittet **mobil** . Välj stjärn ikonen bredvid tjänst namnet för att lägga till tjänsten i **Favoriter** -avsnittet på den vänstra menyn. När du har lagt till **Notification Hubs** i **Favoriter**väljer du den på den vänstra menyn.

      ![Azure-portalen – välj Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet.

      ![Notification Hubs – Lägga till verktygsfältsknapp](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. På sidan **Notification Hub** gör du följande:

    1. Ange ett namn i **Notification Hub**.  

    1. Ange ett namn i **skapa ett nytt namn område**. En namnrymd innehåller en eller flera hubbar.

    1. Välj ett värde i list rutan **plats** . Det här värdet anger den plats där du vill skapa hubben.

    1. Välj en befintlig resurs grupp i **resurs gruppen**eller skapa ett namn för en ny resurs grupp.

    1. Välj **Skapa**.

        ![Azure Portal – ange egenskaper för meddelandehubben](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Välj **meddelanden** (klock ikonen) och välj sedan **gå till resurs**. Du kan också uppdatera listan på sidan **Notification Hubs** och välja hubben.

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Välj **Åtkomstprinciper** i listan. Observera att de två anslutnings strängarna är tillgängliga för dig. Du behöver dem senare för att hantera push-meddelanden.

      >[!IMPORTANT]
      >Använd *inte* **DefaultFullSharedAccessSignature** -principen i ditt program. Detta är endast avsett att användas i Server delen.
      >

      ![Azure Portal – anslutningssträngar för meddelandehubb](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
