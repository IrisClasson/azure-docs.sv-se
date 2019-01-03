---
title: Kör Apache Sqoop jobb med hjälp av .NET och HDInsight - Azure
description: Lär dig hur du använder HDInsight .NET SDK för att köra Apache Sqoop-import och export mellan ett Apache Hadoop-kluster och en Azure SQL database.
keywords: sqoop jobb
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 3cd34bf67b0d796af71036e7d14834a061803973
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628092"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Kör Apache Sqoop jobb med hjälp av .NET SDK för Apache Hadoop i HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Azure HDInsight .NET SDK för att köra Apache Sqoop jobb i HDInsight för att importera och exportera mellan ett HDInsight-kluster och en Azure SQL database eller SQL Server-databas.

> [!NOTE]
> Men du kan använda procedurerna i den här artikeln med antingen ett Windows- eller Linux-baserade HDInsight-kluster, fungerar de bara från en Windows-klient. Använd flikväljaren överst i den här artikeln om du vill välja andra metoder.

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här självstudien måste du ha följande objekt:

* Ett Apache Hadoop-kluster i HDInsight. Mer information finns i [skapa ett kluster och en SQL-databas](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Använda Sqoop på HDInsight-kluster med .NET SDK
HDInsight .NET SDK tillhandahåller klientbibliotek för .NET, så att det är lättare att arbeta med HDInsight-kluster från .NET. I det här avsnittet skapar du ett C#-konsolprogram för att exportera hivesampletable till Azure SQL Database-tabell som du skapade tidigare i den här självstudien.

## <a name="submit-a-sqoop-job"></a>Skicka ett jobb med Sqoop

1. Skapa ett C#-konsolprogram i Visual Studio.

2. Från Visual Studio Package Manager-konsolen importerar du paketet genom att köra följande kommando för NuGet:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Använd följande kod i filen Program.cs:
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Om du vill köra programmet, Välj den **F5** nyckel. 

## <a name="limitations"></a>Begränsningar
Linux-baserade HDInsight visar följande begränsningar:

* Massexport: Sqoop-koppling som används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.

* Batchbearbetning: Med hjälp av den `-batch` växel, Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Oozie med HDInsight](../hdinsight-use-oozie.md): Använd Sqoop åtgärden i ett Oozie-arbetsflöde.
* [Analysera flygförseningsdata med HDInsight](../hdinsight-analyze-flight-delay-data.md): Använda Apache Hive för att analysera flygförseningsdata och Använd sedan Sqoop för att exportera data till en Azure SQL database.
* [Ladda upp data till HDInsight](../hdinsight-upload-data.md): Hitta andra metoder för att överföra data till HDInsight eller Azure Blob storage.

