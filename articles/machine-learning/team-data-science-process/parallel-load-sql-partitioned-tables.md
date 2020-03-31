---
title: Parallell massimport av massdata i SQL-partitionstabeller - Team Data Science Process
description: Skapa partitionerade tabeller för snabb parallell massimport av data till en SQL Server-databas.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721344"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Skapa och optimera tabeller för snabb parallellimport av data till en SQL Server på en virtuell Azure-dator

I den här artikeln beskrivs hur du skapar partitionerade tabeller för snabb parallell massimport av data till en SQL Server-databas. För inläsning/överföring av stordata till en SQL-databas kan importen av data till SQL DB och efterföljande frågor förbättras med hjälp av *partitionerade tabeller och vyer*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Skapa en ny databas och en uppsättning filgrupper
* [Skapa en ny databas](https://technet.microsoft.com/library/ms176061.aspx), om den inte redan finns.
* Lägg till databasfilgrupper i databasen, som innehåller de partitionerade fysiska filerna. 
* Detta kan göras med [SKAPA DATABAS](https://technet.microsoft.com/library/ms176061.aspx) om ny eller [ÄNDRA DATABAS](https://msdn.microsoft.com/library/bb522682.aspx) om databasen redan finns.
* Lägg till en eller flera filer (efter behov) i varje databasfilgrupp.
  
  > [!NOTE]
  > Ange målfilgruppen, som innehåller data för den här partitionen och de fysiska databasfilnamnen där filgruppsdata lagras.
  > 
  > 

I följande exempel skapas en ny databas med tre andra filgrupper än primär- och logggrupperna, som innehåller en fysisk fil i varje. Databasfilerna skapas i standardmappen FÖR SQL Server-data, som konfigurerats i SQL Server-instansen. Mer information om standardfilplatser finns i [Filplatser för standard- och namngivna instanser av SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Skapa en partitionerad tabell
Om du vill skapa partitionerade tabeller enligt dataschemat, mappade till de databasfilgrupper som skapats i föregående steg, måste du först skapa en partitionsfunktion och schema. När data massimporteras till de partitionerade tabellerna fördelas poster mellan filgrupperna enligt ett partitionsschema enligt beskrivningen nedan.

### <a name="1-create-a-partition-function"></a>1. Skapa en partitionsfunktion
[Skapa en partitionsfunktion](https://msdn.microsoft.com/library/ms187802.aspx) Den här funktionen definierar det intervall av värden/gränser som ska ingå i varje enskild\_partitionstabell, till exempel för att begränsa partitioner per månad (något datetime-fält)\_år 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Skapa ett partitionsschema
[Skapa ett partitionsschema](https://msdn.microsoft.com/library/ms179854.aspx). Det här schemat mappar varje partitionsintervall i partitionsfunktionen till en fysisk filgrupp, till exempel:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Om du vill verifiera de intervall som gäller i varje partition enligt funktionen/schemat kör du följande fråga:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Skapa en partitionstabell
[Skapa partitionerade tabeller](https://msdn.microsoft.com/library/ms174979.aspx)enligt ditt dataschema och ange det partitionsschema och villkorsfält som används för att partitionera tabellen, till exempel:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Mer information finns i [Skapa partitionerade tabeller och index](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Massimportera data för varje enskild partitionstabell

* Du kan använda BCP, BULK INSERT eller andra metoder, till exempel [guiden SQL Server-migrering](https://sqlazuremw.codeplex.com/). I exemplet används BCP-metoden.
* [Ändra databasen](https://msdn.microsoft.com/library/bb522682.aspx) för att ändra transaktionsloggningsschemat till BULK_LOGGED för att minimera omkostnaderna för loggning, till exempel:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Om du vill påskynda datainläsningen startar du massimportåtgärderna parallellt. Tips om hur du snabbt importerar stordata till SQL Server-databaser finns [i Läs in 1 TB](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)på mindre än 1 timme .

Följande PowerShell-skript är ett exempel på parallella datainläsning med BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Skapa index för att optimera kopplingar och frågeprestanda
* Om du extraherar data för modellering från flera tabeller skapar du index på kopplingsnycklarna för att förbättra kopplingsprestanda.
* [Skapa index](https://technet.microsoft.com/library/ms188783.aspx) (klustrade eller icke-klustrade) som är inriktade på samma filgrupp för varje partition, till exempel:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Eller
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Du kan välja att skapa indexen innan du importerar data i grupp. Index skapas innan massimportering saktar ner datainläsningen.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Exempel på avancerad analysprocess och teknik i åtgärd
Ett exempel på genomgång från på nytt med hjälp av Team Data Science Process med en offentlig datauppsättning finns [i Team Data Science Process in Action: använda SQL Server](sql-walkthrough.md).

