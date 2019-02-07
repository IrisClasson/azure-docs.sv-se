---
title: Apache Storm skriva till lagring/Data Lake Storage - Azure HDInsight
description: Lär dig hur du använder Apache Storm för att skriva till det HDFS-kompatibla lagringsutrymmet för HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 53f81a06a0a10d4526816b5117eb12f01d75e25a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819169"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>Skriva till Apache Hadoop HDFS från Apache Storm på HDInsight

Lär dig hur du använder [Apache Storm](https://storm.apache.org/) att skriva data till det HDFS-kompatibla lagringsutrymmet som används av Apache Storm på HDInsight. HDInsight kan använda både Azure Storage och Azure Data Lake Storage som HDFS-kompatibla lagringsutrymmet. Storm innehåller en [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) komponent som skriver data till HDFS. Det här dokumentet innehåller information om skrivning till valfri typ av lagring från HdfsBolt. 

> [!IMPORTANT]  
> Exempel-topologin som används i det här dokumentet är beroende av komponenter som ingår i Storm på HDInsight. Det kan kräva ändring av att arbeta med Azure Data Lake Storage när det används med andra Apache Storm-kluster.

## <a name="get-the-code"></a>Hämta koden

Det projekt som innehåller den här topologin är tillgänglig för hämtning från [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

För att kompilera det här projektet, behöver du följande konfiguration för din utvecklingsmiljö:

* [Java JDK 1.8](https://aka.ms/azure-jdks) eller högre. HDInsight 3.5 eller högre kräver Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Följande miljövariabler kan konfigureras när du installerar Java och JDK på utvecklingsdatorn. Du bör dock kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME` – ska peka på den katalog där JDK har installerats.
* `PATH` – ska innehålla följande sökvägar:
  
    * `JAVA_HOME` (eller motsvarande sökväg).
    * `JAVA_HOME\bin` (eller motsvarande sökväg).
    * Den katalog där Maven är installerat.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Hur du använder HdfsBolt med HDInsight

> [!IMPORTANT]  
> Innan du använder HdfsBolt med Storm i HDInsight, måste du först använda en skriptåtgärd för att kopiera nödvändiga jar-filerna till den `extpath` för Storm. Mer information finns i Konfigurera kluster-avsnittet.

HdfsBolt använder schemat för filen som du anger för att lära dig att skriva till HDFS. Med HDInsight, kan du använda en av scheman som följande:

* `wasb://`: Används med ett Azure Storage-konto.
* `adl://`: Använda med Azure Data Lake Storage.

Följande tabell innehåller exempel på användning av fil-schema för olika scenarier:

| Schema | Anteckningar |
| ----- | ----- |
| `wasb:///` | Standardkontot för lagring är en blobbehållare i ett Azure Storage-konto |
| `adl:///` | Standardkontot för lagring är en katalog i Azure Data Lake Storage. När du skapar klustret anger du katalogen i Data Lake Storage som är roten av klustrets HDFS. Till exempel den `/clusters/myclustername/` directory. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | En icke-standard (ytterligare) Azure-lagringskonto som är associerade med klustret. |
| `adl://STORENAME/` | Roten för Data Lake-lagring som används av klustret. Det här schemat kan du komma åt data som ligger utanför den katalog som innehåller klustret-filsystemet. |

Mer information finns i den [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) referens på Apache.org.

### <a name="example-configuration"></a>Exempelkonfiguration

Följande YAML är hämtad från den `resources/writetohdfs.yaml` -filen som ingår i det här exemplet. Den här filen definierar Storm-topologi med den [som](https://storm.apache.org/releases/1.1.2/flux.html) ramverk för Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Den här YAML definierar följande:

* `syncPolicy`: Definierar när filer är synkroniserad/rensade till filsystemet. I det här exemplet var 1000 tupplar.
* `fileNameFormat`: Definierar namnmönstret sökvägen och filnamnet för att använda när du skriver filer. I det här exemplet tillhandahålls sökvägen vid körning med hjälp av ett filter och filnamnstillägget `.txt`.
* `recordFormat`: Definierar det interna formatet för filerna som skrivits. I det här exemplet fälten avgränsas med den `|` tecken.
* `rotationPolicy`: Definierar när du roterar filer. I det här exemplet utförs ingen rotation.
* `hdfs-bolt`: Använder de tidigare komponenterna som konfigurationsparametrar för den `HdfsBolt` klass.

Mer information om ramverket som finns i [ https://storm.apache.org/releases/current/flux.html ](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Konfigurera klustret

Storm på HDInsight innehåller inte de komponenter som HdfsBolt använder för att kommunicera med Azure Storage eller Data Lake Storage i Storms klassökvägen som standard. Använd följande skriptåtgärd för att lägga till dessa komponenter i `extlib` katalogen för Storm i klustret:

* Skript-URI: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Noder som gäller för: Nimbus, Supervisor
* Parametrar: Ingen

Information om hur du använder det här skriptet med ditt kluster finns i den [anpassa HDInsight-kluster med skriptåtgärder](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentet.

## <a name="build-and-package-the-topology"></a>Bygga och paketera topologin

1. Ladda ned exempelprojektet från [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) till utvecklingsmiljön.

2. Från en kommandotolk och terminal eller skal-session, ändra kataloger till roten på det hämta projektet. Om du vill bygga och paketera topologin, använder du följande kommando:
   
        mvn compile package
   
    När det är klart att skapa och paketering, det finns en ny katalog med namnet `target`, som innehåller en fil med namnet `StormToHdfs-1.0-SNAPSHOT.jar`. Den här filen innehåller den kompilerade topologin.

## <a name="deploy-and-run-the-topology"></a>Distribuera och köra topologin

1. Använd följande kommando för att kopiera topologi till HDInsight-klustret. Ersätt **användaren** med SSH-användarnamn som du använde när klustret skapas. Ersätt **CLUSTERNAME** med namnet på klustret.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    När du uppmanas, anger du det lösenord som används när du skapar SSH-användare för klustret. Om du använde en offentlig nyckel i stället för ett lösenord, kan du behöva använda den `-i` parametern för att ange sökvägen till motsvarande privata nyckel.
   
   > [!NOTE]  
   > Mer information om hur du använder `scp` med HDInsight finns i [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. När överföringen är klar kan du använda följande för att ansluta till HDInsight-klustret med SSH. Ersätt **användaren** med SSH-användarnamn som du använde när klustret skapas. Ersätt **CLUSTERNAME** med namnet på klustret.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    När du uppmanas, anger du det lösenord som används när du skapar SSH-användare för klustret. Om du använde en offentlig nyckel i stället för ett lösenord, kan du behöva använda den `-i` parametern för att ange sökvägen till motsvarande privata nyckel.
   
   Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. När du är ansluten, använder du följande kommando för att skapa en fil med namnet `dev.properties`:

        nano dev.properties

4. Använd följande text som innehållet i den `dev.properties` fil:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > Det här exemplet förutsätts att klustret använder ett Azure Storage-konto som standardlagring. Om klustret använder Azure Data Lake Storage, använda `hdfs.url: adl:///` i stället.
    
    Om du vill spara filen, Använd __Ctrl + X__, sedan __Y__, och slutligen __RETUR__. Värdet i den här filen in Webbadressen för Data Lake-lagring och katalognamnet som data ska skrivas till.

3. Använd följande kommando för att starta topologin:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Detta kommando startar topologin med hjälp av ramverket som genom att skicka den till Nimbus-noden i klustret. Topologin definieras av den `writetohdfs.yaml` filen som ingår i den JAR-filen. Den `dev.properties` fil skickas som ett filter och de värden som finns i filen läses av topologin.

## <a name="view-output-data"></a>Visa utdata

Om du vill visa data, använder du följande kommando:

    hdfs dfs -ls /stormdata/

En lista över filer som skapas av den här topologin visas.

I följande lista är ett exempel på data som returneras av de tidigare kommandona:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Stoppa topologin

Storm-topologier köra tills de stoppas eller klustret tas bort. Om du vill stoppa topologin, använder du följande kommando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Ta bort ditt kluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Apache Storm för att skriva till Azure Storage och Azure Data Lake Storage kan du identifiera andra [Apache Storm-exempel för HDInsight](apache-storm-example-topology.md).

## <a name="see-also"></a>Se också
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
