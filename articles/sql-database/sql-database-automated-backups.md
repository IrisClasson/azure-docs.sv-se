---
title: Automatisk, geo-redundanta säkerhetskopieringar i Azure SQL Database | Microsoft Docs
description: SQL-databas och skapar en lokal databassäkerhetskopia några minuters mellanrum och använder Azure läsåtkomst till geografiskt redundant lagring för geo-redundans automatiskt.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/20/2019
ms.openlocfilehash: 1c81f5748d1e3edff4902eb462b9beea78acd8bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65951642"
---
# <a name="automated-backups"></a>Automatiserade säkerhetskopieringar

SQL-databas skapas automatiskt säkerhetskopior av databasen sparas mellan 7 och 35 dagar och använder Azure [läsåtkomst till geografiskt redundant lagring (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) så att de bevaras även om datacentret är tillgänglig. Dessa säkerhetskopior skapas automatiskt och utan extra kostnad. Du behöver inte göra något för att det ska hända. Säkerhetskopior av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Om din säkerhetsregler kräver att dina säkerhetskopior som är tillgängliga under en längre tid (upp till 10 år), kan du konfigurera en [långsiktig kvarhållning](sql-database-long-term-retention.md) på Singleton-databaser och elastiska pooler.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Vad är en SQL Database-säkerhetskopia

SQL Database använder SQL Server-teknik för att skapa [fullständiga säkerhetskopieringar](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) varje vecka, [differentiella säkerhetskopieringar](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) var tolfte timme och [säkerhetskopieringar av transaktionsloggen](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) var 5 – 10 minuter. Säkerhetskopiorna lagras i [lagringsblobar för RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) som replikeras till en [kopplat Datacenter](../best-practices-availability-paired-regions.md) för skydd mot avbrott i datacentret. När du återställer en databas kan databas tjänsten där fullständig, differentiell och transaktionen loggfiler säkerhetskopieringar ska återställas.

Du kan använda dessa säkerhetskopior till:

- **Återställa en befintlig databas till point-in-time tidigare** inom kvarhållningsperioden med hjälp av Azure portal, Azure PowerShell, Azure CLI eller REST API. I enkel databas och elastiska pooler skapar den här åtgärden en ny databas på samma server som den ursprungliga databasen. I Managed Instance kan den här åtgärden skapar en kopia av databasen eller samma eller en annan hanterad instans i samma prenumeration.
  - **[Ändra säkerhetskopiering kvarhållningsperiod](#how-to-change-the-pitr-backup-retention-period)**  mellan till 35 dagar för att konfigurera din säkerhetskopieringsprincip.
  - **Ändra principen för långsiktig kvarhållning upp till 10 år** på enkel databas och elastiska pooler med hjälp av [Azure-portalen](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) eller [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups).
- **Återställa en borttagen databas till den tidpunkt när den har tagits bort** eller när som helst inom kvarhållningsperioden. Den borttagna databasen kan endast återställas i samma logiska server eller hanterad instans där den ursprungliga databasen har skapats.
- **Återställa en databas till en annan geografisk region**. GEO-återställning kan du återställa en geografisk katastrofåterställning när du inte åtkomst till din server och databas. En ny databas skapas i en befintlig server var som helst i världen.
- **Återställa en databas från en specifik långsiktig säkerhetskopiering** på enkel databas eller elastisk Pool om databasen har konfigurerats med en princip för långsiktig kvarhållning av säkerhetskopior (LTR). LTR kan du återställa en äldre version av en databas med hjälp av [Azure-portalen](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) eller [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) att uppfylla en begäran om efterlevnad eller för att köra en äldre version av programmet. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).
- Om du vill utföra en återställning, se [återställa databasen från säkerhetskopior](sql-database-recovery-using-backups.md).

> [!NOTE]
> I Azure storage termen *replikering* syftar på kopiering av filer från en plats till en annan. SQL: s *databasreplikering* refererar till att hålla flera sekundära databaser synkroniseras med en primär databas.

Du kan prova något av dessa åtgärder med hjälp av följande exempel:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Ändra kvarhållning av säkerhetskopior | [Enkel databas](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Hanterad instans](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Enkel databas](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Ändra långsiktig kvarhållning av säkerhetskopior | [Enkel databas](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Hanterad instans - ej tillämpligt  | [Enkel databas](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>Hanterad instans - ej tillämpligt  |
| Återställa databasen från point-in-time | [Enkel databas](sql-database-recovery-using-backups.md#point-in-time-restore) | [Enkel databas](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Återställa borttagen databas | [Enkel databas](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Enkel databas](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Återställa databasen från Azure Blob Storage | Enkel databas - ej tillämpligt <br/>Hanterad instans - ej tillämpligt  | Enkel databas - ej tillämpligt <br/>[Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Hur lång tid hålls säkerhetskopior

Varje SQL-databas har en standard-kvarhållningsperiod mellan 7 och 35 dagar som beror på inköpsmodellen och tjänstnivå. Du kan uppdatera kvarhållningsperioden för säkerhetskopior för en databas i SQL Database-server. Mer information finns i [ändra kvarhållningsperiod](#how-to-change-the-pitr-backup-retention-period).

Om du tar bort en databas, behåller SQL Database säkerhetskopiorna på samma sätt som den skulle ha gjort för en online-databas. Om du tar bort en Basic-databas som har en kvarhållningsperiod på sju dagar, till exempel sparas en säkerhetskopia som är fyra dagar gamla i tre dagar.

Om du vill behålla säkerhetskopior under längre tid än den högsta bevarandeperioden kan ändra du egenskaper för säkerhetskopiering för att lägga till en eller flera långsiktiga kvarhållningsperioder till din databas. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Om du tar bort Azure SQL-servern som är värd för SQL-databaser, raderas också alla elastiska pooler och databaser som hör till servern och kan inte återställas. Du kan inte återställa en borttagen server. Men om du har konfigurerat långsiktig kvarhållning kommer inte att ta bort säkerhetskopior för databaser med LTR och dessa databaser kan återställas.

### <a name="default-backup-retention-period"></a>Standardvärdet för kvarhållning av säkerhetskopior

#### <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

Standardkvarhållningsperioden för en databas som skapats med den DTU-baserade inköpsmodellen beror på tjänstnivån:

- Grundläggande tjänstenivå är **en** vecka.
- Standard-tjänstnivå är **fem** veckor.
- Premiumnivån är **fem** veckor.

#### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

Om du använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md), standard-kvarhållningsperiod är **sju** dagar (för en enskild, pooler och databaser-instans). För alla Azure SQL-databaser (enkel, pooler, och instansdatabaser, kan du [ändra kvarhållningsperioden för säkerhetskopior upp till 35 dagar](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Om du minskar den aktuella kvarhållningsperioden är alla befintliga säkerhetskopior som är äldre än den nya kvarhållningsperioden inte längre tillgängliga. Om du ökar den aktuella kvarhållningsperioden, behåller SQL Database befintliga säkerhetskopior tills den längre kvarhållningsperioden har uppnåtts.

## <a name="how-often-do-backups-happen"></a>Hur ofta händer säkerhetskopior

### <a name="backups-for-point-in-time-restore"></a>Säkerhetskopieringar för point-in-time-återställning

SQL Database stöder Självbetjäning för point-in-time-återställning (PITR) genom att automatiskt skapa fullständig säkerhetskopiering, differentiella säkerhetskopieringar och säkerhetskopieringar av transaktionsloggen. Fullständiga databassäkerhetskopieringar skapas varje vecka, differentiella säkerhetskopieringar skapas vanligtvis var 12: e timme och säkerhetskopieringar av transaktionsloggen skapas vanligtvis var 5-10 minut, med frekvensen baserat på beräkningsstorleken och mängden Databasaktivitet. Den första fullständiga säkerhetskopian schemaläggs omedelbart efter att en databas har skapats. Slutförs vanligtvis inom 30 minuter, men det kan ta längre tid när databasen är av betydande storlek. Den första säkerhetskopieringen kan till exempel ta längre tid på en återställd databas eller en databaskopia. Efter den första fullständiga säkerhetskopian är alla ytterligare säkerhetskopieringar schemalagda automatiskt och hanteras tyst i bakgrunden. Exakta tidsinställningen för alla databassäkerhetskopior bestäms av tjänsten SQL Database som balanseras övergripande systemets arbetsbelastning. Du kan inte ändra eller inaktivera säkerhetskopieringsjobben. 

PITR säkerhetskopiorna är geo-redundant och skyddas av [tvärregional Azure Storage-replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Mer information finns i [Point-in-time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Säkerhetskopior för långsiktig kvarhållning

Enkel och delade databaser erbjuder alternativet att konfigurera långsiktig kvarhållning av säkerhetskopior (LTR) fullständiga säkerhetskopior för upp till 10 år i Azure Blob storage. Om LTR principen är aktiverad, kopieras de veckovisa, fullständiga säkerhetskopiorna automatiskt till en annan behållare för RA-GRS-lagring. Du kan välja olika kvarhållningsperioder för veckovisa, månatliga och årliga säkerhetskopior för att uppfylla olika krav. Lagringsanvändningen beror på den valda frekvensen för säkerhetskopiering och kvarhållning längd. Du kan använda den [LTR priskalkylator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) att uppskatta kostnaden för LTR-lagring.

Precis som PITR, LTR-säkerhetskopior är geo-redundant och skyddas av [Azure Storage-replikering mellan tillgänglighetszoner](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Mer information finns i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Lagringskostnader
Automatiserade säkerhetskopieringar för sju dagar av dina databaser kopieras till RA-GRS standardbloblagring som standard. Lagringsutrymmet används av veckovisa, fullständiga säkerhetskopior, dagliga differentiella säkerhetskopior och säkerhetskopior av transaktionsloggar var femte minut. Storleken på transaktionsloggen beror på ändringsfrekvensen i databasen. En minimimängd lagringsutrymme motsvarande 100 procent av databasens storlek tillhandahålls utan extra kostnad. Ytterligare förbrukning av lagringsenhet för säkerhetskopior debiteras för GB/månad.

Mer information om priserna för lagring finns i den [priser](https://azure.microsoft.com/pricing/details/sql-database/single/) sidan. 

## <a name="are-backups-encrypted"></a>Krypteras säkerhetskopior

Om databasen är krypterad med transparent Datakryptering krypteras automatiskt säkerhetskopior i vila, inklusive LTR säkerhetskopior. Säkerhetskopiorna krypteras också när TDE är aktiverat för en Azure SQL-databas. Alla nya Azure SQL-databaser är konfigurerade med TDE aktiverat som standard. Läs mer på TDE [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Hur säkerställa integriteten för säkerhetskopiering av Microsoft

Med jämna mellanrum testar det tekniska teamet för Azure SQL Database automatiskt återställning av automatiska säkerhetskopior av databaser för tjänsten. Vid en återställning får databaser även integritetskontroller med hjälp av DBCC CHECKDB. Eventuella problem som hittades under integritetskontrollen resulterar i en avisering till teknikteamet. Mer information om dataintegriteten i Azure SQL Database finns i [dataintegriteten i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Hur påverkar efterlevnad av automatiska säkerhetskopieringar

När du migrerar din databas från en DTU-baserade tjänstnivå med standard PITR kvarhållning av 35 dagar till en vCore-baserade tjänstnivå bevaras PITR kvarhållning för att säkerställa att ditt programs data återställningsprincipen inte komprometteras. Om Standardkvarhållning inte uppfyller efterlevnadskraven kan ändra du kvarhållningsperioden PITR med PowerShell eller REST API. Mer information finns i [ändra kvarhållningsperiod](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Så här ändrar du den PITR kvarhållningsperioden

Du kan ändra standard PITR kvarhållningsperioden för säkerhetskopior med hjälp av Azure portal, PowerShell eller REST API. Värdena som stöds är: 7, 14, 21, 28 eller 35 dagar. I följande exempel visas hur du ändrar PITR kvarhållning till 28 dagar.

> [!NOTE]
> Dessa API: er påverkar endast PITR kvarhållningsperioden. Om du har konfigurerat LTR för din databas kommer det inte att påverkas. Läs mer om hur du ändrar de LTR kvarhållning perioder [långsiktig kvarhållning](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Ändra PITR kvarhållningsperiod för säkerhetskopiering med Azure portal

Ändra PITR kvarhållningsperioden för säkerhetskopior med Azure-portalen, gå till server-objekt vars kvarhållningsperioden som du vill ändra i portalen och väljer sedan ett lämpligt alternativ baserat på vilken server-objekt du ändrar.

#### <a name="change-pitr-for-a-sql-database-server"></a>Ändra PITR för en SQL Database-server

![Ändra PITR Azure-portalen](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Ändra PITR för en hanterad instans

![Ändra PITR Azure-portalen](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Ändra PITR kvarhållningsperiod för säkerhetskopior med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Ändra PITR kvarhållningsperiod med REST API

#### <a name="sample-request"></a>Exempelförfrågan

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Begärandetext

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exempelsvar

Statuskod: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Mer information finns i [säkerhetskopiering kvarhållning REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Nästa steg

- Säkerhetskopior av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Läs mer om de andra Azure SQL Database lösningarna för affärskontinuitet, i [översikt över affärskontinuitet](sql-database-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure-portalen, se [återställa databasen till en tidpunkt med hjälp av Azure-portalen](sql-database-recovery-using-backups.md).
- Om du vill återställa till en tidpunkt med hjälp av PowerShell, se [återställa databasen till en tidpunkt med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
- Att konfigurera, hantera och återställa från långsiktig kvarhållning av automatiska säkerhetskopior i Azure Blob storage med Azure portal, se [hantera långsiktig kvarhållning av säkerhetskopior med Azure portal](sql-database-long-term-backup-retention-configure.md).
- Att konfigurera, hantera och återställa från långsiktig kvarhållning av automatiska säkerhetskopior i Azure Blob storage med hjälp av PowerShell, se [hantera långsiktig kvarhållning av säkerhetskopior med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).
