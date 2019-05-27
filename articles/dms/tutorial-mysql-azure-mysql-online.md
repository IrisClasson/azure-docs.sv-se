---
title: 'Självstudier: Använd Azure Database Migration Service för att utföra en online-migrering av MySQL till Azure Database for MySQL | Microsoft Docs'
description: Lär dig att utföra en online-migrering från MySQL på en plats till Azure Database för MySQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5a35df5b72f51f4ef725b3d764e7dc2c80c19ec2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240756"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Självstudier: Migrera MySQL till Azure Database for MySQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal MySQL-instans till [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) med minimal avbrottstid. Du kan med andra ord migrera med minimal stilleståndstid i programmet. I den här självstudien får du migrera den **anställda** exempeldatabasen från en lokal instans av MySQL 5.7 till Azure Database för MySQL med hjälp av en onlinemigrering aktivitet i Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempelschemat med verktyget mysqldump.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Med hjälp av Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans baserat på premiumprisnivån.

> [!IMPORTANT]
> För en optimal migreringsupplevelse Microsoft rekommenderar att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* Ladda ned och installera [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 eller 5.7. Den lokala MySQL-versionen måste överensstämma med Azure Database for MySQL-versionen. Exempelvis kan MySQL 5.6 endast migreras till Azure Database for MySQL 5.6 och inte uppgraderat till 5.7.
* [Skapa en instans i Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Läs artikeln [Använda MySQL Workbench för att ansluta och fråga efter data](https://docs.microsoft.com/azure/mysql/connect-workbench) om du vill ha information om hur du ansluter och skapar en databas med Azure-portalen.  
* Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i den [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/), och särskilt artiklarna i snabbstarten med stegvis information.

    > [!NOTE]
    > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
    > * Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
    > * Slutpunkt för lagring
    > * Service bus-slutpunkt
    >
    > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning.

* Se till att dina regler för Nätverkssäkerhetsgrupp kopplad till virtuella nätverk inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna din Windows-brandväggen så att Azure Database Migration Service att få åtkomst till källan MySQL-Server, vilket som standard är TCP-port 3306.
* När du använder en brandväggsinstallation framför din källdatabaserna, kan du behöva lägga till brandväggsregler som tillåter Azure Database Migration Service åtkomst till källdatabaserna för migrering.
* Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för Azure Database for MySQL att ge Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsintervallet för det virtuella nätverket som används för Azure Database Migration Service.
* MySQL-källan måste vara på en MySQL Community Edition som stöds. Om du vill kontrollera vilken version av MySQL-instansen du har går du till verktyget MySQL eller MySQL Workbench och kör följande kommando:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera MyISAM-tabeller till InnoDB kan du läsa artikeln [Konvertera tabeller från MyISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

* Aktivera binär loggning i filen my.ini (Windows) eller my.cnf (Unix) i källdatabasen med följande konfiguration:

  * **server_id** = 1 eller större (endast relevant för MySQL 5.6)
  * **log-bin** = \<sökväg > (gäller endast för MySQL 5.6) till exempel: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = rad
  * **Expire_logs_days** = 5 (Vi rekommenderar att du inte använder noll; gäller endast för MySQL 5.6)
  * **Binlog_row_image** = fullständig (endast relevant för MySQL 5.6)
  * **log_slave_updates** = 1

* Användaren måste ha rollen ReplicationAdmin med följande behörigheter:

  * **REPLIKERINGSKLIENT** – Krävs endast för ändringsbearbetningsuppgifter. Med andra ord kräver inte uppgifter för fullständig inläsning detta privilegium.
  * **REPLIKERINGSREPLIK** – Krävs endast för ändringsbearbetningsuppgifter. Med andra ord kräver inte uppgifter för fullständig inläsning detta privilegium.
  * **SUPER** – Krävs endast i versioner som är äldre än MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen. Du kan extrahera schema med mysqldump med parametern `--no-data`.

Om vi antar att du har MySQL **anställda** exempeldatabasen i det lokala systemet, kommandot för att göra schemamigreringen Använd mysqldump är:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Exempel:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Om du vill importera schemat till Azure Database for MySQL-mål kör du följande kommando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Exempel:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen.  Kör följande skript i MySQL Workbench för att extrahera skriptet släpp främmande nycklar och Lägg till främmande nycklar skript.

```
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Kör drop-sekundärnyckeln (som är den andra kolumnen) i frågeresultatet till drop-sekundärnyckeln.

> [!IMPORTANT]
> Om du importerar data med hjälp av en säkerhetskopia, tar du bort kommandona skapa DEFINER manuellt eller med hjälp av--skip-definer-kommando när du utför en mysqldump. DEFINER kräver super behörighet för att skapa och är begränsad i Azure Database för MySQL.

Om du har en utlösare i data (insert eller update utlösa), framtvingar dataintegriteten i mål-före replikerade data från källan. Rekommendationen är att inaktivera utlösare i alla tabeller för målet under migreringen och sedan aktivera utlösare när migreringen är klar.

Om du vill inaktivera utlösare i måldatabasen, använder du följande kommando:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Välj den prenumeration som du vill skapa en instans av Azure Database Migration Service och välj sedan **resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk eller skapa en ny.

    Det virtuella nätverket tillhandahåller Azure Database Migration Service åtkomst till SQL Server-källans och målets Azure SQL-databasinstans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta upp alla instanser av Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. På den **Azure Database Migration Service** skärmen, Sök efter namnet på Azure Database Migration Service-instans som du skapade och välj sedan instansen.

     ![Leta upp din instans av Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **MySQL**, i textrutan **Målservertyp** väljer du **AzureDbForMySQL**.
5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**

    ![Skapa Database Migration Service-projekt](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa projekt endast** att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara**, notera kraven för att använda DMS för att migrera data och välj sedan **Skapa och kör aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Lägg till källinformation** anger du anslutningsinformationen för MySQL-källinstansen.

    ![Skärmen Lägg till källinformation](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara** och ange på sidan **Målinformation** anslutningsinformationen för Azure Database for MySQL-målservern, som är den företablerade instansen av Azure Database for MySQL som schemat **Anställda**-distribuerades till med mysqldump.

    ![Skärmen Målinformation](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen, väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

     ![Aktivitetsstatus – slutförd](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Under **Databasnamn** väljer du en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for MySQL finns i artikeln [Known issues and workarounds with Azure Database for MySQL online migrations](known-issues-azure-mysql-online.md) (Kända problem och lösningar med Azure Database for MySQL-onlinemigreringar).
* Information om Azure Database Migration Service finns i artikeln [vad är Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Information om Azure Database för MySQL, finns i artikeln [vad är Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
