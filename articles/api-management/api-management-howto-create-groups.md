---
title: Hantera utvecklarkonton med hjälp av grupper i Azure API Management | Microsoft Docs
description: Lär dig att hantera konton med hjälp av grupper i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658468"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Hur du skapar och använda grupper för att hantera utvecklarkonton i Azure API Management

I API Management används grupper för att hantera hur produkter visas för utvecklare. Produkter görs först synliga för grupper och sedan utvecklare i dessa grupper kan visa och prenumerera på produkter som är associerade med grupper. 

API Management har följande systemgrupper som inte kan ändras:

* **Administratörer** – Administratörer av Azure-prenumerationer är medlemmar i den här gruppen. Administratörer hanterar API Management-tjänstinstanser genom att skapa API:er, åtgärder och produkter som används av utvecklare.
* **Utvecklare** – Autentiserade användare av utvecklarportalen hör till den här gruppen. Utvecklare är de kunder som utvecklar program med hjälp av dina API:er. Utvecklare beviljas åtkomst till utvecklarportalen och bygger program som anropar åtgärderna i ett API.
* **Gäster** – Oautentiserade användare av utvecklarportalen. Potentiella kunder som besöker utvecklarportalen för en API Management-instans hör t.ex. till den här gruppen. De kan beviljas viss skrivskyddad åtkomst, t.ex. möjligheten att visa API:er men inte anropa dem.

Utöver dessa systemgrupper kan administratörer skapa anpassade grupper eller [använda externa grupper i tillhörande Azure Active Directory-klienter][leverage external groups in associated Azure Active Directory tenants]. Anpassade och externa grupper kan användas tillsammans med systemgrupper för att välja vilka utvecklare som kan se och komma åt API-produkter. Du kan till exempel skapa en anpassad grupp för utvecklare som hör till en specifik partnerorganisation och ge dem åtkomst till API:erna från en produkt som endast innehåller relevanta API:er. En användare kan tillhöra mer än en grupp.

Den här guiden beskriver hur administratörer av en API Management-instans kan lägga till nya grupper och koppla dem till produkter och utvecklare.

Förutom att skapa och hantera grupper i publisher-portalen, kan du skapa och hantera grupper med hjälp av API Management REST API [grupp](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitet.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Utför uppgifterna i den här artikeln: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Skapa en grupp

Det här avsnittet visar hur du lägger till en ny grupp till ditt API Management-konto.

1. Välj den **grupper** fliken till vänster på skärmen.
2. Klicka på **+ Lägg till**.
3. Ange ett unikt namn för gruppen och en valfri beskrivning.
4. Tryck på **Skapa**.

    ![Lägg till en ny grupp](./media/api-management-howto-create-groups/groups001.png)

När gruppen har skapats läggs den till den **grupper** lista. <br/>Så här redigerar du den **namn** eller **beskrivning** i gruppen, klicka på namnet på gruppen och **inställningar**.<br/>Om du vill ta bort gruppen, klicka på namnet på gruppen och tryck på **ta bort**.

Nu när gruppen skapas kan det vara associerad med produkter och utvecklare.

## <a name="associate-group-product"> </a>Associera en grupp med en produkt

1. Välj den **produkter** fliken till vänster.
2. Klicka på namnet på produkten.
3. Tryck på **åtkomstkontroll**.
4. Klicka på **+ Lägg till grupp**.

    ![Associera en grupp med en produkt](./media/api-management-howto-create-groups/groups002.png)
5. Välj den grupp som du vill lägga till.

    ![Associera en grupp med en produkt](./media/api-management-howto-create-groups/groups003.png)

    Om du vill ta bort en grupp från produkt, klickar du på **ta bort**.

    ![Ta bort en grupp](./media/api-management-howto-create-groups/groups004.png)

När en produkt är associerad med en grupp kan kan utvecklare i gruppen visa och prenumerera på produkten.

> [!NOTE]
> Om du vill lägga till Azure Active Directory-grupper, se [så auktorisera konton med Azure Active Directory i Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Associera grupper med utvecklare

Det här avsnittet visar hur du associera grupper med medlemmar.

1. Välj den **grupper** fliken till vänster på skärmen.
2. Välj **medlemmar**.

    ![Lägg till medlem](./media/api-management-howto-create-groups/groups005.png)
3. Tryck på **+ Lägg till** och välj en medlem.

    ![Lägg till medlem](./media/api-management-howto-create-groups/groups006.png)
4. Tryck på **Välj**.

När kopplingen har lagts till mellan utvecklare och gruppen, kan du visa den i den **användare** fliken.

## <a name="next-steps"> </a>Nästa steg

* När en utvecklare har lagts till i en grupp kan de visa och prenumerera på produkter som är associerade med den gruppen. Mer information finns i [skapa och publicera en produkt i Azure API Management][How create and publish a product in Azure API Management],
* Förutom att skapa och hantera grupper i publisher-portalen, kan du skapa och hantera grupper med hjälp av API Management REST API [grupp](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entitet.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
