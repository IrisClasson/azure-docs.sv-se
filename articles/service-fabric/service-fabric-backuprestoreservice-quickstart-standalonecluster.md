---
title: Regelbunden säkerhetskopiering och återställning i Azure Service Fabric | Microsoft Docs
description: Använd Service Fabric regelbunden säkerhetskopiering och återställning av funktionen för att aktivera säkerhetskopiering av periodiska data för dina programdata.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 154efffcb1f86907fefecc060419c1d9450470f8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237348"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Regelbunden säkerhetskopiering och återställning i Azure Service Fabric
> [!div class="op_single_selector"]
> * [Kluster på Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Fristående kluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric är en distribuerad systemplattform som gör det enkelt att utveckla och hantera tillförlitliga, distribuerat mikrotjänster som bygger molnprogram. Det tillåter körning av både tillståndslösa och tillståndskänsliga mikrotjänster. Tillståndskänsliga tjänster kan underhålla föränderligt och auktoritärt tillstånd bortom begäran och svaret eller hela transaktionen. Om en tillståndskänslig tjänst kraschar under en längre tid eller förlorar information på grund av en katastrof, kan det behöva återställas till en aktuell säkerhetskopia av dess tillstånd för att kunna fortsätta att tillhandahålla tjänsten när den visas.

Service Fabric replikerar tillståndet över flera noder så att tjänsten har hög tillgänglighet. Även om en nod i klustret misslyckas, fortsätter tjänsten att vara tillgängliga. I vissa fall, men är det fortfarande önskvärt service-data kan vara tillförlitlig mot bredare fel.
 
En tjänst kan till exempel vill säkerhetskopiera data för att skydda mot följande scenarier:
- Permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av replikeringar av en tjänstpartition
- Administrativa fel där tillståndet hämtar eller förstörs. En administratör med tillräcklig behörighet för tar till exempel felaktigt bort tjänsten.
- Buggar i tjänsten som orsaka skadade data. Det kan exempelvis ske när en kod tjänsteuppgraderingen börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kanske både koden och data återställas till ett tidigare tillstånd.
- Offline databearbetning. Det kan vara praktiskt att ha offline bearbetning av data för business intelligence som sker separat från den tjänst som genererar data.

Service Fabric tillhandahåller ett inbyggda API som tidpunkt [säkerhetskopierar och återställer](service-fabric-reliable-services-backup-restore.md). Utvecklare kan använda dessa API: er för att säkerhetskopiera tillståndet för tjänsten med jämna mellanrum. Administratörer kan utlösa en säkerhetskopia från utanför tjänsten vid en viss tidpunkt måste som innan du uppgraderar program, utvecklare även att exponera säkerhetskopiering (och återställa) som ett API från tjänsten. Att underhålla säkerhetskopiorna som är en ytterligare kostnad över detta. Du kanske vill göra fem inkrementella säkerhetskopior varje halvtimme följt av en fullständig säkerhetskopia. När du har en fullständig säkerhetskopiering kan du ta bort tidigare inkrementella säkerhetskopior. Den här metoden kräver ytterligare kod som leder till extra kostnad under programutvecklingen.

Säkerhetskopiering av programdata på regelbunden basis är ett grundläggande behov för att hantera ett distribuerat program och skydd mot förlust av data eller långvarig för tjänstens tillgänglighet. Service Fabric tillhandahåller ett valfritt säkerhetskopiering och återställning tjänst, där du kan konfigurera regelbunden säkerhetskopiering av tillståndskänsliga Reliable Services (inklusive Aktörstjänster) utan att behöva skriva ytterligare kod. Det underlättar också återställa utfört tidigare säkerhetskopior. 

Service Fabric tillhandahåller en uppsättning API: er för att uppnå följande funktioner relaterade till periodiska säkerhetskopia och återställa funktionen:

- Schemalägga regelbunden säkerhetskopiering av Reliable Stateful services och Reliable Actors med stöd för att ladda upp säkerhetskopiering till (extern) lagringsplatser. Stöds lagringsplatser
    - Azure Storage
    - Filresurs (lokalt)
- Räkna upp säkerhetskopior
- Utlös en ad hoc-säkerhetskopiering för en partition
- Återställa en partition med hjälp av föregående säkerhetskopia
- Tillfälligt säkerhetskopior
- Kvarhållning av säkerhetskopior (kommande)

## <a name="prerequisites"></a>Nödvändiga komponenter
* Service Fabric-kluster med Fabric version 6.2 och senare. Klustret ska ställas in på Windows Server. Referera till denna [artikeln](service-fabric-cluster-creation-for-windows-server.md) steg att ladda ned paket som krävs.
* X.509-certifikat för kryptering av hemligheter som behövs för att ansluta till storage för lagring av säkerhetskopior. Se [artikeln](service-fabric-windows-cluster-x509-security.md) kunskaper om att hämta eller skapa ett självsignerat X.509-certifikat.

* Service Fabric tillförlitliga tillståndskänsliga program som skapats med hjälp av Service Fabric SDK version 3.0 eller senare. För program som riktar in sig på .net Core 2.0, bör skapat programmet med hjälp av Service Fabric SDK version 3.1 eller senare.
* Installera Microsoft.ServiceFabric.Powershell.Http modulen [förhandsgranskning] för configuration anrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Se till att klustret är anslutna med hjälp av den `Connect-SFCluster` kommandot innan du gör några konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktivera säkerhetskopiera och återställa tjänsten
Först måste du aktivera den _säkerhetskopiera och återställa tjänsten_ i klustret. Hämta mallen för det kluster som du vill distribuera. Du kan använda den [exempel på mallar](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Aktivera den _säkerhetskopiera och återställa tjänsten_ med följande steg:

1. Kontrollera att den `apiversion` är inställd på `10-2017` i klusterkonfigurationen filen och om den inte uppdatera den som visas i följande kodavsnitt:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Nu kan du aktivera den _säkerhetskopiera och återställa tjänsten_ genom att lägga till följande `addonFeatures` avsnittet `properties` som visas i följande kodavsnitt: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurera X.509-certifikat för kryptering av autentiseringsuppgifter. Detta är viktigt att se till att autentiseringsuppgifterna, om sådant finns, att ansluta till storage krypteras innan de beständig lagring. Konfigurera certifikat för kryptering genom att lägga till följande `BackupRestoreService` avsnittet `fabricSettings` som visas i följande kodavsnitt: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. När du har uppdaterat din konfigurationsfil för klustret med föregående ändringarna, tillämpar dem och låta Slutför distribution/uppgradering. När det är klart, den _säkerhetskopiera och återställa tjänsten_ börjar köras i klustret. Uri för den här tjänsten är `fabric:/System/BackupRestoreService` och tjänsten kan finnas under system service-avsnittet i Service Fabric explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors
Låt oss gå igenom stegen för att aktivera regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors. Dessa steg förutsätter
- Klustret har konfigurerats med _säkerhetskopiera och återställa tjänsten_.
- En tillförlitlig tillståndskänslig tjänst har distribuerats på klustret. I den här snabbstartsguiden programmets Uri är `fabric:/SampleApp` och URI: N för tillförlitlig tillståndskänslig tjänst som hör till det här programmet är `fabric:/SampleApp/MyStatefulService`. Den här tjänsten har distribuerats med partition och partitions-ID är `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Skapa säkerhetskopieringsprincip

Första steget är att skapa princip för säkerhetskopiering som beskriver schemat för säkerhetskopiering, målstorleken för säkerhetskopierade data, principnamn, maximalt inkrementella säkerhetskopior som ska tillåtas innan du aktiverar fullständig säkerhetskopiering och kvarhållningsprincipen för lagring av säkerhetskopior. 

Skapa filresurs för lagring av säkerhetskopior, och ge ReadWrite-åtkomst till den här filresursen för alla datorer i Service Fabric-noden. Det här exemplet förutsätter resursen med namnet `BackupStore` finns på `StorageServer`.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av Powershell

Kör följande PowerShell-skript för att anropa den nödvändiga REST API för att skapa ny princip.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Aktivera regelbunden säkerhetskopiering
Principen för säkerhetskopiering ska vara associerat med programmet när du har definierat principen för att uppfylla kraven på dataskydd för programmet. Beroende på krav, kan säkerhetskopieringspolicyn som associeras med ett program, tjänst eller en partition.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av Powershell
Kör följande PowerShell-skript för att anropa den nödvändiga REST API för att associera säkerhetskopieringspolicyn med namnet `BackupPolicy1` i ovanstående steg med programmet `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Kontrollera att regelbundna säkerhetskopieringar fungerar

När du har aktiverat säkerhetskopiering för programmet, startas alla partitioner som hör till Reliable Stateful services och Reliable Actors under programmet komma säkerhetskopieras regelbundet enligt den tillhörande säkerhetskopieringsprincipen.

![Partition säkerhetskopierade Hälsotillståndshändelse][0]

### <a name="list-backups"></a>Lista över säkerhetskopior

Säkerhetskopieringar som är associerade med alla partitioner som hör till Reliable Stateful services och Reliable Actors för programmet kan att räkna upp med hjälp av _GetBackups_ API. Beroende på krav, kan du räkna upp säkerhetskopiorna för program, tjänst eller en partition.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av Powershell

Kör följande PowerShell-skript för att anropa HTTP-API för att räkna upp de säkerhetskopior som har skapats för alla partitioner i den `SampleApp` program.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Exempel på utdata för ovanstående kör:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="limitation-caveats"></a>Begränsningen / varningar
- Service Fabric PowerShell-cmdlet: ar är i förhandsgranskningsläge.
- Inget stöd för Service Fabric-kluster på Linux.

## <a name="next-steps"></a>Nästa steg
- [Förstå periodiska säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST API-referens för Backup restore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

