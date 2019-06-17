---
title: IP-Brandvägg för Azure Cosmos-konton
description: Lär dig hur du skyddar Azure Cosmos DB-data med hjälp av IP-principer för åtkomstkontroll för brandvägg.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241083"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-brandvägg i Azure Cosmos DB

Om du vill skydda data som lagras i ditt konto, stöder Azure Cosmos DB en hemlig baserat auktoriseringsmodellen som använder en stark hashbaserad meddelandeautentiseringskod (HMAC). Azure Cosmos DB stöder dessutom IP-baserade åtkomstkontroller för stöd för Brandvägg för inkommande trafik. Den här modellen är liknar brandväggsreglerna för traditionella databassystem och ger en extra nivå av säkerhet för ditt konto. Med brandväggar, kan du konfigurera ditt Azure Cosmos-konto för att endast vara tillgängliga från en godkänd uppsättning datorer och/eller molntjänster. Åtkomst till data som lagras i din Azure Cosmos-databas från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande anroparen att presentera en giltig auktoriseringstoken.

## <a id="ip-access-control-overview"></a>IP-åtkomstkontroll: översikt

Som standard kan Azure Cosmos-kontot kan nås från internet, så länge begäran åtföljs av en giltig auktoriseringstoken. Om du vill konfigurera IP-policy-baserad åtkomstkontroll, måste användaren ange en uppsättning IP-adresser eller IP-adressintervall i CIDR (Classless Inter-Domain Routing) form som ska tas med som listan över tillåtna för klientens IP-adresser för åtkomst till en viss Azure Cosmos-kontot. När den här konfigurationen tillämpas, får alla begäranden från datorer utanför den här listan över tillåtna 403 (förbjudet)-svar. När du använder IP-brandvägg, rekommenderas det att Azure-portalen för att komma åt ditt konto. Åtkomst krävs för att tillåta användning av data explorer samt att hämta mått för ditt konto som visas på Azure portal. När du använder datautforskaren, förutom att Azure-portalen för att komma åt ditt konto måste du också att uppdatera inställningarna för brandväggen för att lägga till din aktuella IP-adress till brandväggsreglerna. Observera att brandväggsändringarna kan ta upp till 15 minuter att sprida. 

Du kan kombinera IP-baserad brandvägg med undernät och VNET-åtkomstkontroll. Genom att kombinera dem kan begränsa du åtkomsten till vilken källa som har en offentlig IP-adress och/eller från ett specifikt undernät i VNET. Mer information om hur du använder undernät och VNET-baserad åtkomstkontroll finns [åtkomst till Azure Cosmos DB-resurser från virtuella nätverk](vnet-service-endpoint.md).

Sammanfattningsvis krävs alltid Autentiseringstoken för åtkomst till ett Azure Cosmos-konto. Om IP-brandvägg och virtuellt nätverk åtkomstkontrollistan (ACL) inte har ställts in, kan Azure Cosmos-konto nås med autentiseringstoken. När IP-brandvägg eller VNET-ACL: er eller båda har konfigurerats för Azure Cosmos-kontot kan få endast förfrågningar som kommer från källor som du har angett (och med autentiseringstoken) giltiga svar. 

## <a name="next-steps"></a>Nästa steg

Sedan konfigurera du IP-brandväggen eller VNET-tjänstslutpunkt för ditt konto med hjälp av följande dokument:

* [Så här konfigurerar du IP-Brandvägg för ditt Azure Cosmos-konto](how-to-configure-firewall.md)
* [Få åtkomst till Azure Cosmos DB-resurser från virtuella nätverk](vnet-service-endpoint.md)
* [Så här konfigurerar du tjänstslutpunkt för virtuellt nätverk för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md)




