---
title: Redigera ett API med Azure Portal | Microsoft Docs
description: 'Lär dig hur du använder API Management (APIM) för att redigera ett API. Lägg till, ta bort eller Byt namn på åtgärder i APIM-instansen eller redigera API: n för Swagger.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 8d3f476f4a6ecf05d63223f3ba2c2b5173e1d0bf
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905167"
---
# <a name="edit-an-api"></a>Redigera ett API

Stegen i den här självstudien visar hur du använder API Management (APIM) för att redigera ett API. 

+ Du kan göra det genom att lägga till, ta bort, byta namn på åtgärderna i APIM-instansen. 
+ Du kan redigera din API:s swagger.

## <a name="prerequisites"></a>Förutsättningar

+ [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ [Importera och publicera ditt första API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Redigera ett API i APIM

![Redigera ett API](./media/edit-api/edit-api001.png)

1. Klicka på fliken **API:er**.
2. Välj ett av de API:er som du tidigare har importerat.
3. Välj fliken **Design**.
4. Välj en åtgärd som du vill redigera.
5. Byt namn på åtgärden genom att markera en **penna** i **klientdel**-fönstret.

## <a name="update-the-swagger"></a>Uppdatera swagger

Du kan uppdatera ditt serverdels-API från Azure Portal genom att följa dessa steg:

1. Välj **Alla åtgärder**
2. Klicka på penna i **klientdel**-fönstret.

    ![Redigera ett API](./media/edit-api/edit-api002.png)

    Ditt API:s swagger visas.

    ![Redigera ett API](./media/edit-api/edit-api003.png)

3. Uppdatera swagger.
4. Tryck på **Spara**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [APIM princip exempel](policy-samples.md) 
>  [Transformera och skydda ett publicerat API](transform-api.md)