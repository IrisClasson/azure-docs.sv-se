---
title: Använda Livy Spark skicka jobb till Spark-kluster i Azure HDInsight
description: Lär dig använda Apache Spark REST API för att skicka Spark-jobb via en fjärranslutning till ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: f5b3500e1e700abf894fc4e21fb540eb258d5e35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066061"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Använda Apache Spark REST API för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster

Lär dig hur du använder [Apache Livy](https://livy.incubator.apache.org/), [Apache Spark](https://spark.apache.org/) REST API, som används för att skicka fjärrstyrda jobb till ett Azure HDInsight Spark-kluster. Detaljerad dokumentation finns i [ https://livy.incubator.apache.org/ ](https://livy.incubator.apache.org/).

Du kan använda Livy för att köra interaktiv Spark-gränssnitt eller skicka batch-jobb som ska köras på Spark. Den här artikeln berättar om hur du använder Livy för att skicka batchjobb. Kodfragmenten i den här artikeln använder cURL för att göra REST API-anrop till Livy Spark-slutpunkten.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett HDInsight Spark-kluster.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Skicka ett Apache Spark för Livy batch-jobb

Innan du skickar in ett batch-jobb, måste du överföra JAR-filen för programmet i klusterlagringen kopplat till klustret. Du kan använda [AzCopy](../../storage/common/storage-use-azcopy.md), ett kommandoradsverktyg för att göra detta. Det finns olika andra klienter som du kan använda för att överföra data. Det finns mer information om dem i [Ladda upp data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Exempel

* Om jar-filen i klusterlagringen (WASB)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Om du vill skicka jar-filnamnet och classname som en del av en indatafil (i det här exemplet input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Få information om Livy Spark-batchar som körs i klustret

Syntax:

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Exempel

* Om du vill hämta alla Livy Spark-batchar som körs i klustret:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* Om du vill hämta en specifik grupp med en viss batch-ID

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Ta bort ett batch-jobb med Livy Spark

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Exempel

Tar bort ett batch-jobb med batch-ID `5`.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark och hög tillgänglighet

Livy ger hög tillgänglighet för Spark jobb som körs i klustret. Här är några exempel.

* Om tjänsten Livy slutar att fungera när du har skickat ett jobb via en fjärranslutning till ett Spark-kluster fortsätter jobbet att köras i bakgrunden. När Livy är säkerhetskopiera, återställer status för jobbet och rapporter som det igen.
* Jupyter notebooks för HDInsight drivs av Livy i serverdelen. Om en bärbar dator kör ett Spark-jobb och tjänsten Livy hämtar startats om, fortsätter anteckningsboken att köra kod celler. 

## <a name="show-me-an-example"></a>Visa mig ett exempel

I det här avsnittet ska titta vi på exempel du använder Livy Spark för att skicka batch-jobb, övervaka förloppet för jobbet och tar sedan bort den. Programmet som vi använder i det här exemplet är det som har utvecklats i artikeln [skapa ett fristående program för Scala och för att köra på HDInsight Spark-kluster](apache-spark-create-standalone-application.md). De här stegen förutsätter som:

* Du har redan kopierat över programmet JAR-filen till lagringskontot som associerats med klustret.
* Du har CuRL installerad på den dator där du försöker de här stegen.

Utför följande steg:

1. Låt oss först kontrollera att Livy Spark körs i klustret. Vi kan göra det genom att hämta en lista över aktiva batchar. Om du kör ett jobb med Livy för första gången ska utdata returnera noll.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    Du bör få utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observera hur den sista raden i resultatet säger **totalt: 0**, vilket tyder på Ingen aktiv batchar.

2. Låt oss nu skicka ett batch-jobb. I följande kodfragment används en indatafil (input.txt) för att skicka jar-namn och namnet på klassen som parametrar. Om du använder de här stegen från en Windows-dator, är den rekommenderade metoden att använda en indatafil.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametrarna i filen **input.txt** definieras enligt följande:

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observera hur den sista raden i resultatet säger **tillstånd: startar**. Den också heter **-id: 0**. Här kan **0** är batch-ID.

3. Du kan nu hämta status för den här specifika batch med hjälp av batch-ID.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Nu visas i utdata **status: lyckades**, vilket tyder på att jobbet har slutförts.

4. Om du vill kan du nu ta bort gruppen.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Den sista raden i utdata visar att gruppen har tagits bort. Tar bort ett jobb, medan den körs också stoppar jobbet. Om du tar bort ett jobb som har slutförts utan problem eller i annat fall raderas Jobbinformationen helt.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Uppdateringar till Livy konfigurationen från och med HDInsight 3.5-version

HDInsight 3.5-kluster och inaktivera användning av lokala sökvägar till åtkomst exempeldatafiler eller JAR-filer ovan, som standard. Vi rekommenderar att du kan använda den `wasb://` sökvägen i stället att komma åt JAR-filer eller exempeldata filer från klustret.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Skicka Livy jobb för ett kluster i Azure-nätverk

Om du ansluter till ett HDInsight Spark-kluster i Azure Virtual Network kan kan du ansluta direkt till Livy i klustret. I detta fall är URL: en för Livy slutpunkten är `http://<IP address of the headnode>:8998/batches`. Här kan **8998** är den port som Livy körs på klustrets huvudnod. Mer information om att komma åt tjänster på icke-offentlig portar finns i [portar som används av Apache Hadoop-tjänster på HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Nästa steg

* [Apache Livy REST API-dokumentation](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)