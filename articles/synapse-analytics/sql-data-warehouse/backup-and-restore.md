---
title: Säkerhets kopiering och återställning – ögonblicks bilder, Geo-redundant
description: Lär dig hur säkerhets kopiering och återställning fungerar i Azure Synapse Analytics SQL-poolen. Använd säkerhets kopior för att återställa data lagret till en återställnings punkt i den primära regionen. Använd geo-redundanta säkerhets kopieringar för att återställa till en annan geografisk region.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d4a08035b03c104555c39311bfb812218cca44b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482555"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Säkerhets kopiering och återställning i Azure Synapse SQL-poolen

Lär dig hur du använder säkerhets kopiering och återställning i Azure Synapse SQL-poolen. Använd återställnings punkter för SQL-pool för att återställa eller kopiera data lagret till ett tidigare tillstånd i den primära regionen. Använd informations lager geo-redundanta säkerhets kopieringar för att återställa till en annan geografisk region.

## <a name="what-is-a-data-warehouse-snapshot"></a>Vad är en ögonblicks bild av informations lagret

En *ögonblicks bild av ett informations lager* skapar en återställnings punkt som du kan använda för att återställa eller kopiera data lagret till ett tidigare tillstånd.  Eftersom SQL-poolen är ett distribuerat system består en ögonblicks bild av ett informations lager av många filer som finns i Azure Storage. Ögonblicks bilder fångar stegvisa ändringar från data som lagras i ditt informations lager.

En *data lager återställning* är ett nytt informations lager som skapas från en återställnings punkt för ett befintligt eller Borttaget data lager. Att återställa ditt informations lager är en viktig del av en affärs kontinuitet och katastrof återställnings strategi eftersom den återskapar dina data efter haveri skada eller borttagning. Data lagret är också en kraftfull mekanism för att skapa kopior av ditt informations lager i test-eller utvecklings syfte.  Återställnings takten för SQL-pooler kan variera beroende på databasens storlek och platsen för käll-och mål data lagret.

## <a name="automatic-restore-points"></a>Automatiska återställningspunkter

Ögonblicks bilder är en inbyggd funktion som skapar återställnings punkter. Du behöver inte aktivera den här funktionen. SQL-poolen bör dock vara i ett aktivt tillstånd för skapande av återställnings punkter. Om SQL-poolen har pausats ofta kan automatiska återställnings punkter inte skapas, så se till att skapa en användardefinierad återställnings punkt innan du pausar SQL-poolen. Det går inte att ta bort automatiska återställnings punkter av användare eftersom tjänsten använder dessa återställnings punkter för att underhålla service avtal för återställning.

Ögonblicks bilder av ditt informations lager tas under dagen som skapar återställnings punkter som är tillgängliga i sju dagar. Kvarhållningsperioden kan inte ändras. SQL-poolen har stöd för ett återställnings punkt mål på 8 timmar. Du kan återställa ditt informations lager i den primära regionen från någon av de ögonblicks bilder som tagits under de senaste sju dagarna.

Kör den här frågan på SQL-poolen online om du vill se när den senaste ögonblicks bilden startade.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

Med den här funktionen kan du utlösa ögonblicks bilder manuellt för att skapa återställnings punkter för ditt informations lager innan och efter stora ändringar. Den här funktionen säkerställer att återställnings punkter logiskt konsekvent, vilket ger ytterligare data skydd i händelse av arbets belastnings avbrott eller användar fel för snabb återställnings tid. Användardefinierade återställnings punkter är tillgängliga i sju dagar och tas automatiskt bort för din räkning. Det går inte att ändra kvarhållningsperioden för användardefinierade återställnings punkter. **42 användardefinierade återställnings punkter** garanteras vid varje tidpunkt så att de måste [tas bort](https://go.microsoft.com/fwlink/?linkid=875299) innan en annan återställnings punkt skapas. Du kan utlösa ögonblicks bilder för att skapa användardefinierade återställnings punkter via [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) eller Azure Portal.

> [!NOTE]
> Om du behöver återställnings punkter längre än 7 dagar bör du rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Du kan också skapa en användardefinierad återställnings punkt och återställa från den nyligen skapade återställnings punkten till ett nytt data lager. När du har återställt har du SQL-poolen online och kan pausa den under obegränsad tid för att spara beräknings kostnader. Den pausade databasen ådrar sig lagrings kostnader med Azure Premium Storage rate. Om du behöver en aktiv kopia av det återställda informations lagret kan du återuppta vilket som bör ta några minuter.

### <a name="restore-point-retention"></a>Kvarhållning av återställnings punkt

Följande visar information om återställnings punkter för återställnings punkter:

1. SQL-poolen tar bort en återställnings punkt när den får en kvarhållningsperiod på 7 dagar **och** när det finns minst 42 sammanlagt återställnings punkter (inklusive både användardefinierad och automatisk).
2. Ögonblicks bilder utförs inte när en SQL-pool har pausats.
3. En återställnings punkts ålder mäts av de absoluta kalender dagarna från den tidpunkt då återställnings punkten tas, inklusive när SQL-poolen har pausats.
4. Vid en viss tidpunkt är en SQL-pool garanterat att kunna lagra upp till 42 användardefinierade återställnings punkter och 42 automatiska återställnings punkter så länge de här återställnings punkterna inte har nått lagrings perioden på 7 dagar
5. Om en ögonblicks bild tas pausas SQL-poolen i mer än 7 dagar och återupptas sedan återställnings punkten tills det finns 42 totala återställnings punkter (inklusive både användardefinierad och automatisk)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Kvarhållning av ögonblicks bilder när en SQL-pool släpps

När du släpper en SQL-pool skapas en slutgiltig ögonblicks bild som sparas i sju dagar. Du kan återställa SQL-poolen till den sista återställnings punkten som skapas vid borttagning. Om SQL-poolen släpps i pausat läge tas ingen ögonblicks bild. I det scenariot ser du till att skapa en användardefinierad återställnings punkt innan du släpper SQL-poolen.

> [!IMPORTANT]
> Om du tar bort servern som är värd för en SQL-pool, tas även alla databaser som tillhör servern bort och kan inte återställas. Det går inte att återställa en borttagen Server.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-säkerhets kopiering och haveri beredskap

En geo-säkerhetskopiering skapas en gång per dag till ett [parat Data Center](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Återställningen för en geo-återställning är 24 timmar. Du kan återställa geo-säkerhetskopiering till en server i någon annan region där SQL-poolen stöds. En geo-säkerhetskopiering garanterar att du kan återställa data lagret om du inte kan komma åt återställnings punkterna i din primära region.

> [!NOTE]
> Om du behöver en kortare återställnings punkt för geo-säkerhetskopiering, röst för den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse). Du kan också skapa en användardefinierad återställnings punkt och återställa från den nyligen skapade återställnings punkten till ett nytt informations lager i en annan region. När du har återställt har du data lagret online och kan pausa det oändligt för att spara beräknings kostnader. Den pausade databasen ådrar sig lagrings kostnader med Azure Premium Storage rate. Om du behöver en aktiv kopia av data lagret kan du fortsätta vilket som bör ta några minuter.

## <a name="backup-and-restore-costs"></a>Säkerhetskopiera och återställa kostnader

Observera att Azure-fakturan har ett rad objekt för lagring och ett rad objekt för haveri beredskaps lagring. Lagrings avgiften är den totala kostnaden för att lagra dina data i den primära regionen tillsammans med de stegvisa ändringar som fångas av ögonblicks bilder. En mer detaljerad förklaring av hur ögonblicks bilder debiteras finns i [förstå hur ögonblicks](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)bilder debiteras. Den geo-redundanta avgiften täcker kostnaden för lagring av Geo-säkerhetskopieringar.  

Den totala kostnaden för ditt primära informations lager och sju dagars ögonblicks bilds ändringar avrundas till närmaste TB. Om ditt informations lager till exempel är 1,5 TB och ögonblicks bilderna fångar 100 GB debiteras du för 2 TB data med Azure Premium Storage priser.

Om du använder Geo-redundant lagring får du en separat lagrings avgift. Geo-redundant lagring debiteras enligt standard priset för Läs åtkomst till geografiskt redundant lagring (RA-GRS).

Mer information om priser för Azure Synapse finns i [priser för Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Du debiteras inte för utgående data vid återställning mellan regioner.

## <a name="restoring-from-restore-points"></a>Återställa från återställnings punkter

Varje ögonblicks bild skapar en återställnings punkt som representerar tidpunkten då ögonblicks bilden startades. Om du vill återställa ett informations lager väljer du en återställnings punkt och utfärdar kommandot Restore.  

Du kan antingen behålla det återställda informations lagret och det aktuella, eller ta bort ett av dem. Om du vill ersätta det aktuella data lagret med det återställda data lagret kan du byta namn på det med hjälp av [Alter Database (SQL pool)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) med alternativet ändra namn.

Information om hur du återställer ett informations lager finns i [återställa en SQL-pool](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Om du vill återställa ett borttaget eller pausat data lager kan du [skapa ett support ärende](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Återställning mellan prenumerationer

Om du behöver återställa direkt mellan prenumerationen kan du rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Återställ till en annan server och [Flytta](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) servern över prenumerationer för att utföra en återställning mellan prenumerationer.

## <a name="geo-redundant-restore"></a>Geo-redundant återställning

Du kan [återställa SQL-poolen](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) till vilken region som helst som stöder SQL-poolen på den valda prestanda nivån.

> [!NOTE]
> Om du vill utföra en Geo-redundant återställning får du inte välja att använda den här funktionen.

## <a name="next-steps"></a>Nästa steg

Mer information om återställnings punkter finns i [användardefinierade återställnings punkter](sql-data-warehouse-restore-points.md)