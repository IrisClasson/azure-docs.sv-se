---
title: Använda .NET med Hadoop MapReduce på Linux-baserat HDInsight – Azure
description: Lär dig hur du använder .NET-program för strömning av MapReduce på Linux-baserade HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a6e9bea5d600771a7754142a4df4c0af503ccf5d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075492"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migrera .NET-lösningar för Windows-baserade HDInsight till Linux-baserade HDInsight

Linux-baserade HDInsight-kluster använder [mono ( https://mono-project.com) ](https://mono-project.com) för att köra .NET-program. Med mono kan du använda .NET-komponenter som MapReduce-program med Linux-baserade HDInsight. I det här dokumentet lär du dig hur du migrerar .NET-lösningar som skapats för Windows-baserade HDInsight-kluster och arbetar med mono på Linux-baserade HDInsight.

## <a name="mono-compatibility-with-net"></a>Mono-kompatibilitet med .NET

Mono version 4.2.1 ingår i HDInsight version 3,6. Mer information om den version av mono som ingår i HDInsight finns i [versioner av HDInsight-komponenter](hdinsight-component-versioning.md).

Mer information om kompatibilitet mellan mono och .NET finns i [mono-kompatibilitet ( https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokument.

> [!IMPORTANT]  
> SCP.NET-ramverket är kompatibelt med mono. Mer information om hur du använder SCP.NET med mono finns i [använda Visual Studio för att utveckla C#-topologier för Apache storm i HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatisk analys av portabilitet

[.Net-portabiliteten Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) kan användas för att generera en rapport över inkompatibiliteter mellan ditt program och mono. Använd följande steg för att konfigurera analys programmet för att kontrol lera om det finns en mono-portabilitet:

1. Installera [.net-portbaserad analys](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Under installationen väljer du den version av Visual Studio som ska användas.

2. Från Visual Studio 2015 väljer du __analysera__  >  __Inställningar för portabilitet__och kontrollerar att __4,5__ är markerat i __mono__ -avsnittet.

    ![4,5 kontrollerade i mono-sektionen för analys inställningarna](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Välj __OK__ för att spara konfigurationen.

3. Välj __analysera__  >  __sammansättnings portabilitet__. Välj den sammansättning som innehåller din lösning och välj sedan __Öppna__ för att börja analysera.

4. När analysen är klar väljer du __analysera__  >  __Visa analys rapporter__. I __analys resultat för portabilitet__väljer du __Öppna rapport__ för att öppna en rapport.

    ![Dialog rutan resultat för portabilitet-analys](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Analys verktyget kan inte fånga varje problem med lösningen. Till exempel anses en fil Sök väg som `c:\temp\file.txt` är OK om mono körs i Windows. Samma sökväg är inte giltig på en Linux-plattform.

## <a name="manual-portability-analysis"></a>Manuell analys av portabilitet

Utföra en manuell granskning av koden med hjälp av informationen i [Application portabilitet ( https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) Document.

## <a name="modify-and-build"></a>Ändra och bygg

Du kan fortsätta att använda Visual Studio för att bygga dina .NET-lösningar för HDInsight. Du måste dock se till att projektet är konfigurerat att använda .NET Framework 4,5.

## <a name="deploy-and-test"></a>Distribuera och testa

När du har ändrat din lösning med hjälp av rekommendationerna från .NET Solution Analyzer eller från en manuell analys måste du testa den med HDInsight. Testning av lösningen i ett Linux-baserat HDInsight-kluster kan avslöja diskreta problem som behöver åtgärdas. Vi rekommenderar att du aktiverar ytterligare loggning i programmet när du testar det.

Mer information om hur du kommer åt loggar finns i följande dokument:

* [Åtkomst Apache Hadoop garn program loggar på Linux-baserade HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Nästa steg

* [Använda C# med MapReduce på HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Använd C#-användardefinierade funktioner med Apache Hive och Apache gris](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Utveckla C#-topologier för Apache Storm i HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
