---
title: Om Azure Lab Services | Microsoft Docs
description: Lär dig hur Lab Services kan göra det enkelt att skapa, hantera och skydda labb med virtuella datorer som kan användas av utvecklare, testare, lärare, elever och andra.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: 7661b26d16eb2c2acd53c30889ea791689c1eec0
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85442813"
---
# <a name="an-introduction-to-azure-lab-services"></a>En introduktion till Azure Lab Services
Med **Azure Lab Services** kan du skapa labb vars infrastruktur hanteras av Azure. För närvarande är klass rums labb den enda typen av hanterade labb som stöds av Azure Lab Services. Tjänsten hanterar all infrastruktur hantering för en hanterad labb typ, från att skapa virtuella datorer för att hantera fel och skala infrastrukturen. När en IT-administratör har skapat ett labb konto i Azure Lab Services kan en lärare snabbt konfigurera ett labb för klassen, ange antal och typ av virtuella datorer som behöver övningar i klassen och lägga till användare i klassen. När en användare har registrerat sig för klassen, kan användaren komma åt den virtuella datorn för att göra övningar för klassen.  

## <a name="key-capabilities"></a>De viktigaste funktionerna
Azure Lab Services stöder följande viktiga funktioner:

- **Snabb och flexibel konfiguration av ett labb**. Med Azure Lab Services kan labbägare snabbt konfigurera ett labb efter behov. Tjänsten erbjuder alternativet att ta hand om all Azure-infrastruktur som fungerar för hanterade labb typer. Tjänsten tillhandahåller inbyggd skalning och motståndskraftig infrastruktur för labb som tjänsten hanterar åt dig.
- **Förenklad upplevelse för labbanvändare**. Labb användare kan registrera sig för ett labb med en registrerings kod och kan komma åt labbet när som helst för att använda Labbets resurser. 
- **Kostnadsoptimering och analys**. En labbägare kan ställa in labbscheman för automatisk start och avstängning av virtuella datorer. Labb ägaren kan ange ett schema för att ange de tidpunkter då Labbets virtuella datorer är tillgängliga för användare och ange användnings principer per användare eller labb för att optimera kostnaderna. 

Om du bara vill ange precis vad du behöver i ett labb och låta tjänsten konfigurera och hantera den infrastruktur som krävs för labbet väljer du något av de **hanterade labbtyperna**. För närvarande är **klassrumslabb** den enda hanterade labbtypen som du kan skapa med Azure Lab Services.

Följande avsnitt innehåller mer information om dessa labb. 

## <a name="managed-lab-types"></a>Hanterade labbtyper
Med Azure Lab Services kan du skapa laboratorier vars infrastruktur hanteras av Azure. I den här artikeln kallas de för hanterade labbtyper. Hanterade labbtyper erbjuder olika typer av labb som passar dina specifika behov. För närvarande är den enda hanterade labb typen som stöds **klass rum Lab**. 

Med hanterade labbtyper kommer du igång direkt med minimal konfigurering. Tjänsten hanterar all hantering av infrastrukturen för labbet, från att snurra de virtuella datorerna för att hantera fel och skala infrastrukturen.För att kunna skapa en hanterad labbtyp, till exempel ett klassrumslabb, måste du först skapa ett labbkonto för din organisation. Labbkontot fungerar som det centrala kontot där alla labb i organisationen hanteras. 

När du skapar och använder Azure-resurser i dessa hanterade labbtyper skapar och hanterar tjänsten resurser i interna Microsoft-prenumerationer. De skapas inte i din egen Azure-prenumeration. Tjänsten håller reda på användningen av dessa resurser i interna Microsoft-prenumerationer. Denna användning faktureras tillbaka till din Azure-prenumeration som innehåller labbkontot.   

Här följer några av **användningsfallen för hanterade labbtyper**: 

- Förse eleverna med ett labb med virtuella datorer som är konfigurerat med exakt det som behövs för en lektion. Ge varje elev ett begränsat antal timmar för användning av de virtuella datorerna för läxor eller personliga projekt.
- Konfigurera en pool med högpresterande virtuella datorer för att utföra beräkningsintensiv eller grafikintensiv research. Kör de virtuella datorerna efter behov och rensa datorerna när du är klar. 
- Flytta din skolas fysiska datorlabb till molnet. Skala automatiskt antalet virtuella datorer endast efter den maximala användning och de kostnadströsklar som du anger för labbet.  
- Etablera snabbt ett labb med virtuella datorer som värd för en hackathon. Ta bort labbet med en enda klickning när du är klar. 

## <a name="next-steps"></a>Nästa steg
I följande själv studie kurser finns stegvisa anvisningar för hur du skapar ett labb konto och skapar ett klass rums labb.

- [Självstudie: Konfigurera ett labb konto](tutorial-setup-lab-account.md)
- [Självstudie: skapa ett klass rums labb](tutorial-setup-classroom-lab.md)
