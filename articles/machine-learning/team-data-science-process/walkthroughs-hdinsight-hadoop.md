---
title: Analys i Azure HDInsight Hadoop med Hive - Team Data Science Process
description: Exempel på Team Data Science Process som går genom att använda Hive på Azure HDInsight Hadoop för att göra förutsägelseanalyser.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d3c3278a058162632a6ba7ea9731e5f233190700
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60804665"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Använda Hive på Azure HDInsight Hadoop data science genomgångar 

Dessa genomgångar använda Hive med HDInsight Hadoop-kluster för att göra förutsägelseanalyser. De följer du anvisningarna i Team Data Science Process. En översikt över Team Data Science Process, se [Data Science Process](overview.md). En introduktion till Azure HDInsight finns i [introduktion till Azure HDInsight, Hadoop-teknikstacken och Hadoop-kluster](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Ytterligare data science genomgångar som kör Team Data Science Process är grupperade efter den **plattform** som de använder. Se [genomgångar kör Team Data Science Process](walkthroughs.md) för en specifikation av de här exemplen.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Förutsäg taxitips med Hive med HDInsight Hadoop

Den [Använd HDInsight Hadoop-kluster](hive-walkthrough.md) genomgången använder data från New York-taxibilar för att förutsäga: 

- Om ett tips är betald 
- Distributionen av tips belopp

Scenariot implementeras med hjälp av Hive med en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/). Du lär dig att lagra, utforska och funktionen engineer data från en offentligt tillgänglig NYC Taxitransport resa och avgiften datauppsättning. Du kan också använda Azure Machine Learning för att skapa och distribuera modeller.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Förutsäga annons klick med Hive med HDInsight Hadoop

Den [Använd Azure HDInsight Hadoop-kluster i en datauppsättning som 1 TB](hive-criteo-walkthrough.md) genomgången använder en offentligt tillgänglig [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) klickar du på datauppsättningen för att förutsäga om ett tips är betalas ut och intervallet för belopp som förväntat. Scenariot implementeras med hjälp av Hive med en [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, funktionen tekniker och ned exempeldata. Azure Machine Learning används för att skapa, träna och betygsätta en binär klassificeringsmodell att förutsäga om en användare klickar på en annons. Avslutar den här genomgången visar hur du publicerar en av dessa modeller som en webbtjänst.


## <a name="next-steps"></a>Nästa steg

En beskrivning av de viktigaste komponenterna som utgör Team Data Science Process finns i [Team Data Science Process översikt](overview.md).

En beskrivning av livscykeln för Team Data Science Process som du kan använda för att strukturera dina dataforskningsprojekt finns [Team Data Science Process-livscykeln](lifecycle.md). Livscykeln beskrivs stegen, från början till slut att projekt vanligtvis följer när de utförs. 

