---
title: Exempeldata i olika Azure lagringsplatser - Team Data Science Process
description: Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller för att minska det till en mindre men representativa och mer hanterbara storlek.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253103"
---
# <a name="heading"></a>Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller

I följande artiklar beskrivs hur du sampla data som lagras i något av tre olika Azure-platser:

* [**Azure container-blobbdata** ](sample-data-blob.md) samplas genom att hämta den via programmering och samlar det med Python-exempelkod.
* [**SQL Server-data** ](sample-data-sql-server.md) samplas med både SQL och Python Programming språk. 
* [**Hive-tabelldata** ](sample-data-hive.md) samplas med hjälp av Hive-frågor.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Varför sampla data?**

Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Detta underlättar förståelse av data, utforskning och funktioner. Dess roll i Cortana Analytics-processen är att snabbt skapa prototyper för bearbetning av funktions- och machine learning-modeller.

