---
title: Importera och publicera ditt första API i Azure API Management | Microsoft Docs
description: Lär dig hur du importerar och publicerar ditt första API med API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: 6a1ae2966e8d5535a5fd9aeffb5ddc3a788f85ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072108"
---
# <a name="import-and-publish-your-first-api"></a>Importera och publicera ditt första API 

I den här kursen visas hur du importerar en OpenAPI-specifikation för serverdels-API som finns på https://conferenceapi.azurewebsites.net?format=json. Detta serverdels-API tillhandahålls av Microsoft och finns i Azure. 

När serverdels-API:et importeras i API Management (APIM) blir APIM-API:et en fasad för serverdels-API:et. När du importerar serverdels-API:et är käll-API:et och APIM-API:et identiska. Med APIM kan du anpassa fasaden efter dina behov utan att behöva röra serverdels-API:et. Mer information finns i [Transformera och skydda ditt API](transform-api.md). 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera ditt första API
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

![Nytt API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importera och publicera ett serverdels-API

I det här avsnittet visas hur du importerar och publicerar en OpenAPI-specifikation för serverdels-API.
 
1. Välj **API: er** under **API-HANTERING**.
2. Välj **OpenAPI-specifikation** i listan och klicka på **Fullständig** i popup-fönstret.

    ![Skapa ett API](./media/api-management-get-started/create-api.png)

    Du kan konfigurera API-värdena under skapandet eller senare genom att gå till fliken **Inställningar**. Den röda stjärnan bredvid ett fält anger att fältet är obligatoriskt.

    Använd värdena i tabellen nedan för att skapa ditt första API.

    | Inställning                   | Värde                                              | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **OpenAPI-specifikation** | https://conferenceapi.azurewebsites.net?format=json | Refererar till tjänsten som implementerar API:et. API-hanteringen vidarebefordrar begäranden till den här adressen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Visningsnamn**          | *Demokonferens-API*                              | Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON. <br/>Det här namnet visas i Developer-portalen.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Namn**                  | *demo-conference-api*                              | Tillhandahåller ett unikt namn för API:et. <br/>Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Beskrivning**           | Ange en valfri beskrivning av API: et.        | Om du trycker på fliken när du har angett tjänstens URL fyller APIM i det här fältet baserat på vad som finns i JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **URL-schema**            | *HTTPS*                                            | Fastställer vilka protokoll som kan användas för att få åtkomst till API:et.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **API URL-suffix**        | *konferens*                                       | Suffixet läggs till i API-hanteringstjänstens bas-URL. API Management skiljer API:erna åt med hjälp av deras suffix, och suffixet måste därför vara unikt för alla API:er för en viss utgivare.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Produkter**              | *Obegränsat*                                        | Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er i en produkt och erbjuda dem till utvecklare via utvecklarportalen. <br/>Du kan publicera API:et genom att associera det med en produkt (i det här exemplet *Unlimited*). Om du vill lägga till detta nya API till en produkt, så ange produktens namn (du kan också göra det senare från sidan **Inställningar**). Det här steget kan du upprepa flera gånger för om du ska lägga till API:et till flera produkter.<br/>För att få åtkomst till API:et måste utvecklarna först prenumerera på en produkt. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. <br/> Om du har skapat APIM-instansen är du redan administratör, vilket innebär att du prenumererar på alla produkter.<br/> Som standard medföljer två exempelprodukter varje API Management-instans: **Starter** och **Obegränsat**. |
    | **Taggar**                  |                                                    | Taggar för att organisera API:er. Taggar kan användas för sökning, gruppering och filtrering.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Version av det här API:et?**     |                                                    | Om du vill ha mer versionsinformation kan du gå till [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Om du vill publicera API:t måste du associera det med en produkt. Du kan göra det från sidan **Inställningar**.

3. Välj **Skapa**.

> [!TIP]
> Om du påträffar problem med import av din egen API-definition kan du [läsa listan över kända problem och begränsningar](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testa det nya APIM API:et i Azure Portal

![Testa API-karta](./media/api-management-get-started/01-import-first-api-01.png)

Du kan anropa åtgärder direkt från Azure Portal, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API.

1. Välj det API som du skapade i föregående steg (från fliken **API:er**).
2. Tryck på fliken **Test**.
3. Klicka på **GetSpeakers**. På sidan visas fälten för frågeparametrar (inga i det här fallet) och huvuden. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Nyckeln fylls i automatiskt.
4. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

## <a name="call-operation"> </a>Anropa en åtgärd från utvecklarportalen

Åtgärder kan också anropas från **utvecklarportalen** för att testa API:er.

1. Gå till **utvecklarportalen**.

    ![Utvecklarportal](./media/api-management-get-started/developer-portal.png)

2. Välj **APIS** (API:er), klicka på **Demo Conference API** (Demokonferens-API) och sedan på **GetSpeakers**.

    På sidan visas fälten för frågeparametrar (inga i det här fallet) och huvuden. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade APIM-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt.

3. Tryck på **Testa**.
4. Tryck på **Skicka**.

    När en åtgärd har anropats visar utvecklarportalen svaren.  

## <a name="next-steps"> </a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera ditt första API
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa och publicera en produkt](api-management-howto-add-products.md)
