---
title: Lär dig med hjälp av en Apache Hadoop-sandbox - emulatorn – Azure HDInsight
description: 'Om du vill börja lära dig om hur du använder Apache Hadoop-ekosystemet, kan du ställa in en Hadoop-sandbox från Hortonworks på virtuella Azure-datorer. '
keywords: hadoop-emulatorn, begränsat hadoop-läge
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393137"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Kom igång med en Apache Hadoop-sandbox, ett emuleringsprogram på en virtuell dator

Lär dig hur du installerar Apache Hadoop-sandbox från Hortonworks på en virtuell dator för att lära dig om Hadoop-ekosystemet. Sandbox-miljön tillhandahåller en lokal utvecklingsmiljö för att lära dig om Hadoop, Hadoop Distributed File System (HDFS) och jobböverföring. När du är bekant med Hadoop kan börja du använda Hadoop på Azure genom att skapa ett HDInsight-kluster. Mer information om hur du kommer igång finns i [Kom igång med Hadoop i HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* [Oracle VirtualBox](https://www.virtualbox.org/). Hämta och installera det från [här](https://www.virtualbox.org/wiki/Downloads).


## <a name="download-and-install-the-virtual-machine"></a>Ladda ned och installera den virtuella datorn
1. Bläddra till den [Cloudera hämtar](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

2. Klicka på **VIRTUALBOX** under **Välj installationstyp** att ladda ned Hortonworks sandbox-miljön på en virtuell dator. Logga in eller Slutför produktformuläret.

1. Klicka på knappen **HDP SANDBOX (senaste)** att påbörja hämtningen.

Anvisningar om hur du konfigurerar sandbox-miljön finns i [Sandbox-distribution och installerar guiden](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Om du vill hämta en äldre version sandbox-miljön för HDP finns i länkarna under **äldre versioner**.

## <a name="start-the-virtual-machine"></a>Starta den virtuella datorn

1. Öppna Oracle VM VirtualBox.
2. Från den **filen** -menyn klickar du på **Import installation**, och sedan ange avbildningen som begränsat Hortonworks-läge.
1. Välj Hortonworks sandbox-miljön, klicka på **starta**, och sedan **Normal Start**. När den virtuella datorn har slutförts startprocessen visar instruktioner för inloggning.

    ![Normal start](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Öppna en webbläsare och gå till den URL som visas (vanligtvis `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Ange Sandbox-lösenord

1. Från den **börjar** steg i Hortonworks Sandbox-sida väljer **visa avancerade alternativ**. Använd informationen på den här sidan för att logga in på sandbox-miljön med hjälp av SSH. Använd namnet och lösenordet som angavs.

   > [!NOTE]
   > Om du inte har en SSH-klient installerad kan du kan använda den webbaserade SSH som anges i av den virtuella datorn på **http://localhost:4200/** .

    Första gången du ansluter med hjälp av SSH uppmanas du att ändra lösenordet för rotkontot. Ange ett nytt lösenord som du använder när du loggar in med SSH.

2. När du loggat in kan du ange följande kommando:

        ambari-admin-password-reset

    När du uppmanas ange ett lösenord för administratörskonto Ambari. Det här används när du har åtkomst till Ambari-Webbgränssnittet.

## <a name="use-hive-commands"></a>Använda Hive-kommandon

1. Använd följande kommando för att starta Hive-gränssnittet från en SSH-anslutning till sandbox:

        hive
2. När gränssnittet har startats kan du använda följande för att visa vilka tabeller som tillhandahålls med sandbox-miljön:

        show tables;
3. Använd följande för att hämta 10 raderna från den `sample_07` tabell:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du använder Visual Studio med begränsat Hortonworks-läge](../hdinsight-hadoop-emulator-visual-studio.md)
* [Lära dig grunderna för Hortonworks sandbox-miljön](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop-självstudiekurs – komma igång med HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

