---
title: 'Självstudie: Utföra åtgärder för att extrahera, transformera, läsa in (ETL) med Hive i HDInsight – Azure'
description: Lär dig att extrahera data från en rå CSV-datamängd, transformera dem med Hive på HDInsight och därefter läsa in de transformerade data i Azure SQL Database med Sqoop.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 58773cfd8db5e065752e5cf89a7e6d6f172391b8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974372"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Självstudie: Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight

I den här självstudiekursen tar du en oformaterad CSV-datafil, importerar den till en HDInsight-kluster och transformerar sedan data med Apache Hive på Azure HDInsight. När dessa data har transformerats läser du in dem till en Azure SQL-databas med Apache Sqoop. Den här artikeln använder offentligt tillgängliga flygdata.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Ladda ned exempelflygdata
> * Ladda upp data till ett HDInsight-kluster
> * Transformera data med Hive
> * Skapa en tabell i Azure SQL Database
> * Använd Sqoop för att exportera data till Azure SQL Database

Följande bild visar ett typiskt ETL-programflöde.

![ETL-åtgärd med Apache Hive på Azure HDInsight](./media/data-lake-storage-tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "ETL-åtgärd med Apache Hive på Azure HDInsight")

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

* **Ett Linux-baserat Hadoop-kluster i HDInsight**. Läs informationen om att [konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera](./data-lake-storage-quickstart-create-connect-hdi-cluster.md) om du vill få anvisningar om hur du skapar ett nytt Linux-baserat HDInsight-kluster.

* **Azure SQL Database**. Du använder en Azure SQL-databas som måldatalager. Om du inte har någon SQL-databas kan du läsa [Skapa en Azure SQL-databas i Azure-portalen](../../sql-database/sql-database-get-started.md).

* **Azure CLI**. Om du inte har installerat Azure CLI kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att få mer anvisningar.

* **En SSH-klient**. Mer information finns i [Ansluta till HDInsight (Hadoop) med hjälp av SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med Azure HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Ladda ned flygdata

1. Gå till [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Välj följande värden på sidan:

   | Namn | Värde |
   | --- | --- |
   | Filtrera år |2013 |
   | Filtrera period |Januari |
   | Fält |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Rensa alla andra fält

3. Välj **Ladda ned**. Du får en .zip-fil med de datafält du valde.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Ladda upp data till ett HDInsight-kluster

Det finns många sätt att överföra data till lagring som är associerade med ett HDInsight-kluster. I det här avsnittet använder du `scp` för att ladda upp data. Läs avsnittet om att [använda Distcp för att kopiera data mellan ett befintligt lagringskonto och ett nytt lagringskonto med Data Lake Storage Gen2 aktiverat](data-lake-storage-use-distcp.md).

1. Öppna en kommandotolk och använd följande kommando för att ladda upp .zip-filen till HDInsight-klustrets huvudnod:

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    Ersätt *FILE_NAME* med namnet på .zip-filen. Ersätt *SSH_USER_NAME* med SSH-inloggningen för HDInsight-klustret. Ersätt *CLUSTER_NAME* med namnet på HDInsight-klustret.

   > [!NOTE]
   > Om du använder ett lösenord för att autentisera din SSH-inloggning uppmanas du att ange lösenordet. Om du använder en offentlig nyckel kan du behöva använda `-i`-parametern och ange sökvägen till motsvarande privata nyckel. Till exempel `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

2. När uppladdningen är klar kan du ansluta till klustret med hjälp av SSH. Öppna kommandotolken och ange följande kommando:

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. Använd följande kommando för att packa upp .zip-filen:

    ```bash
    unzip <FILE_NAME>.zip
    ```

    Det här kommandot extraherar en *.csv-fil* som är cirka 60 MB.

4. Använd följande kommandon för att skapa en katalog och kopiera sedan *.csv*-filen till katalogen:

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Skapa filsystemet för Azure Data Lake Storage Gen2.

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformera data med en Hive-fråga

Det finns många sätt att köra ett Hive-jobb på ett HDInsight-kluster. I det här avsnittet använder du Beeline för att köra ett Hive-jobb. Information om andra metoder för att köra ett Hive-jobb finns i [Use Hive on HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md) (Använda Hive i HDInsight).

Som en del av Hive-jobbet importerar du data från .csv-filen till en Hive-tabell med namnet **Delays** (Fördröjningar).

1. Från SSH-frågan som du redan har för HDInsight-klustret använder du följande kommando för att skapa och redigera en ny fil med namnet **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Använd följande text som filens innehåll:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Om du vill spara filen trycker du på **Esc** och anger sedan `:x`.

3. Om du vill starta Hive och köra filen **flightdelays.hql** använder du följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. När skriptet __flightdelays.hql__ har körts klart använder du följande kommando för att öppna en interaktiv Beeline-session:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. När du får uppmaningen `jdbc:hive2://localhost:10001/>` ska du använda följande fråga för att hämta data från de importerade flygförseningsdata:

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Frågan returnerar en lista över städer som berörs av förseningar på grund av vädret samt genomsnittlig förseningstid och sparar det till `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Senare läser Sqoop data från den här platsen och exporterar dem till Azure SQL Database.

6. Om du vill avsluta Beeline skriver du `!quit` vid uppmaningen.

## <a name="create-a-sql-database-table"></a>Skapa en SQL-databastabell

Det här avsnittet förutsätter att du redan har skapat en Azure SQL-databas. Om du inte redan har en SQL-databas använder du informationen i [Create an Azure SQL database in the Azure portal](../../sql-database/sql-database-get-started.md) (Skapa en Azure SQL-databas i Azure-portalen) för att skapa en.

Om du redan har en SQL-databas måste du hämta servernamnet. För att hitta servernamnet i [Azure-portalen](https://portal.azure.com) väljer du **SQL-databaser** och filtrerar sedan på namnet på databasen du vill använda. Serverns namn finns i kolumnen **Servernamn**.

![Hämta information om Azure SQL-server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Hämta information om Azure SQL-server")

> [!NOTE]
> Det finns många sätt att ansluta till SQL Database och skapa en tabell. Följande steg använder [FreeTDS](http://www.freetds.org/) från HDInsight-klustret.


1. För att installera FreeTDS använder du följande kommando från en SSH-anslutning till klustret:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. När installationen är klar använder du följande kommando för att ansluta till SQL Database-servern. Ersätt **serverName** med SQL Database-servernamnet. Ersätt **adminLogin** och **adminPassword** med inloggningen för SQL Database. Ersätt **databaseName** med databasnamnet.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    När du blir ombedd anger du lösenordet till SQL Database-administratörsinloggningen.

    Du får utdata som liknar följande text:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Vid uppmaningen `1>` anger du följande rader:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    När instruktionen `GO` har angivits värderas de föregående instruktionerna. Den här frågan skapar en tabell som heter **delays** (fördröjningar), med ett grupperat index.

    Använd följande fråga för att verifiera att tabellen har skapats:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    De utdata som genereras liknar följande text:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. Skriv `exit` vid uppmaningen `1>` för att avsluta tsql-verktyget.


## <a name="export-data-to-sql-database-using-sqoop"></a>Exportera data till SQL Database med Sqoop

I föregående avsnitt kopierade du omvandlade data på `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. I det här avsnittet använder du Sqoop för att exportera data från `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` till tabellen du skapade i Azure SQL Database. 

1. Använd följande kommando för att verifiera att Sqoop kan se din SQL-databas:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    Det här kommandot returnerar en lista med databaser, däribland databasen som du skapade fördröjningstabellen i tidigare.

2. Använd följande kommando för att exportera data från hivesampletable till fördröjningstabellen:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    Sqoop ansluter till databasen som innehåller fördröjningstabellen och exporterar data från katalogen `/tutorials/flightdelays/output` till fördröjningstabellen.

3. När sqoop-kommandot avslutas använder du tsql-verktyget för att ansluta till databasen:

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    Använd följande instruktioner för att verifiera att data exporterades till fördröjningstabellen:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Du ska se en lista över data i tabellen. Tabellen innehåller stadens namn och genomsnittlig flygförseningstid för den staden. 

    Skriv `exit` för att avsluta tsql-verktyget.

## <a name="next-steps"></a>Nästa steg

Mer information om att arbeta med data i HDInsight finns i följande artiklar:

* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Utveckla Java MapReduce-program för Hadoop i HDInsight][hdinsight-develop-mapreduce]
* [Utveckla Python-strömmande MapReduce-program för HDInsight][hdinsight-develop-streaming]
* [Använda Oozie med HDInsight][hdinsight-use-oozie]
* [Använda Sqoop med HDInsight][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
