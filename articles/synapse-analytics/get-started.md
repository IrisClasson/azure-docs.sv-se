---
title: 'Självstudie: kom igång med Azure Synapse Analytics'
description: I den här självstudien får du lära dig de grundläggande stegen för att konfigurera och använda Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075863"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Kom igång med Azure Synapse Analytics

Den här självstudien är en steg-för-steg-guide genom de större funktions områdena i Azure Synapse Analytics. Självstudien är den perfekta start punkten för någon som vill ha en guidad rundtur genom de viktigaste scenarierna i Azure Synapse Analytics. När du har slutfört stegen i självstudien har du en helt funktionell Synapse-arbetsyta där du kan börja analysera data med SQL, SQL på begäran och Apache Spark.

Du får lära dig att:
* Etablera en Synapse-arbetsyta i en Azure-prenumeration
* Konfigurera åtkomst kontroll på ett ADLSGEN2-konto så att det sömlöst fungerar med arbets ytan Synapse
* Läs in NYCTaxi-exempel data i arbets ytan Synapse så att den kan användas av SQL, SQL på begäran och Spark
* Redigera och köra SQL-skript och Synapse-anteckningsböcker med Synapse Studio
* Fråga SQL-tabeller och Spark-tabeller
* Läsa in data från SQL-tabeller till Spark-dataframes
* Läs in data i SQL-tabeller från Spark dataframes
* Utforska innehållet i ett ADLSGEN2-konto
* Analysera Parquet-datafiles i ADLSGEN2-konton med Spark och SQL på begäran 
* Bygg en datapipeline som automatiskt kör en Synapse Notebook varje timme

Följ stegen *i den ordning* som visas nedan och ta en rundtur igenom många av funktionerna och lär dig hur du använder dess kärn funktioner.

* [STEG 1 – Skapa och konfigurera en Synapse-arbetsyta](get-started-create-workspace.md)
* [STEG 2 – analysera med en SQL-pool](get-started-analyze-sql-pool.md)
* [STEG 3 – analysera med Spark](get-started-analyze-spark.md)
* [STEG 4 – analysera med SQL på begäran](get-started-analyze-sql-on-demand.md)
* [STEG 5 – analysera data i ett lagrings konto](get-started-analyze-storage.md)
* [STEG 6 – dirigera med pipelines](get-started-pipelines.md)
* [STEG 7 – visualisera data med Power BI](get-started-visualize-power-bi.md)
