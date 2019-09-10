---
title: Översikt över Azure blockchain Workbench Preview
description: Översikt över Azure blockchain Workbench Preview och dess funktioner.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 097185502321c8810214ed737047bdf596d18bdb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844099"
---
# <a name="what-is-azure-blockchain-workbench"></a>Vad är Azure Blockchain Workbench?

Azure blockchain Workbench Preview är en samling Azure-tjänster och-funktioner som är utformade för att hjälpa dig att skapa och distribuera blockchain-program för att dela affärs processer och data med andra organisationer. Azure Blockchain Workbench tillhandahåller infrastrukturen för att skapa blockkedjeprogram och gör det möjligt för utvecklarna att fokusera på att skapa affärslogik och smarta kontrakt. Dessutom blir det enklare att skapa blockkedjeprogram genom att integrera flera Azure-tjänster och funktioner så att vanliga utvecklingsuppgifter kan automatiseras.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Skapa blockkedjeprogram

Med Blockchain Workbench kan du definiera blockkedjeprogram genom konfiguration och att skriva smart kontraktkod. Du kan rivstarta utvecklingen av blockkedjeprogram och fokusera på att definiera ditt kontrakt och skriva affärslogik istället för att skapa scaffold-teknik och konfigurera stödtjänster.

## <a name="manage-applications-and-users"></a>Hantera program och användare

Azure Blockchain Workbench innehåller ett webbprogram och REST API:er för att hantera blockkedjeprogram och användare. Blockchain Workbench-administratörer kan hantera programåtkomst och tilldela dina användare programroller. Azure AD-användare mappas automatiskt till medlemmar i programmet.

## <a name="integrate-blockchain-with-applications"></a>Integrera blockkedja med program

Du kan integrera med befintliga system genom att använda Blockchain Workbench REST API:er och meddelandebaserade API:er. API:erna tillhandahåller ett gränssnitt som gör det möjligt att ersätta eller använda flera tekniker för distribuerad redovisning, lagring och databaserbjudanden.

Blockchain Workbench kan omvandla meddelanden som skickats till dess meddelandebaserade API och skapa transaktioner i ett format som förväntas av blockkedjans egna API.  Workbench kan logga in och vidarebefordra transaktioner till lämplig blockkedja. 

Workbench levererar automatiskt händelser till Service Bus och Event Grid så att meddelanden kan skickas till underordnade konsumenter. Utvecklarna kan integrera med endera av dessa meddelandesystem för att driva transaktioner och studera resultaten.

## <a name="deploy-a-blockchain-network"></a>Distribuera ett blockkedjenätverk

Azure Blockchain Workbench förenklar konfigurationen av blockkedjenätverket som en förkonfigurerad lösning med en Azure Resource Manager-lösningsmall. Mallen tillhandahåller en förenklad distribution som distribuerar alla komponenter som behövs för att köra ett konsortium. Blockchain Workbench stöder för närvarande Ethereum.

## <a name="use-active-directory"></a>Använd Active Directory

Med befintliga blockkedjeprotokoll representeras blockkedjeidentiteterna som en adress i nätverket. Azure Blockchain Workbench avlägsnar direkt blockkedjeidentiteten genom att associera den med en Active Directory-identitet, vilket gör det enklare att skapa företagsprogram med Active Directory-identiteter.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Synkronisera kedjedata med lagring utanför kedjan

Azure Blockchain Workbench gör det lättare att analysera blockkedjehändelser och data genom att automatiskt synkronisera data i blockkedjan med lagring utanför kedjan. I stället för att extrahera data direkt från blockkedjan kan du fråga databassystem utanför kedjan som t.ex. SQL Server. Blockchain-expertis krävs inte för slutanvändare som utför data analys uppgifter.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench-arkitektur](architecture.md)
