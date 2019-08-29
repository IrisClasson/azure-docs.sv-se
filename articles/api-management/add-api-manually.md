---
title: Lägg till ett API manuellt med Azure Portal | Microsoft Docs
description: Den här självstudien visar hur du använder API Management (APIM) för att lägga till ett API manuellt.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 5440333360549c5df2da57c97b24dcc77436ba4b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072708"
---
# <a name="add-an-api-manually"></a>Lägga till ett API manuellt

Stegen i den här artikeln visar hur du använder Azure Portal för att lägga till ett API manuellt till API Management-instansen (APIM). Ett vanligt scenario när du vill skapa ett tomt API och definiera det manuellt är när du vill testa API:et. Mer information om att testa API:er finns i [Testa API-svar](mock-api-responses.md).

Om du vill importera ett befintligt API, se avsnittet [relaterade ämnen](#related-topics).

I den här artikeln, skapar vi ett tomt API och anger [httpbin.org](https://httpbin.org) (en offentlig testtjänst) som serverdels-API.

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Skapa ett API

1. Välj **API: er** under **API-HANTERING**.
2. Välj **+ Lägg till API** på den vänstra menyn.
3. Välj **Tomt API** i listan.

    ![Tomt API](media/add-api-manually/blank-api.png)
4. Ange inställningarna för API:et.

    |**Namn**|**Värde**|**Beskrivning**|
    |---|---|---|
    |**Visningsnamn**|*Tomt API*|Det här namnet visas i Developer-portalen.|
    |**Namn**|*tomt API*|Tillhandahåller ett unikt namn för API:et.|
    |**Webbtjänst-URL** (valfritt)|*https://httpbin.org*| Om du vill testa ett API, kanske du inte skriver in något. <br/>I så fall kan vi ange [https://httpbin.org](https://httpbin.org). Detta är en offentlig testtjänst. <br/>Om du vill importera ett API som mappas till en serverdel automatiskt, se något av ämnena i avsnittet [relaterade ämnen](#related-topics).|
    |**URL-schema**|*HTTPs*|I det här fallet anger vi en säker HTTPS APIM-åtkomst till serverdelen även om serverdelen har icke-säker HTTP-åtkomst. <br/>Den här typen av scenario (HTTPS till HTTP) kallas HTTPS-avslutning. Du kan göra det om ditt API finns inom ett virtuellt nätverk (där du vet att åtkomsten är säker, även om inte HTTPS används). <br/>Du kanske vilja använda HTTPS-avslutning för att spara på CPU-cykler.|
    |**URL-suffix**|*hbin*| Suffixet är ett namn som identifierar det här specifika API:et i den här APIM-instansen. Det måste vara unikt i den här APIM-instansen.|
    |**Produkter|*Obegränsat*|Du kan publicera API:et genom att associera det med en produkt. Om du vill att API:et ska publiceras och vara tillgänglig för utvecklare, lägger du till det till en produkt. Du kan göra det vid API-skapandet eller ställa in det senare.<br/><br/>Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. <br/>Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.<br/><br/> Som standard medföljer två exempelprodukter varje API Management-instans: **Starter** och **Obegränsat**.| 
5. Välj **Skapa**.

För tillfället har du inga åtgärder i APIM som mappar till åtgärderna i ditt serverdels-API. Om du anropar en åtgärd som exponeras via serverdelen men inte via APIM, får du en **404**.

>[!NOTE] 
> Som standard när du lägger till en API, även om den är ansluten till en serverdelstjänst, kommer inte APIM att exponera några åtgärder förrän du vitlistat dem. Om du vill vitlista en åtgärd i din serverdelstjänst, skapar du en APIM-åtgärd som mappar till serverdelsåtgärden.

## <a name="add-and-test-an-operation"></a>Lägg till och testa en åtgärd

Det här avsnittet visar hur du lägger till en /get-åtgärd för att mappa den till serverdelens "http://httpbin.org/get"-åtgärd.

### <a name="add-an-operation"></a>Lägga till en åtgärd

1. Välj det API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärd**.
3. I **URL**, väljer du **GET** och anger */get* i resursen.
4. Ange *FetchData* som **Visningsnamn**.
5. Välj **Spara**.

### <a name="test-an-operation"></a>Testa en åtgärd

Testa åtgärden i Azure Portal. Du kan också testa den i **Utvecklarportalen**.

1. Välj fliken **Test**.
2. Välj **FetchData**.
3. Tryck på **Skicka**.

Svaret som "http://httpbin.org/get"-åtgärden genererar visas. Om du vill omvandla dina åtgärder, se [Omvandla och skydda ditt API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Lägg till och testa en parameteriserad åtgärd

Det här avsnittet visar hur du lägger till en åtgärd som tar en parameter. I det här fallet mappar vi åtgärden till "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Lägg till åtgärden

1. Välj det API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärd**.
3. I **URL**, väljer du **GET** och anger */status/{code}* i resursen. Du kan också kan du ange information som är associerad med den här parametern. Ange till exempel *Nummer* för **Typ**, *200* (standard) för **Värden**.
4. Ange GetStatus som **Visningsnamn**.
5. Välj **Spara**.

### <a name="test-the-operation"></a>Testa åtgärden 

Testa åtgärden i Azure Portal.  Du kan också testa den i **Utvecklarportalen**.

1. Välj fliken **Test**.
2. Välj **GetStatus**. Som standard är kodvärdet satt till *200*. Du kan ändra det för att testa andra värden. Ange till exempel *418*.
3. Tryck på **Skicka**.

    Svaret som "http://httpbin.org/status/200"-åtgärden genererar visas. Om du vill omvandla dina åtgärder, se [Omvandla och skydda ditt API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
