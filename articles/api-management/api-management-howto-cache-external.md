---
title: Använda en extern cache i Azure API Management | Microsoft Docs
description: Lär dig hur du konfigurerar och använder en extern cache i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072493"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Använda en extern Azure Cache for Redis i Azure API Management

Utöver att använda den inbyggda cachen kan Azure API Management även användas för cachelagring av svar i en extern Azure Cache for Redis.

Användning av en extern cache kan lösa vissa begränsningar i den inbyggda cachen. Det är särskilt användbart om du vill:

* Undvika att ditt cacheminne rensas med jämna mellanrum API Management-uppdateringar
* Få mer kontroll över din cache-konfiguration
* Cachelagra större mängder data än vad din API Management-nivå tillåter
* Använda cachelagring med förbrukningsnivån för API Management

Mer detaljerad information om cachelagring finns i [Principer för cachelagring för API Management](api-management-caching-policies.md) och [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md)

![Ta din egen cache till APIM](media/api-management-howto-cache-external/overview.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * Lägga till en extern cache i API Management

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

+ [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ Förstå [cachelagring i Azure API Management](api-management-howto-cache.md)

## <a name="create-cache"> </a> Skapa Azure Cache for Redis

Det här avsnittet beskriver hur du skapar en Azure Cache for Redis i Azure. Om du redan har en Azure Cache for Redis i eller utanför Azure kan du <a href="#add-external-cache">hoppa över</a> detta till nästa avsnitt.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Lägga till en extern cache

Följ stegen nedan om du vill lägga till en extern Azure Cache for Redis i Azure API Management.

![Ta din egen cache till APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Inställningen **Använd från** anger vilken API Management regional-distributionen kommer att kommunicera med den konfigurerade cachen i händelse av en multi-regional konfiguration av API Management. Den cache som anges som **standard** åsidosätts av cacher med ett regionalt värde.
>
> Om till exempel API Management hanteras i regionerna USA, östra, Asien, sydöstra och Europa, västra och det finns två cacher konfigurerade, en för **Standard** och en för **Asien, sydöstra**, använder API Management i **Asien, sydöstra** sin egen cache, medan de andra två regionerna använder cacheposten **Standard**.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Lägga till en Azure Cache for Redis från samma prenumeration

1. Bläddra till API Management-instansen i Azure-portalen.
2. Välj fliken **Extern cache** på menyn till vänster.
3. Klicka på knappen **+ Lägg till**.
4. Välj din cache i det nedrullningsbara fältet **Cacheinstans**.
5. Välj **standard** eller ange önskad region i list rutan **Använd från** .
6. Klicka på **Spara**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Lägga till en Azure Cache for Redis som hanteras utanför den aktuella Azure-prenumerationen eller Azure i allmänhet

1. Bläddra till API Management-instansen i Azure-portalen.
2. Välj fliken **Extern cache** på menyn till vänster.
3. Klicka på knappen **+ Lägg till**.
4. Välj **Anpassad** i det nedrullningsbara fältet **Cacheinstans**.
5. Välj **standard** eller ange önskad region i list rutan **Använd från** .
6. Ange din anslutningssträng för Azure Cache for Redis i fältet **Anslutningssträng**.
7. Klicka på **Spara**.

## <a name="use-the-external-cache"></a>Använda den externa cachen

När den externa cachen har konfigurerats i Azure API Management kan den användas via cachelagringsprinciper. Detaljerade steg finns i avsnittet om att [lägga till cachelagring för att förbättra prestanda i Azure API Management](api-management-howto-cache.md).

## <a name="next-steps"> </a>Nästa steg

* Mer information om cachelagringsprinciper finns i [Cachelagringsprinciper][Caching policies] i [Principreferens för API Management][API Management policy reference].
* Mer information om hur du cachelagrar objekt med nycklar med hjälp av principuttryck finns i [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
