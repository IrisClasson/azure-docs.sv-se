---
title: Jämföra olika labbtyper i Azure Lab Services | Microsoft Docs
description: Beskriver och jämför olika typer av labb som du kan skapa med hjälp av Azure Lab Services
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cf779f203850ca03942ba2395baf07412712610
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092977"
---
# <a name="compare-managed-labs-in-azure-lab-services-and-devtest-labs"></a>Jämföra hanterade labb och DevTest Labs i Azure Lab Services
Du kan skapa två typer av labb, **hanterade labb** med Azure Lab Services och **anpassade labb** med Azure DevTest Labs. Om du bara vill ange precis vad du behöver i ett labb och låta tjänsten konfigurera och hantera den infrastruktur som krävs för labbet, väljer du något av de **hanterade labben**. För närvarande är **klassrumslabb** den enda typen av hanterat labb som du kan skapa med Azure Lab Services. Om du vill hantera din egen infrastruktur kan du skapa ett labb med hjälp av Azure DevTest Labs.

Följande avsnitt innehåller mer information om dessa labb. 

## <a name="managed-lab-types"></a>Hanterade labbtyper
Med Azure Lab Services kan du skapa laboratorier vars infrastruktur hanteras av Azure. I den här artikeln kallas de för hanterade labb. Hanterade labb bjuder på olika typer av labb som passar för dina specifika behov. Den enda typen av hanterade labb som stöds är för närvarande **klassrumslabb**. 

Med hanterade labb kommer du igång direkt med minimal konfigurering. Tjänsten hanterar all infrastrukturhantering för labbet, från att skapa virtuella datorer till att hantera fel och skala infrastrukturen. För att kunna skapa ett hanterat labb, till exempel ett klassrumslabb, måste du först skapa ett labbkonto för din organisation. Labbkontot fungerar som det centrala kontot där alla labb i organisationen hanteras. 

När du skapar och använder Azure-resurser i dessa hanterade labb, skapar och hanterar tjänsten resurser i interna Microsoft-prenumerationer. De skapas inte i din egen Azure-prenumeration. Tjänsten håller reda på användningen av dessa resurser i interna Microsoft-prenumerationer. Denna användning faktureras tillbaka till din Azure-prenumeration som innehåller labbkontot.   

Här följer några av **användningsfallen för hanterade labb**: 

- Förse eleverna med ett labb med virtuella datorer som är konfigurerat med exakt det som behövs för en lektion. Ge varje elev ett begränsat antal timmar för användning av de virtuella datorerna för läxor eller personliga projekt.
- Konfigurera en pool med högpresterande virtuella datorer för att utföra beräkningsintensiv eller grafikintensiv research. Kör de virtuella datorerna efter behov och rensa datorerna när du är klar. 
- Flytta skolans fysiska datorlabb till molnet. Skala automatiskt antalet virtuella datorer endast efter den maximala användning och de kostnadströsklar som du anger för labbet.  
- Etablera snabbt ett labb med virtuella datorer som värd för en hackathon. Ta bort labbet med en enda klickning när du är klar. 


## <a name="devtest-labs"></a>DevTest Labs
Du kan ha scenarier där du vill hantera hela infrastrukturen och konfigurationen själv, inom ramarna för din egen prenumeration. Om du vill göra det kan du skapa ett labb med Azure DevTest Labs i Azure-portalen. För de här labben behöver du inte skapa ett labbkonto. De här labben visas inte på labbkontot (som finns till för de hanterade labben).  

Här följer några **användningsfall för DevTest Labs**: 

- Etablera snabbt ett labb med virtuella datorer som värd för en hackathon eller en praktisk session på en konferens. Ta bort labbet med en enda klickning när du är klar. 
- Skapa en pool med virtuella datorer med ditt program och låt ditt team enkelt använda en enda virtuell dator för samtidig testning av ej släppt programvara.  
- Förse utvecklare med virtuella datorer som konfigurerats med alla de verktyg som de behöver. Schemalägg automatisk start och avstängning för att minimera kostnaderna. 
- Skapa vid upprepade tillfällen ett labb med testdatorer som en del av distributionen. Testa dina senaste delar och rensa testdatorerna när du är klar. 
- Konfigurera flera olika olikt konfigurerade virtuella datorer och flera testagenter för skalning och prestandatester. 
- Erbjud utbildningssessioner till dina kunder med ett labb som konfigurerats med den senaste versionen av din produkt. Ge varje kund ett begränsat antal timmar för att använda labbet. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Hanterade labbtyper jämfört med DevTest Labs
I följande tabell jämförs två typer av labb som stöds av Azure Lab Services: 

| Funktioner | Hanterade labb | DevTest Labs |
| -------- | ----------------- | ---------- |
| Hantering av Azure-infrastrukturen i labbet |  Hanteras automatiskt av tjänsten | Du hanterar på egen hand  |
| Inbyggd motståndskraft mot infrastruktursproblem | Hanteras automatiskt av tjänsten | Du hanterar på egen hand  |
| Prenumerationshantering | Tjänsten hanterar tilldelning av resurser i Microsoft-prenumerationer som stöder tjänsten. Skalning hanteras automatiskt av tjänsten | Du hanterar det på egen hand i din egen Azure-prenumeration. Ingen automatisk skalning av prenumerationer |
| Azure Resource Manager-distribution i labbet | Inte tillgängligt | Tillgängligt |

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](classroom-labs/tutorial-setup-classroom-lab.md)
- [Konfigurera ett labb](tutorial-create-custom-lab.md)
