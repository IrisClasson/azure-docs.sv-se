---
title: Migrera till nya Elastic Database-jobb | Microsoft Docs
description: Migrera till nya Elastic Database-jobb.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: johnpaulkee
ms.author: joke
ms.reviewer: ''
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: 4470a13dfe462671b1a58a217e679d2b40236c6a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873979"
---
# <a name="migrate-to-the-new-elastic-database-jobs"></a>Migrera till nya elastiska databasjobb

En uppgraderad version av [elastiska Databasjobb](elastic-jobs-overview.md) är tillgänglig.

Om du har en befintlig kund finns version av [elastiska Databasjobb](sql-database-elastic-jobs-overview.md), migrerings-cmdlets och skript tillhandahålls för att enkelt migrera till den senaste versionen.


## <a name="prerequisites"></a>Förutsättningar

Uppgradering av Elastic Database-jobb har en ny uppsättning PowerShell-cmdletar för användning under migreringen. Dessa nya cmdletarna överför alla dina befintliga jobbautentiseringsuppgifter mål (inklusive databaser, servrar, anpassade samlingar), jobb-utlösare, scheman för datalagerjobb, jobbet innehållet och jobb över till en ny elastisk jobbagent.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Installera de senaste elastiska jobb-cmdletarna

Om du inte redan har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Installera **AzureRM.Sql** 4.8.1-förhandsmodulen för att få de senaste elastiska jobb-cmdletarna. Kör följande kommandon i PowerShell med administratörsbehörighet.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -RequiredVersion 4.8.1-preview -Force

# Import the AzureRM.Sql 4.8.1 module
Import-Module AzureRM.Sql -RequiredVersion 4.8.1

# Confirm if module successfully imported - if the imported version is 4.8.1, then continue
Get-Module AzureRM.Sql
```

### <a name="create-a-new-elastic-job-agent"></a>Skapa en ny elastisk jobbagent

Skapa en ny elastisk jobbagent när du har installerat de nya cmdletarna.

```powershell
# Register your subscription for the for the Elastic Jobs public preview feature
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Get an existing database to use as the job database - or create a new one if necessary
$db = Get-AzureRmSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName>
# Create a new elastic job agent
$agent = $db | New-AzureRmSqlElasticJobAgent -Name <agentName>
```

### <a name="install-the-old-elastic-database-jobs-cmdlets"></a>Installera gamla cmdlets för Elastic Database-jobb

Migreringen måste använda en del av den *gamla* Elastiskt jobb cmdletar, så kör du följande kommandon om du inte redan har dem installerade.

```powershell
# Install the old elastic job cmdlets if necessary and initialize the old jobs cmdlets
.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

# Install the old jobs cmdlets
cd Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools
Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
.\InstallElasticDatabaseJobsCmdlets.ps1

# Choose the subscription where your existing jobs are
Select-AzureRmSubscription -SubscriptionId <subscriptionId>
Use-AzureSqlJobConnection -CurrentAzureSubscription -Credential (Get-Credential)
```



## <a name="migration"></a>Migrering

När båda de gamla och nya elastiska jobb-cmdletarna initieras, migrera din jobbautentiseringsuppgifter, mål och jobb till den nya *jobbet databasen*.

### <a name="setup"></a>Konfiguration

```powershell
$ErrorActionPreference = "Stop";

# Helper function to show starting write output
function Log-StartOutput ($output) {
  Write-Output ("`r--------------------- " + $output + " ---------------------")
}

# Helper function to show starting write output
function Log-ChildOutput ($output) {
  Write-Output ("  - " + $output)
}
```



### <a name="migrate-credentials"></a>Migrera autentiseringsuppgifter

```powershell
function Migrate-Credentials ($agent) {
    Log-StartOutput "Migrating credentials"

    $oldCreds = Get-AzureSqlJobCredential
    $oldCreds | % {
        $oldCredName = $_.CredentialName
        $oldUserName = $_.UserName
        Write-Output ("Credential " + $oldCredName)
        $oldCredential = Get-Credential -UserName $oldUserName `
                         -Message ("Please enter in the password that was used for your credential " + $oldCredName)
        try
        {
            $cred = New-AzureRmSqlElasticJobCredential -ParentObject $agent -Name $oldCredName -Credential $oldCredential
        }
        catch [System.Management.Automation.PSArgumentException]
        {
            $cred = Get-AzureRmSqlElasticJobCredential -ParentObject $agent -Name $oldCredName
            $cred = Set-AzureRmSqlElasticJobCredential -InputObject $cred -Credential $oldCredential
        }

        Log-ChildOutput ("Added user " + $oldUserName)
    }
}
```

Om du vill migrera dina autentiseringsuppgifter för att köra följande kommando genom att skicka in den `$agent` PowerShell-objekt från tidigare.

```powershell
Migrate-Credentials $agent
```

Exempel på utdata

```powershell
# You should see similar output after executing the above
# --------------------- Migrating credentials ---------------------
# Credential cred1
#  - Added user user1
# Credential cred2
#  - Added user user2
# Credential cred3
#  - Added user user3
```

### <a name="migrate-targets"></a>Migrera mål

```powershell
function Migrate-TargetGroups ($agent) {
    Log-StartOutput "Migrating target groups"

    # Setup hash of target groups
    $targetGroups = [ordered]@{}

    # Fetch root job targets from old service
    $rootTargets = Get-AzureSqlJobTarget

    # Return if no root targets are found
    if ($rootTargets.Count -eq 0)
    {
        Write-Output "No targets found - no need for migration"
        return
    }

    # Create list of target groups to create
    # We format the target group name as such:
    # - If root target is server type, then target group name is "(serverName)"
    # - If root target is database type, then target group name is "(serverName,databaseName)"
    # - If root target is shard map type, then target group name is "(serverName,databaseName,shardMapName)"
    # - If root target is custom collection, then target group name is "customCollectionName"
    $rootTargets | % {
        $tgName = Format-OldTargetName -target $_
        $childTargets = Get-ChildTargets -target $_
        $targetGroups.Add($tgName, $childTargets)
    }

    # Flatten list
    for ($i=$targetGroups.Count - 1; $i -ge 0; $i--)
    {
        # Fetch target group's initial list of targets unexpanded
        $targets = $targetGroups[$i]

        # Expand custom collection targets
        $j = 0;
        while ($j -lt $targets.Count)
        {
            $target = $targets[$j]
            if ($target.TargetType -eq "CustomCollection")
            {
                $targets = [System.Collections.ArrayList] $targets
                $targets.Remove($target) # Remove this target from the list

                $expandedTargets = $targetGroups[$target.TargetDescription.CustomCollectionName]

                foreach ($expandedTarget in $expandedTargets)
                {
                    $targets.Add($expandedTarget) | Out-Null
                }

                # Set updated list of targets for tg
                $targetGroups[$i] = $targets
                # Note we don't increment here in case we need to expand further
            }
            else
            {
                # Skip if no custom collection target needs to be expanded
                $j++
            }
        }
    }

    # Add targets to target group
    foreach ($targetGroup in $targetGroups.Keys)
    {
        $tg = Setup-TargetGroup -tgName $targetGroup -agent $agent
        $targets = $targetGroups[$targetGroup]
        Migrate-Targets -targets $targets -tg $tg
        $targetsAdded = (Get-AzureRmSqlElasticJobTargetGroup -ParentObject $agent -Name $tg.TargetGroupName).Targets
        foreach ($targetAdded in $targetsAdded)
        {
            Log-ChildOutput ("Added target " + (Format-NewTargetName $targetAdded))
        }
    }
}

## Target group helpers
# Migrate shard map target from old jobs to new job's target group
function Migrate-Targets ($targets, $tg) {
  Write-Output ("Target group " + $tg.TargetGroupName)
  foreach ($target in $targets) {
    if ($target.TargetType -eq "Server") {
      Add-ServerTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "Database") {
      Add-DatabaseTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "ShardMap") {
      Add-ShardMapTarget -target $target -tg $tg
    }
  }
}

# Migrate server target from old jobs to new job's target group
function Add-ServerTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $credName = $jobTarget.MasterDatabaseCredentialName
  $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -RefreshCredentialName $credName
}

# Migrate database target from old jobs to new job's target group
function Add-DatabaseTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $databaseName = $jobTarget.DatabaseName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName -Exclude
  }
  else {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName
  }
}

# Migrate shard map target from old jobs to new job's target group
function Add-ShardMapTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $smName = $jobTarget.ShardMapName
  $serverName = $jobTarget.ShardMapManagerServerName
  $databaseName = $jobTarget.ShardMapManagerDatabaseName
  $credName = $jobTarget.ShardMapManagerCredentialName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName -Exclude
  }
  else {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName
  }
}

# Helper to format target old target names
function Format-OldTargetName ($target) {
  if ($target.TargetType -eq "Server") {
    $tgName = "(" + $target.ServerName + ")"
  }
  elseif ($target.TargetType -eq "Database") {
    $tgName = "(" + $target.ServerName + "," + $target.DatabaseName + ")"
  }
  elseif ($target.TargetType -eq "ShardMap") {
    $tgName = "(" + $target.ShardMapManagerServerName + "," +
    $target.ShardMapManagerDatabaseName + "," + `
      $target.ShardMapName + ")"
  }
  elseif ($target.TargetType -eq "CustomCollection") {
    $tgName = $target.CustomCollectionName
  }

  return $tgName
}

# Helper to format new target names
function Format-NewTargetName ($target) {
  if ($target.TargetType -eq "SqlServer") {
    $tgName = "(" + $target.TargetServerName + ")"
  }
  elseif ($target.TargetType -eq "SqlDatabase") {
    $tgName = "(" + $target.TargetServerName + "," + $target.TargetDatabaseName + ")"
  }
  elseif ($target.TargetType -eq "SqlShardMap") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetShardMapName + ")"
  }
  elseif ($target.TargetType -eq "SqlElasticPool") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetElasticPoolName + ")"
  }

  return $tgName
}

# Get child targets
function Get-ChildTargets($target) {
  if ($target.TargetType -eq "CustomCollection") {
    $children = Get-AzureSqlJobChildTarget -TargetId $target.TargetId
    if ($children.Count -eq 1)
    {
        $arr = New-Object System.Collections.ArrayList($null)
        $arr.Add($children)
        $children = $arr
    }
    return $children
  }
  else {
    return $target
  }
}

# Migrates target groups
function Setup-TargetGroup ($tgName, $agent) {
  try {
    $tg = New-AzureRmSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
  catch [System.Management.Automation.PSArgumentException] {
    $tg = Get-AzureRmSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
}
```

Om du vill migrera dina mål (servrar, databaser och anpassade samlingar) till den nya databasen för jobbet måste köra den **migrera TargetGroups** cmdlet för att utföra följande:

- Roten på mål som är servrar och databaser kommer att migreras till en ny målgrupp med namnet ”(\<serverName\>, \<databaseName\>)” som innehåller endast rot på målet.
- En anpassad samling migrerar till en ny målgrupp som innehåller alla underordnade mål.

```powershell
Migrate-TargetGroups $agent
```

Exempel på utdata:

```powershell
# --------------------- Migrating target groups ---------------------
# Target group cc1
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group cc2
#   - Added target (s1,db1)
# Target group cc3
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group (s1,db1)
#   - Added target (s1,db1)
# Target group (s1,db2)
#   - Added target (s1,db2)
# Target group (s1)
#   - Added target (s1)
# Target group (s1,db1,sm1)
#   - Added target (s1,db1,sm1)
```



### <a name="migrate-jobs"></a>Migrera jobb

```powershell
function Migrate-Jobs ($agent)
{
    Log-StartOutput "Migrating jobs and job steps"

    $oldJobs = Get-AzureSqlJob
    $newJobs = [System.Collections.ArrayList] @()

    foreach ($oldJob in $oldJobs)
    {
        # Ignore system jobs
        if ($oldJob.ContentName -eq $null)
        {
            continue
        }

        # Schedule
        $oldJobTriggers = Get-AzureSqlJobTrigger -JobName $oldJob.JobName

        if ($oldJobTriggers.Count -ge 1)
        {
            foreach ($trigger in $oldJobTriggers)
            {

                $schedule = Get-AzureSqlJobSchedule -ScheduleName $trigger.ScheduleName
                $newJob = [PSCustomObject] @{
                    JobName  = ($trigger.JobName + " (" + $trigger.ScheduleName + ")");
                    Description = $oldJob.ContentName
                    Schedule = $schedule
                    TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                    CredentialName = $oldJob.CredentialName
                    Output = $oldJob.ResultSetDestination
                }
                $newJobs.Add($newJob) | Out-Null
            }
        }
        else
        {
            $newJob = [PSCustomObject] @{
                JobName  = $oldJob.JobName
                Description = $oldJob.ContentName
                Schedule = $null
                TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                CredentialName = $oldJob.CredentialName
                Output = $oldJob.ResultSetDestination
            }
            $newJobs.Add($newJob) | Out-Null
        }
    }

    # At this point, we should have an organized list of jobs to create
    foreach ($newJob in $newJobs)
    {
        Write-Output ("Job " + $newJob.JobName)
        $job = Setup-Job $newJob $agent
        If ($job.Interval -ne $null)
        {
            Log-ChildOutput ("Schedule with start time " + $job.StartTime + " and end time at " + $job.EndTime)
            Log-ChildOutput ("Repeats every " + $job.Interval)
        }
        else {
            Log-ChildOutput ("Repeats once")
        }

        Setup-JobStep $newJob $job
    }
}

# Migrates jobs
function Setup-Job ($job, $agent) {
  $jobName = $newJob.JobName
  $jobDescription = $newJob.Description

  # Create or update a job has a recurring schedule
  if ($newJob.Schedule -ne $null) {
    $schedule = $newJob.Schedule
    $startTime = $schedule.StartTime.UtcTime
    $endTime = $schedule.EndTime.UtcTime
    $intervalType = $schedule.Interval.IntervalType.ToString()
    $intervalType = $intervalType.Substring(0, $intervalType.Length - 1) # Remove the last letter (s)
    $intervalCount = $schedule.Interval.Count

    try {
      $job = New-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzureRmSqlElasticJob -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
  }
  # Create or update a job that runs once
  else {
    try {
      $job = New-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -RunOnce
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzureRmSqlElasticJob -Description $jobDescription -RunOnce
      return $job
    }
  }
}
# Migrates job steps
function Setup-JobStep ($newJob, $job) {
  $defaultJobStepName = 'JobStep'
  $contentName = $newJob.Description
  $commandText = (Get-AzureSqlJobContentDefinition -ContentName $contentName).CommandText
  $targetGroupName = $newJob.TargetGroupName
  $credentialName = $newJob.CredentialName

  $output = $newJob.Output

  if ($output -ne $null) {
    $outputServerName = $output.TargetDescription.ServerName
    $outputDatabaseName = $output.TargetDescription.DatabaseName
    $outputCredentialName = $output.CredentialName
    $outputSchemaName = $output.SchemaName
    $outputTableName = $output.TableName
    $outputDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $job.ResourceGroupName -ServerName $outputServerName -Databasename $outputDatabaseName

    try {
      $jobStep = $job | Add-AzureRmSqlElasticJobStep -Name $defaultJobStepName `
        -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzureRmSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzureRmSqlElasticJobStep -TargetGroupName $targetGroupName `
        -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
  }
  else {
    try {
      $jobStep = $job | Add-AzureRmSqlElasticJobStep -Name $defaultJobStepName -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzureRmSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzureRmSqlElasticJobStep -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
  }
  Log-ChildOutput ("Added step " + $jobStep.StepName + " using target group " + $jobStep.TargetGroupName + " using credential " + $jobStep.CredentialName)
  Log-ChildOutput("Command text script taken from content name " + $contentName)

  if ($jobStep.Output -ne $null) {
    Log-ChildOutput ("With output target as (" + $jobStep.Output.ServerName + "," + $jobStep.Output.DatabaseName + "," + $jobStep.Output.SchemaName + "," + $jobStep.Output.TableName + ")")
  }
}
```

Om du vill migrera din jobb, jobbet innehåll, utlösare för jobb och jobbscheman över till din nya Elastiskt jobb agent-databas, köra den **migrera jobb** cmdlet skicka ditt ombud.

- Jobb med flera utlösare med olika scheman är indelade i flera jobb med namngivningsschemat ”:\<jobName\> (\<scheduleName\>)”.
- Jobbet innehållet migreras till ett jobb genom att lägga till en standard-jobbsteg med namnet JobStep med associerade Kommandotext.
- Jobben är inaktiverade som standard så att du kan validera dem innan du aktiverar dem.

```powershell
Migrate-Jobs $agent
```

Exempel på utdata:
```powershell
--------------------- Migrating jobs and job steps ---------------------
Job job1
  - Repeats once
  - Added step JobStep using target group cc2 using credential cred1
  - Command text script taken from content name SampleContext
Job job2
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
  - With output target as (s1,db1,dbo,sampleTable)
Job job3 (repeat every 10 min)
  - Schedule with start time 05/16/2018 22:05:28 and end time at 12/31/9999 11:59:59
  - Repeats every PT10M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job3 (repeat every 5 min)
  - Schedule with start time 05/16/2018 22:05:31 and end time at 12/31/9999 11:59:59
  - Repeats every PT5M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job4
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
```



## <a name="migration-complete"></a>Migreringen har slutförts

Den *jobbet databasen* bör nu ha alla jobbautentiseringsuppgifter, mål, utlösare för jobbet, scheman för datalagerjobb, jobbet innehållet och jobb som migreras.

Kontrollera att allt migrerats korrekt genom att använda följande skript:

```powershell
$creds = $agent | Get-AzureRmSqlElasticJobCredential
$targetGroups = $agent | Get-AzureRmSqlElasticJobTargetGroup
$jobs = $agent | Get-AzureRmSqlElasticJob
$steps = $jobs | Get-AzureRmSqlElasticJobStep
```

Testa att jobb körs korrekt genom att starta dem:

```powershell
$jobs | Start-AzureRmSqlElasticJob
```

Kom ihåg att aktivera dem så att de kan köras i bakgrunden för alla jobb som körs enligt ett schema:

```powershell
$jobs | Set-AzureRmSqlElasticJob -Enable
```

## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell.md)
- [Skapa och hantera elastiska jobb med Transact-SQL (T-SQL)](elastic-jobs-tsql.md)