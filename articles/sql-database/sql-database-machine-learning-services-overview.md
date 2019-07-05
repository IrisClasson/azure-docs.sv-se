---
title: Azure SQL Database Machine Learning Services med översikt över R (förhandsversion)
description: Den här artikeln beskrivs Azure SQL Database Machine Learning Services (med R) och hur det fungerar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427948"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services med R (förhandsversion)

Machine Learning Services är en funktion i Azure SQL-databas som används för att köra R-skript i databasen. Funktionen innehåller Microsoft R-paket för högpresterande förutsägande analys och maskininlärning. Relationella data kan användas i R-skript via lagrade procedurer, T-SQL-skript som innehåller R-uttryck eller R-kod som innehåller T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (med R) är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Den offentliga förhandsversionen är tillgängligt för enskilda databaser och elastiska pooler med den vCore-baserade inköpsmodellen i den **generella** och **affärskritisk** tjänstnivåer. I den här första offentliga förhandsversionen kan den **hyperskala** tjänstnivå och **hanterad instans** distributionsalternativet stöds inte. R är för närvarande det enda språk som stöds. Det finns inget stöd för Python just nu.
>
> Förhandsversionen är nu tillgänglig i följande regioner: Västeuropa, Nordeuropa, USA, västra 2, östra USA, södra centrala USA, norra centrala USA, Kanada, centrala, Sydostasien, södra och Australien, sydöstra Australien.
>
> [Registrera dig för förhandsversionen av](#signup) nedan.

## <a name="what-you-can-do-with-r"></a>Vad du kan göra med R

Använd kraften hos R-språket för att leverera avancerad analys och maskininlärning i databasen. Den här funktionen ger beräkning och bearbetning där data finns, vilket eliminerar behovet av att hämta data över nätverket. Du kan också utnyttja kraften i R-paket för företaget att tillhandahålla avancerade analyser i stor skala.

Machine Learning Services innehåller en grundläggande distribution av R med överlägg med R-företagspaket från Microsoft. Microsofts R-funktioner och algoritmer är utformade för både stor skala och nytta. De levererar förutsägelseanalys, statistisk modellering, datavisualiseringar och ledande algoritmer för maskininlärning.

### <a name="r-packages"></a>R-paket

De vanligaste open source-R-paket är förinstallerade i Machine Learning Services. Följande R-paket från Microsoft ingår också:

| R-paket | Beskrivning|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open är de förbättrade R från Microsoft-distributionen. Det är en fullständig plattform för öppen källkod för statistiska analyser och datavetenskap. Den är baserad på och 100% kompatibel med R och omfattar ytterligare funktioner för bättre prestanda och reproducerbarhet. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR är primärt bibliotek för skalbar R. funktioner i det här biblioteket är bland de mest använda. Dataomvandlingar och manipulering, Statistisk sammanfattning, visualisering och modellering och analyser på många sätt finns i dessa bibliotek. Dessutom distribuera funktioner i dessa bibliotek automatiskt arbetsbelastningar över tillgängliga kärnor för parallell bearbetning, med möjlighet att arbeta med datasegment som samordnas och hanteras av beräkningsmotorn för. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML lägger till machine learning-algoritmer för att skapa anpassade modeller för textanalys, bildanalys och attitydanalys. |

Förutom de förinstallerade paket kan du [installera ytterligare paket](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrera dig för förhandsversionen

Om du vill registrera dig för den offentliga förhandsversionen kan du följa dessa steg:

1. Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/) innan du börjar.

2. Skicka ett e-postmeddelande till Microsoft på [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) om du vill registrera dig för den offentliga förhandsversionen. Den offentliga förhandsversionen av Machine Learning Services (med R) i SQL Database är inte aktiverad som standard.

När du är anmäld i programmet Microsoft publicerar du så att den allmänna förhandsgranskningen och aktivera R din befintliga eller nya databas.

Machine Learning-tjänster med R rekommenderas inte för produktionsarbetsbelastning den offentliga förhandsversionen.

## <a name="next-steps"></a>Nästa steg

- Se den [viktiga skillnader från SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhandsversion), finns det [Snabbstartsguide](sql-database-connect-query-r.md).
- Kom igång med några enkla R-skript, se [skapa och kör enkelt R-skript i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-quickstart-r-create-script.md).
