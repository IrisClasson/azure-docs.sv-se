---
title: Säkerhetskopiera SQL Server-databaser till Azure
description: This article explains how to back up SQL Server to Azure. Den här artikeln beskriver även återställning av SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 39f2348a95be95a03dada45d48952dce99ec4ec7
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462584"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Om SQL Server-säkerhetskopiering i virtuella Azure-datorer

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett lågt mål för återställningspunkt (RPO) och långsiktig kvarhållning. You can back up SQL Server databases running on Azure VMs using [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Backup process

This solution leverages the SQL native APIs to take backups of your SQL databases.

* Once you specify the SQL Server VM that you want to protect and query for the databases in it, Azure Backup service will install a workload backup extension on the VM by the name `AzureBackupWindowsWorkload` extension.
* This extension consists of a coordinator and a SQL plugin. While the coordinator is responsible for triggering workflows for various operations like configure backup, backup and restore, the plugin is responsible for actual data flow.
* To be able to discover databases on this VM, Azure Backup creates the account `NT SERVICE\AzureWLBackupPluginSvc`. This account is used for backup and restore and requires SQL sysadmin permissions. The `NT SERVICE\AzureWLBackupPluginSvc` account is a [Virtual Service Account](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts), and therefore does not require any password management. Azure Backup leverages the `NT AUTHORITY\SYSTEM` account for database discovery/inquiry, so this account needs to be a public login on SQL. Om du inte skapade SQL Server-datorn från Azure Marketplace kan det hända att du får felet **UserErrorSQLNoSysadminMembership**. Om det inträffar [följer du de här instruktionerna](#set-vm-permissions).
* Once you trigger configure protection on the selected databases, the backup service sets up the coordinator with the backup schedules and other policy details, which the extension caches locally on the VM.
* At the scheduled time, the coordinator communicates with the plugin and it starts streaming the backup data from the SQL server using VDI.  
* The plugin sends the data directly to the recovery services vault, thus eliminating the need for a staging location. The data is encrypted and stored by the Azure Backup service in storage accounts.
* When the data transfer is complete, coordinator confirms the commit with the backup service.

  ![SQL Backup architecture](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Innan du börjar

Before you start, verify the below:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) på Marketplace.
2. Review the [feature consideration](#feature-consideration-and-limitations) and [scenario support](#scenario-support).
3. [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Distributioner som stöds** | Virtuella SQL Marketplace Azure-datorer och virtuella icke-Marketplace-datorer (manuellt installerat SQL Server) stöds.
**Geografiska områden som stöds** | Australia South East (ASE), East Australia (AE), Australia Central (AC), Australia Central 2 (AC) <br> Brasilien, södra (BRS)<br> Canada Central (CNC), Canada East (CE)<br> South East Asia (SEA), East Asia (EA) <br> East US (EUS), East US 2 (EUS2), West Central US (WCUS), West US (WUS); West US 2 (WUS 2) North Central US (NCUS) Central US (CUS) South Central US (SCUS) <br> India Central (INC), India South (INS), India West <br> Japan East (JPE), Japan West (JPW) <br> Korea Central (KRC), Korea South (KRS) <br> North Europe (NE), West Europe <br> UK South (UKS), UK West (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Germany North, Germany West Central <br> Switzerland North, Switzerland West <br> Frankrike, centrala <br> China East, China East 2, China North, China North 2
**Operativsystem som stöds** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux stöds inte för närvarande.
**SQL Server-versioner som stöds** | SQL Server 2019, SQL Server 2017 as detailed on the [Search product lifecycle page](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 and SPs as detailed on the [Search product lifecycle page](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Supported .NET versions** | .NET Framework 4.5.2 or later installed on the VM

## <a name="feature-consideration-and-limitations"></a>Feature consideration and limitations

* SQL Server backup can be configured in the Azure portal or **PowerShell**. We do not support CLI.
* The solution is supported on both kinds of [deployments](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) - Azure Resource Manager VMs and classic VMs.
* VM running SQL Server requires internet connectivity to access Azure public IP addresses.
* SQL Server **Failover Cluster Instance (FCI)** is not supported.
* Back up and restore operations for mirror databases and database snapshots aren't supported.
* Using more than one backup solutions to back up your standalone SQL Server instance or SQL Always on availability group may lead to backup failure; refrain from doing so.
* Backing up two nodes of an availability group individually with same or different solutions, may also lead to backup failure.
* Azure Backup supports only Full and Copy-only Full backup types for **Read-only** databases
* Databaser med ett stort antal filer kan inte skyddas. The maximum number of files that is supported is **~1000**.  
* You can back up to **~2000** SQL Server databases in a vault. You can create multiple vaults in case you have a greater number of databases.
* You can configure backup for up to **50** databases in one go; this restriction helps optimize backup loads.
* We support databases up to **2 TB** in size; for sizes greater than that performance issues may come up.
* To have a sense of as to how many databases can be protected per server, we need to consider factors such as bandwidth, VM size, backup frequency, database size, etc. [Download](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) the resource planner that gives the approximate number of databases you can have per server based on the VM resources and the backup policy.
* In case of availability groups, backups are taken from the different nodes based on a few factors. The backup behavior for an availability group is summarized below.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Back up behavior in case of Always on availability groups

It is recommended that the backup is configured on only one node of an AG. Backup should always be configured in the same region as the primary node. In other words, you always need the primary node to be present in the region in which you are configuring backup. If all the nodes of the AG are in the same region in which the backup is configured, there isn’t any concern.

#### <a name="for-cross-region-ag"></a>For cross-region AG

* Regardless of the backup preference, backups won’t happen from the nodes that are not in the same region where the backup is configured. This is because the cross-region backups are not supported. If you have only two nodes and the secondary node is in the other region; in this case, the backups will continue to happen from primary node (unless your backup preference is ‘secondary only’).
* If a fail-over happens to a region different than the one in which the backup is configured, backups would fail on the nodes in the failed-over region.

Depending on the backup preference and backups types (full/differential/log/copy-only full), backups are taken from a particular node (primary/secondary).

* **Backup preference: Primary**

**Backup Type** | **Node**
    --- | ---
    Fullständig | Primär
    Differential | Primär
    Logg |  Primär
    Copy-Only Full |  Primär

* **Backup preference: Secondary Only**

**Backup Type** | **Node**
--- | ---
Fullständig | Primär
Differential | Primär
Logg |  Sekundär
Copy-Only Full |  Sekundär

* **Backup preference: Secondary**

**Backup Type** | **Node**
--- | ---
Fullständig | Primär
Differential | Primär
Logg |  Sekundär
Copy-Only Full |  Sekundär

* **No Backup preference**

**Backup Type** | **Node**
--- | ---
Fullständig | Primär
Differential | Primär
Logg |  Sekundär
Copy-Only Full |  Sekundär

## <a name="set-vm-permissions"></a>Ange VM-behörigheter

  When you run discovery on a SQL Server, Azure Backup does the following:

* Adds the AzureBackupWindowsWorkload extension.
* Creates an NT SERVICE\AzureWLBackupPluginSvc account to discover databases on the virtual machine. This account is used for a backup and restore and requires SQL sysadmin permissions.
* Discovers databases that are running on a VM, Azure Backup uses the NT AUTHORITY\SYSTEM account. This account must be a public sign-in on SQL.

If you didn't create the SQL Server VM in the Azure Marketplace or if you are on SQL 2008 and 2008 R2, you might receive a **UserErrorSQLNoSysadminMembership** error.

For giving permissions in case of **SQL 2008** and **2008 R2** running on Windows 2008 R2, refer [here](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

For all other versions, fix permissions with the following steps:

  1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
  2. På SQL Server öppnar du mappen **Security/Logins** (Säkerhet/Inloggningar).

      ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

  3. Högerklicka på mappen **Logins** och välj **Ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

      ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

  4. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Klicka på **OK**

      ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)

  5. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Klicka på **OK** De behörigheter som krävs bör nu finnas.

      ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nu associerar du databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

      ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

      ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> If your SQL Server has multiple instances of SQL Server installed, then you must add sysadmin permission for **NT Service\AzureWLBackupPluginSvc** account to all SQL instances.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Give SQL sysadmin permissions for SQL 2008 and SQL 2008 R2

Add **NT AUTHORITY\SYSTEM** and **NT Service\AzureWLBackupPluginSvc** logins to the SQL Server Instance:

1. Go the SQL Server Instance in the Object explorer.
2. Navigate to Security -> Logins
3. Right click on the Logins and click *New Login…*

    ![New Login using SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Go to the General tab and enter **NT AUTHORITY\SYSTEM** as the Login Name.

    ![login name for SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Go to *Server Roles* and choose *public* and *sysadmin* roles.

    ![choosing roles in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Go to *Status*. *Grant* the Permission to connect to database engine and Login as *Enabled*.

    ![Grant permissions in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Klicka på OK.
8. Repeat the same sequence of steps (1-7 above) to add NT Service\AzureWLBackupPluginSvc login to the SQL Server instance. If the login already exists, make sure it has the sysadmin server role and under Status it has Grant the Permission to connect to database engine and Login as Enabled.
9. After granting permission, **Rediscover DBs** in the portal: Vault **->** Backup Infrastructure **->** Workload in Azure VM:

    ![Rediscover DBs in Azure portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternatively, you can automate giving the permissions by running the following PowerShell commands in admin mode. The instance name is set to MSSQLSERVER by default. Change the instance name argument in script if need be:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Nästa steg

* [Learn about](backup-sql-server-database-azure-vms.md) backing up SQL Server databases.
* [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
* [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.
