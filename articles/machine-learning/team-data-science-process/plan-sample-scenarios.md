---
title: Identifiera scenarier för Azure Machine Learning - Team Data Science Process
description: Välj de lämpliga scenarierna för att göra avancerade förutsägande analyser med Team Data Science Process.
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
ms.openlocfilehash: 3e7d747901fb73afa78b6162316709d7d2e78927
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981122"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenarier för avancerade analyser i Azure Machine Learning
Den här artikeln beskrivs olika exempel datakällor och målscenarier som kan hanteras av den [Team Data Science Process (TDSP)](overview.md). TDSP erbjuder en systematisk metod för team att samarbeta med att skapa intelligenta program. De scenarier som presenteras här visar alternativ som är tillgängliga i bearbetning av arbetsflödet som beror på den dataegenskaper, datakällor och mål-databaser i Azure.

Den **beslutsträd** för att välja exempelscenarier som passar för dina data och målet visas i det sista avsnittet.

Var och en av de följande avsnitten presenterar ett exempelscenario. För varje scenario, en möjlig datavetenskap eller avancerad analys flödet och stöd för Azure-resurser visas.

> [!NOTE]
> **För alla av följande scenarier måste du:**
> <br/>
> 
> * [skapar ett lagringskonto](../../storage/common/storage-account-create.md)
>   <br/>
> * [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scenariot \#1: små till medelstora datauppsättning i tabellformat i lokala filer
![Små till medelstora lokala filer][1]

#### <a name="additional-azure-resources-none"></a>Ytterligare Azure-resurser: ingen
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Ladda upp en datauppsättning.
1. Skapa ett flöde för Azure Machine Learning-experiment som från och med överförda datauppsättningar.

## <a name="smalllocalprocess"></a>Scenariot \#2: små till medelstora datauppsättningen för lokala filer som kräver bearbetning
![Små till medelstora lokala filer med bearbetning][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (IPython Notebook-server)
1. Skapa en Azure virtuell dator som kör IPython Notebook.
1. Ladda upp data till en Azure storage-behållare.
1. Förbearbeta och rensa data i IPython Notebook, komma åt data från Azure storage-behållare.
1. Omvandla data till rensad, tabellformat.
1. Spara transformerade data i Azure-blobar.
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data från Azure-blobbar med modulen [Importera data][import-data] .
1. Skapa ett flöde för Azure Machine Learning-experiment som från och med infogade datauppsättningar.

## <a name="largelocal"></a>Scenariot \#3: stor datauppsättning av lokala filer som riktar in sig på Azure Blobs
![Stora lokala filer][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (IPython Notebook-server)
1. Skapa en Azure virtuell dator som kör IPython Notebook.
1. Ladda upp data till en Azure storage-behållare.
1. Förbearbeta och rensa data i IPython Notebook, komma åt data från Azure-blobar.
1. Omvandla data till rensad, tabellform, om det behövs.
1. Utforska data och skapa funktioner efter behov.
1. Extrahera ett datasampel för små till medelstora.
1. Spara exempeldata i Azure-blobar.
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data från Azure-blobbar med modulen [Importera data][import-data] .
1. Skapa Azure Machine Learning experiment flöde från och med infogade datauppsättningar.

## <a name="smalllocaltodb"></a>Scenariot \#4: små till medelstora datauppsättningen för lokala filer som riktar in sig på SQL Server i en Azure virtuell dator
![Små till medelstora lokala filer till SQL DB i Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython Notebook-server)
1. Skapa en Azure virtuell dator som kör SQL Server + IPython Notebook.
1. Ladda upp data till en Azure storage-behållare.
1. Förbearbeta och rensa data i Azure storage-behållare med hjälp av IPython Notebook.
1. Omvandla data till rensad, tabellform, om det behövs.
1. Sparar data till VM-lokala filer (IPython Notebook körs på den virtuella datorn finns i lokala enheter för VM-enheter).
1. Läsa in data till SQL Server-databas som körs på en virtuell Azure-dator.
   
   Alternativet \#1: med SQL Server Management Studio.
   
   * Logga in på SQLServer-dator
   * Kör SQL Server Management Studio.
   * Skapa tabeller i databasen och mål.
   * Använd en av stora importera metoder för att läsa in data från VM-lokala filer.
   
   Alternativet \#2: använda IPython Notebook – inte lämpligt för medelstora och större datauppsättningar
   
   <!-- -->    
   * Använd ODBC-anslutningssträng för att ansluta till SQL Server på den virtuella datorn.
   * Skapa tabeller i databasen och mål.
   * Använd en av stora importera metoder för att läsa in data från VM-lokala filer.
1. Utforska data, skapa funktioner som behövs. Observera att funktionerna inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
1. Besluta om en exempelstorlek för data om det behövs och/eller önskas.
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Skapa Azure Machine Learning experiment flöde från och med infogade datauppsättningar.

## <a name="largelocaltodb"></a>Scenariot \#5: stor datauppsättning i lokala filer, rikta SQL Server i Azure VM
![Stora lokala filer till SQL DB i Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython Notebook-server)
1. Skapa en Azure virtuell dator som kör SQL Server och IPython Notebook-server.
1. Ladda upp data till en Azure storage-behållare.
1. (Valfritt) Förbearbeta och rensa data.
   
   a.  Förbearbeta och rensa data i IPython Notebook, komma åt data från Azure
   
       blobs.
   
   b.  Omvandla data till rensad, tabellform, om det behövs.
   
   c.  Sparar data till VM-lokala filer (IPython Notebook körs på den virtuella datorn finns i lokala enheter för VM-enheter).
1. Läsa in data till SQL Server-databas som körs på en virtuell Azure-dator.
   
   a.  Logga in på SQLServer-dator.
   
   b.  Om data inte sparats redan hämta filer från Azure
   
       storage container to local-VM folder.
   
   c.  Kör SQL Server Management Studio.
   
   d.  Skapa tabeller i databasen och mål.
   
   e.  Använd en av stora importera metoder för att läsa in data.
   
   f.  Om tabellkopplingar måste, skapa index för att bearbeta kopplingar.
   
   > [!NOTE]
   > Det rekommenderas för snabbare inläsning av stora datamängder som du skapar partitionerade tabeller och bulk dataimport parallellt. Mer information finns i [parallella importera Data till SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Utforska data, skapa funktioner som behövs. Observera att funktionerna inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
1. Besluta om en exempelstorlek för data om det behövs och/eller önskas.
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Enkelt flöde för Azure Machine Learning experiment från och med överförd datamängd

## <a name="largedbtodb"></a>Scenario \#6: stor data uppsättning i en SQL Server databas lokalt, mål SQL Server på en virtuell Azure-dator
![Stora SQL DB lokal till SQL-databas i Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython Notebook-server)
1. Skapa en Azure virtuell dator som kör SQL Server och IPython Notebook-server.
1. Använd en av data exportera metoder för att exportera data från SQL Server till dumpfiler.
   
   > [!NOTE]
   > Om du vill flytta alla data från den lokala databasen kan du flytta den fullständiga databasen till SQL Server-instansen i Azure med en alternativ metod (snabbare). Hoppa över stegen för att exportera data, skapa databas, och Läs in/importera data till måldatabasen och följa den alternativa metoden.
   > 
   > 
1. Ladda upp filer med felsökningsdumpar till Azure storage-behållare.
1. Läsa in data till en SQL Server-databas som körs på en Azure-dator.
   
   a.  Logga in på SQLServer-dator.
   
   b.  Hämta filer från en Azure storage-behållare till den lokala VM-mappen.
   
   c.  Kör SQL Server Management Studio.
   
   d.  Skapa tabeller i databasen och mål.
   
   e.  Använd en av stora importera metoder för att läsa in data.
   
   f.  Om tabellkopplingar måste, skapa index för att bearbeta kopplingar.
   
   > [!NOTE]
   > För snabbare inläsning av stora datamängder, skapa partitionerade tabeller och vill importera data parallellt. Mer information finns i [parallella importera Data till SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Utforska data, skapa funktioner som behövs. Observera att funktionerna inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
1. Besluta om en exempelstorlek för data om det behövs och/eller önskas.
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Enkelt Azure Machine Learning experiment flöde från och med överförda datamängden.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativ metod för att kopiera en fullständig databas från en lokal SQL Server till Azure SQL Database
![Koppla från lokala DB och Anslut till SQL DB i Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure-dator (SQL Server / IPython Notebook-server)
Om du vill replikera hela SQL Server-databasen i SQL Server-dator, bör du kopiera en databas från en plats/server till en annan, förutsatt att databasen kan tas offline. Du kan göra detta i SQL Server Management Studio Object Explorer eller med hjälp av motsvarande Transact-SQL-kommandon.

1. Koppla från databasen på källplatsen. Mer information finns i [koppla från en databas](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. Kopiera frånkopplad databasfilen eller filerna och loggfilen eller filer till målplatsen på SQL Server-dator i Azure i Windows Explorer eller Windows kommandotolk-fönster.
1. Bifoga filerna på SQL Server-målinstansen. Mer information finns i [ansluta en databas](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Flytta en databas med frånkoppling och bifoga (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scenariot \#7: Big data i lokala filer, rikta Hive-databas i Azure HDInsight Hadoop-kluster
![Big data i lokala mål Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure HDInsight Hadoop-kluster och Azure-dator (IPython Notebook-server)
1. Skapa en Azure virtuell dator som kör IPython Notebook-server.
1. Skapa ett Azure HDInsight Hadoop-kluster.
1. (Valfritt) Förbearbeta och rensa data.
   
   a.  Förbearbeta och rensa data i IPython Notebook, komma åt data från Azure
   
       blobs.
   
   b.  Omvandla data till rensad, tabellform, om det behövs.
   
   c.  Sparar data till VM-lokala filer (IPython Notebook körs på den virtuella datorn finns i lokala enheter för VM-enheter).
1. Ladda upp data till standardbehållaren för Hadoop-kluster som valts i steg 2.
1. Läsa in data till Hive-databas i Azure HDInsight Hadoop-kluster.
   
   a.  Logga in på huvudnoden för Hadoop-kluster
   
   b.  Öppna Hadoop-kommandoraden.
   
   c.  Ange rotkatalogen Hive med kommandot `cd %hive_home%\bin` i Hadoop-kommandoraden.
   
   d.  Köra Hive-frågor för att skapa databasen och tabeller och läsa in data från blob storage till Hive-tabeller.
   
   > [!NOTE]
   > Om data är stor, kan användare skapa Hive-tabell med partitioner. Användare kan sedan använda en `for` loop i Hadoop kommandoraden på huvudnoden att läsa in data till Hive-tabell som har partitionerats med partitionen.
   > 
   > 
1. Utforska data och skapa funktioner som behövs i Hadoop-kommandoraden. Observera att funktionerna inte behöver materialiseras i databastabeller. Observera endast nödvändiga frågan för att skapa dem.
   
   a.  Logga in på huvudnoden för Hadoop-kluster
   
   b.  Öppna Hadoop-kommandoraden.
   
   c.  Ange rotkatalogen Hive med kommandot `cd %hive_home%\bin` i Hadoop-kommandoraden.
   
   d.  Köra Hive-frågor i Hadoop-kommandoraden på huvudnoden i Hadoop-kluster och utforska data och skapa funktioner efter behov.
1. Om behövs, eller önskade, exempeldata att få plats i Azure Machine Learning Studio.
1. Logga in på den [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från `Hive Queries` med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Enkelt Azure Machine Learning experiment flöde från och med överförda datamängden.

## <a name="decisiontree"></a>Beslutsträd för val av scenario
---
Följande diagram sammanfattar de scenarier som beskrivs ovan och Advanced Analytics Process and Teknikval görs som tar dig till var och en av de specificerade scenarierna. Observera att databearbetning, utforskning, funktionsframställning och sampling kan ta i en eller flera metoden/miljö – vid källan, mellanliggande, och/eller målmiljöer – och kan fortsätta upprepade gånger efter behov. Diagrammet endast fungerar som en illustration av några av möjliga flöden och ger inte en uttömmande förteckning.

![Genomgång exempelscenarier DS process][8]

### <a name="advanced-analytics-in-action-examples"></a>Avancerad analys i praktiken exempel
Slutpunkt till slutpunkt Azure Machine Learning genomgångar som använder Advanced Analytics Process and Technology med offentliga datauppsättningar, finns här:

* [Team Data Science Process i praktiken: med SQL Server](sql-walkthrough.md).
* [Team Data Science Process i praktiken: med hjälp av HDInsight Hadoop-kluster](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
