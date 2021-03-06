---
title: Migrera ett HBase-kluster till en ny version – Azure HDInsight
description: Så här migrerar du Apache HBase-kluster till en nyare version i Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 23843743b58db91d457b509fa38243f110b76b41
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079555"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Migrera ett Apache HBase-kluster till en ny version

Den här artikeln beskriver de steg som krävs för att uppdatera ditt Apache HBase-kluster på Azure HDInsight till en nyare version.

Drift stoppet vid uppgradering bör vara minimalt i ordningen på minuter. Den här stillestånds tiden orsakas av stegen för att tömma alla InMemory-data, sedan hur lång tid det tar att konfigurera och starta om tjänsterna på det nya klustret. Resultaten varierar beroende på antalet noder, mängden data och andra variabler.

## <a name="review-apache-hbase-compatibility"></a>Granska Apache HBase-kompatibilitet

Innan du uppgraderar Apache HBase kontrollerar du att HBase-versionerna på käll-och mål klustren är kompatibla. Mer information finns i [Apache Hadoop komponenter och versioner som är tillgängliga med HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> Vi rekommenderar starkt att du granskar [HBase-adressbokens](https://hbase.apache.org/book.html#upgrading)versions kompatibilitet. Eventuella avbrytande inkompatibiliteter bör beskrivas i versions information för HBase.

Här är ett exempel på en versions mat ris. Y indikerar kompatibilitet och N anger en möjlig inkompatibilitet:

| Typ av kompatibilitet | Huvud version| Lägre version | Patch |
| --- | --- | --- | --- |
| Kompatibilitet med klient serverns tråd | N | J | Y |
| Server-Server-kompatibilitet | N | J | Y |
| Kompatibilitet för fil format | N | J | Y |
| Kompatibilitet för klient-API | N | J | Y |
| Klient-binär kompatibilitet | N | N | J |
| **Begränsad API-kompatibilitet på Server Sidan** |  |  |  |
| Stable | N | J | Y |
| Utvecklas | N | N | J |
| Instabil | N | N | N |
| Beroende kompatibilitet | N | J | Y |
| Operationell kompatibilitet | N | N | J |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Uppgradera med samma Apache HBase huvud version

Utför följande steg för att uppgradera ditt Apache HBase-kluster på Azure HDInsight:

1. Kontrol lera att programmet är kompatibelt med den nya versionen, som du ser i HBase och viktig information. Testa ditt program i ett kluster som kör mål versionen av HDInsight och HBase.

1. Skapa [ett nytt mål HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md) med samma lagrings konto, men med ett annat behållar namn:

    ![Använd samma lagrings konto, men skapa en annan behållare](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Rensa ditt käll HBase-kluster, vilket är det kluster som du uppgraderar. HBase skriver inkommande data till ett minnes intern lager, som kallas för en _memstores_. När memstores har nått en viss storlek tömmer HBase den till disk för långsiktig lagring i klustrets lagrings konto. När du tar bort det gamla klustret återvinns memstores, vilket kan förlora data. Om du vill tömma memstores manuellt för varje tabell till disk kör du följande skript. Den senaste versionen av det här skriptet finns på Azures [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh).

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Stoppa inmatningen till det gamla HBase-klustret.

1. Kör föregående skript igen för att säkerställa att alla nyligen använda data i memstores töms.

1. Logga in på [Apache Ambari](https://ambari.apache.org/) på det gamla klustret ( `https://OLDCLUSTERNAME.azurehdidnsight.net` ) och stoppa HBase-tjänsterna. När du uppmanas att bekräfta att du vill stoppa tjänsterna ska du markera kryss rutan för att aktivera underhålls läget för HBase. Mer information om hur du ansluter till och använder Ambari finns i [Hantera HDInsight-kluster med hjälp av Ambari-WEBBgränssnittet](../hdinsight-hadoop-manage-ambari.md).

    ![I Ambari klickar du på tjänster > HBase > stoppa under tjänst åtgärder](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Markera kryss rutan Aktivera underhålls läge för HBase och bekräfta sedan](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Logga in på Ambari på det nya HDInsight-klustret. Ändra `fs.defaultFS` HDFS-inställningen så att den pekar på det behållar namn som används av det ursprungliga klustret. Den här inställningen finns under **HDFS > configs > avancerad > Advanced Core-site**.

    ![I Ambari klickar du på tjänster > HDFS > config > Avancerat](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![I Ambari ändrar du behållar namnet](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Hoppa över det här steget om du inte använder HBase-kluster med funktionen för förbättrade skrivningar. Det behövs bara för HBase-kluster med förbättrade skrivningar.

   Ändra `hbase.rootdir` sökvägen så att den pekar på den ursprungliga klustrets behållare.

    ![I Ambari ändrar du behållar namnet för HBase rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Om du uppgraderar HDInsight 3,6 till 4,0 följer du stegen nedan, annars hoppar du till steg 10:
    1. Starta om alla nödvändiga tjänster i Ambari genom att välja **tjänster**som  >  **startas om allt krävs**.
    1. Stoppa HBase-tjänsten.
    1. SSH till Zookeeper-noden och köra kommandot [zkCli](https://github.com/go-zkcli/zkcli) `rmr /hbase-unsecure` för att ta bort HBase root-znode från Zookeeper.
    1. Starta om HBase.

1. Följ dessa steg om du uppgraderar till en annan HDInsight-version än 4,0:
    1. Spara ändringarna.
    1. Starta om alla nödvändiga tjänster som anges av Ambari.

1. Peka ditt program till det nya klustret.

    > [!NOTE]  
    > Den statiska DNS-filen för programmet ändras när du uppgraderar. I stället för att hårdkoda den här DNS-konfigurationen kan du konfigurera en CNAME-post i domän namnets DNS-inställningar som pekar på klustrets namn. Ett annat alternativ är att använda en konfigurations fil för ditt program som du kan uppdatera utan att distribuera om.

1. Starta inmatningen för att se om allt fungerar som förväntat.

1. Om det nya klustret är tillfredsställande tar du bort det ursprungliga klustret.

## <a name="next-steps"></a>Nästa steg

Mer information om [Apache HBase](https://hbase.apache.org/) och uppgradering av HDInsight-kluster finns i följande artiklar:

* [Uppgradera ett HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md)
* [Övervaka och hantera Azure HDInsight med hjälp av Apache Ambari-webbgränssnittet](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop komponenter och versioner](../hdinsight-component-versioning.md)
* [Optimera Apache HBase](../optimize-hbase-ambari.md)
