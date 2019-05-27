---
title: Åtkomst till ändringen feed i Azure Cosmos DB Azure Cosmos DB
description: Den här artikeln beskrivs olika alternativ som är tillgängliga för att läsa och komma åt ändringsflödet i Azure Cosmos DB Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: e008b44ee2859f319d0250658d7c2beb190af1c2
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967167"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Läsa Azure Cosmos DB-ändringsflödet

Du kan arbeta med Azure Cosmos DB-ändringsflödet med hjälp av något av följande alternativ:

* Använd Azure Functions
* Med hjälp av ändringen feed processor-biblioteket
* Med hjälp av Azure Cosmos DB SQL API-SDK

## <a name="using-azure-functions"></a>Använd Azure Functions

Azure Functions är det enklaste och rekommenderade alternativet. När du skapar en Azure Cosmos DB-utlösare i ett program för Azure Functions kan du kan välja behållaren för att ansluta och Azure-funktion aktiveras när en ändring till behållaren. Utlösare kan skapas med hjälp av Azure Functions-portalen, Azure Cosmos DB-portalen eller via programmering med SDK: er. Visual Studio och VS Code ger support för att skriva Azure Functions och du kan även använda Azure Functions CLI för plattformsoberoende utveckling. Du kan skriva och felsöka koden på skrivbordet och sedan distribuera en funktion med ett enda klick. Se [databas utan Server databehandling med Azure Functions](serverless-computing-database.md) och [med ändringen feed med Azure Functions](change-feed-functions.md)) artiklar om du vill veta mer.

## <a name="using-the-change-feed-processor-library"></a>Med hjälp av ändringen feed processor-biblioteket

Processor-biblioteket för ändringsflödet döljer komplexiteten och fortfarande får du en fullständig kontroll över den ändringsflödet. Biblioteket följer mönstret Övervakare där bearbetning funktionen anropas av biblioteket. Om du har en hög genomströmning ändringsflödet kan du kan skapa en instans av flera klienter för att läsa ändringsflöde. Eftersom du använder ändringsflödet processor-biblioteket, delar den belastningen mellan de olika klienterna automatiskt utan att du behöver att implementera den här logiken. Alla komplexitet hanteras av biblioteket. Om du vill ha en egen belastningsutjämnare så du kan implementera `IPartitionLoadBalancingStrategy` för en anpassad partition feed-strategi för att bearbeta ändringar. Mer information finns i [med ändringen feed processor-biblioteket](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Med hjälp av Azure Cosmos DB SQL API-SDK

Med SDK: N får du en på låg nivå kontroll av ändringen feed. Du kan hantera kontrollpunkten, få åtkomst till en viss logisk partition nyckel, osv. Om du har flera läsare kan du använda `ChangeFeedOptions` att distribuera skrivskyddade belastning till olika trådar eller olika klienter. 

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Översikt över ändringsfeed](change-feed.md)
* [Med hjälp av ändringen feed med Azure Functions](change-feed-functions.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
