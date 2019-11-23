---
title: Konfigurera Azure Active Directory-autentisering
description: Learn how to connect to SQL Database, managed instance, and SQL Data Warehouse by using Azure Active Directory Authentication - after configuring Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 11/06/2019
ms.openlocfilehash: 5830e0b7ee49a7d954dbdb3f897ee7ac5901c6a5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421755"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configure and manage Azure Active Directory authentication with SQL

This article shows you how to create and populate Azure AD, and then use Azure AD with Azure [SQL Database](sql-database-technical-overview.md), [managed instance](sql-database-managed-instance.md), and [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). For an overview, see [Azure Active Directory Authentication](sql-database-aad-authentication.md).

> [!NOTE]
> This article applies to Azure SQL server, and to both SQL Database and SQL Data Warehouse databases that are created on the Azure SQL server. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]  
> Connecting to SQL Server running on an Azure VM is not supported using an Azure Active Directory account. Use a domain Active Directory account instead.

## <a name="create-and-populate-an-azure-ad"></a>Create and populate an Azure AD

Create an Azure AD and populate it with users and groups. Azure AD can be the initial Azure AD managed domain. Azure AD can also be an on-premises Active Directory Domain Services that is federated with the Azure AD.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD](../active-directory/active-directory-domains-add-azure-portal.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](../active-directory/fundamentals/active-directory-administer.md) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](/powershell/azure/overview) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associate or add an Azure subscription to Azure Active Directory

1. Associate your Azure subscription to Azure Active Directory by making the directory a trusted directory for the Azure subscription hosting the database. For details, see [How Azure subscriptions are associated with Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Use the directory switcher in the Azure portal to switch to the subscription associated with domain.

   > [!IMPORTANT]
   > Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna. For more information about resources, see [Understanding resource access in Azure](../active-directory/active-directory-b2b-admin-add-users.md). To learn more about this trusted relationship see [How to associate or add an Azure subscription to Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Create an Azure AD administrator for Azure SQL server

Each Azure SQL server (which hosts a SQL Database or SQL Data Warehouse) starts with a single server administrator account that is the administrator of the entire Azure SQL server. A second SQL Server administrator must be created, that is an Azure AD account. This principal is created as a contained database user in the master database. As administrators, the server administrator accounts are members of the **db_owner** role in every user database, and enter each user database as the **dbo** user. For more information about the server administrator accounts, see [Managing Databases and Logins in Azure SQL Database](sql-database-manage-logins.md).

When using Azure Active Directory with geo-replication, the Azure Active Directory administrator must be configured for both the primary and the secondary servers. If a server does not have an Azure Active Directory administrator, then Azure Active Directory logins and users receive a "Cannot connect" to server error.

> [!NOTE]
> Users that are not based on an Azure AD account (including the Azure SQL server administrator account), cannot create Azure AD-based users, because they do not have permission to validate proposed database users with the Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provision an Azure Active Directory administrator for your managed instance

> [!IMPORTANT]
> Only follow these steps if you are provisioning a managed instance. This operation can only be executed by Global/Company administrator or a Privileged Role Administrator in Azure AD. Following steps describe the process of granting permissions for users with different privileges in directory.

> [!NOTE]
> For Azure AD admins for MI created prior to GA, but continue operating post GA, there is no functional change to the existing behavior. For more information, see the [New Azure AD admin functionality for MI](#new-azure-ad-admin-functionality-for-mi) section for more details.

Your managed instance needs permissions to read Azure AD to successfully accomplish tasks such as authentication of users through security group membership or creation of new users. For this to work, you need to grant permissions to managed instance to read Azure AD. There are two ways to do it: from Portal and PowerShell. The following steps both methods.

1. In the Azure portal, in the upper-right corner, select your connection to drop down a list of possible Active Directories.

2. Välj rätt Active Directory som standard-Azure AD.

   This step links the subscription associated with Active Directory with Managed Instance making sure that the same subscription is used for both Azure AD and the Managed Instance.

3. Navigate to Managed Instance and select one that you want to use for Azure AD integration.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Select the banner on top of the Active Directory admin page and grant permission to the current user. If you're logged in as Global/Company administrator in Azure AD, you can do it from the Azure portal or using PowerShell with the script below.

    ![grant permissions-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. After the operation is successfully completed, the following notification will show up in the top-right corner:

    ![lyckades](./media/sql-database-aad-authentication/success.png)

6. Now you can choose your Azure AD admin for your managed instance. For that, on the Active Directory admin page, select **Set admin** command.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. In the AAD admin page, search for a user, select the user or group to be an administrator, and then select **Select**.

   The Active Directory admin page shows all members and groups of your Active Directory. Users or groups that are grayed out can't be selected because they aren't supported as Azure AD administrators. See the list of supported admins in [Azure AD Features and Limitations](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Role-based access control (RBAC) applies only to the Azure portal and isn't propagated to SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. At the top of the Active Directory admin page, select **Save**.

    ![save](./media/sql-database-aad-authentication/save.png)

    Processen med att ändra administratör kan ta några minuter. Then the new administrator appears in the Active Directory admin box.

After provisioning an Azure AD admin for your managed instance, you can begin to create Azure AD server principals (logins) with the <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> syntax. For more information, see [managed instance overview](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> To later remove an Admin, at the top of the Active Directory admin page, select **Remove admin**, and then select **Save**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>New Azure AD admin functionality for MI

The table below summarizes the functionality for the public preview Azure AD login admin for MI, versus a new functionality delivered with GA for Azure AD logins.

| Azure AD login admin for MI during public preview | GA functionality for Azure AD admin for MI |
| --- | ---|
| Behaves in a similar way as Azure AD admin for SQL Database, which enables Azure AD authentication, but the Azure AD admin cannot create Azure AD or SQL logins in the master db for MI. | Azure AD admin has sysadmin permission and can create AAD and SQL logins in master db for MI. |
| Is not present in the sys.server_principals view | Is present in the sys.server_principals view |
| Allows individual Azure AD guest users to be set up as Azure AD admin for MI. For more information, see [Add Azure Active Directory B2B collaboration users in the Azure portal](../active-directory/b2b/add-users-administrator.md). | Requires creation of an Azure AD group with guest users as members to set up this group as an Azure AD admin for MI. For more information, see [Azure AD business to business support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

As a best practice for existing Azure AD admins for MI created before GA, and still operating post GA, reset the Azure AD admin using the Azure portal “Remove admin” and “Set admin” option for the same Azure AD user or group.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Known issues with the Azure AD login GA for MI

- If an Azure AD login exists in the master database for MI, created using the T-SQL command `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, it can't be set up as an Azure AD admin for MI. You'll experience an error setting the login as an Azure AD admin using the Azure portal, PowerShell, or CLI commands to create the Azure AD login.
  - The login must be dropped in the master database using the command `DROP LOGIN [myaadaccount]`, before the account can be created as an Azure AD admin.
  - Set up the Azure AD admin account in the Azure portal after the `DROP LOGIN` succeeds. 
  - If you can't set up the Azure AD admin account, check in the master database of the managed instance for the login. Use the following command: `SELECT * FROM sys.server_principals`
  - Setting up an Azure AD admin for MI will automatically create a login in the master database for this account. Removing the Azure AD admin will automatically drop the login from the master database.

- Individual Azure AD guest users are not supported as Azure AD admins for MI. Guest users must be part of an Azure AD group to be set up as Azure AD admin. Currently, the Azure portal blade doesn't gray out guest users for another Azure AD, allowing users to continue with the admin setup. Saving guest users as an Azure AD admin will cause the setup to fail.
  - If you wish to make a guest user an Azure AD admin for MI, include the guest user in an Azure AD group, and set this group as an Azure AD admin.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell for SQL managed instance

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

To run PowerShell cmdlets, you need to have Azure PowerShell installed and running. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

To provision an Azure AD admin, execute the following Azure PowerShell commands:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets used to provision and manage Azure AD admin for SQL managed instance:

| Cmdlet name | Beskrivning |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisions an Azure AD administrator for SQL managed instance in the current subscription. (Must be from the current subscription)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Removes an Azure AD administrator for SQL managed instance in the current subscription. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Returns information about an Azure AD administrator for SQL managed instance in the current subscription.|

The following command gets information about an Azure AD administrator for a managed instance named ManagedInstance01 that is associated with a resource group named ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

The following command provisions an Azure AD administrator group named DBAs for the managed instance named ManagedInstance01. This server is associated with resource group ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

The following command removes the Azure AD administrator for the managed instance named ManagedInstanceName01 associated with the resource group ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

You can also provision an Azure AD admin for SQL managed instance by calling the following CLI commands:

| Kommando | Beskrivning |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Provisions an Azure Active Directory administrator for SQL managed instance. (Must be from the current subscription) |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Removes an Azure Active Directory administrator for SQL managed instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Returns information about an Azure Active Directory administrator currently configured for SQL managed instance. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Updates the Active Directory administrator for a SQL managed instance. |

For more information about CLI commands, see [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Etablera en Azure Active Directory-administratör för din Azure SQL Database-server

> [!IMPORTANT]
> Only follow these steps if you are provisioning an Azure SQL Database server or Data Warehouse.

The following two procedures show you how to provision an Azure Active Directory administrator for your Azure SQL server in the Azure portal and by using PowerShell.

### <a name="azure-portal"></a>Azure portal

1. På [Azure-portalen](https://portal.azure.com/) väljer du din anslutning i det övre högra hörnet för att visa en lista över möjliga Active Directories. Välj rätt Active Directory som standard-Azure AD. Det här steget länkar prenumerationsassocierad Active Directory till Azure SQL-servern, vilket gör att samma prenumeration används för både Azure AD och SQL Server. (The Azure SQL server can be hosting either Azure SQL Database or Azure SQL Data Warehouse.) ![choose-ad][8]

2. In the left banner select **All services**, and in the filter type in **SQL server**. Select **Sql Servers**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > On this page, before you select **SQL servers**, you can select the **star** next to the name to *favorite* the category and add **SQL servers** to the left navigation bar.

3. On **SQL Server** page, select **Active Directory admin**.

4. In the **Active Directory admin** page, select **Set admin**.

    ![Välj active directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. på sidan **Lägg till administratör** söker du efter en användare, väljer den användare eller den grupp som ska vara administratör och väljer sedan **Välj**. (Active Directory-administratörssidan visar alla medlemmar och grupper för din Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. (See the list of supported admins in the **Azure AD Features and Limitations** section of [Use Azure Active Directory Authentication for authentication with SQL Database or SQL Data Warehouse](sql-database-aad-authentication.md).) Role-based access control (RBAC) applies only to the portal and is not propagated to SQL Server.

    ![välja administratör](./media/sql-database-aad-authentication/select-admin.png)  

6. Längst upp på sidan **Active Directory-administratör** väljer du **SPARA**.

    ![save admin](./media/sql-database-aad-authentication/save-admin.png)

Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

   > [!NOTE]
   > När Azure AD-administratören konfigureras får namnet för den nya administratören (användare eller grupp) inte redan finnas i den virtuella huvuddatabasen som SQL Server-autentiseringsanvändare. Om det finns misslyckas Azure AD-administratörskonfigurationen. Den återställer skapande och anger att den administratören (namnet) redan finns. Eftersom en sådan SQL Server-autentiseringsanvändare inte är en del av Azure AD misslyckas alla försök att ansluta till servern med hjälp av Azure AD-autentisering.

To later remove an Admin, at the top of the **Active Directory admin** page, select **Remove admin**, and then select **Save**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell for Azure SQL Database and Azure SQL Data Warehouse

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

To run PowerShell cmdlets, you need to have Azure PowerShell installed and running. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). To provision an Azure AD admin, execute the following Azure PowerShell commands:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets used to provision and manage Azure AD admin for Azure SQL Database and Azure SQL Data Warehouse:

| Cmdlet name | Beskrivning |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisions an Azure Active Directory administrator for Azure SQL server or Azure SQL Data Warehouse. (Must be from the current subscription) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Removes an Azure Active Directory administrator for Azure SQL server or Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Returns information about an Azure Active Directory administrator currently configured for the Azure SQL server or Azure SQL Data Warehouse. |

Use PowerShell command get-help to see more information for each of these commands. Till exempel `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

The following script provisions an Azure AD administrator group named **DBA_Group** (object ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) for the **demo_server** server in a resource group named **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

The **DisplayName** input parameter accepts either the Azure AD display name or the User Principal Name. For example, ``DisplayName="John Smith"`` and ``DisplayName="johns@contoso.com"``. For Azure AD groups only the Azure AD display name is supported.

> [!NOTE]
> The Azure PowerShell  command ```Set-AzSqlServerActiveDirectoryAdministrator``` does not prevent you from provisioning Azure AD admins for unsupported users. An unsupported user can be provisioned, but can not connect to a database.

The following example uses the optional **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> The Azure AD **ObjectID** is required when the **DisplayName** is not unique. To retrieve the **ObjectID** and **DisplayName** values, use the Active Directory section of Azure Classic Portal, and view the properties of a user or group.

The following example returns information about the current Azure AD admin for Azure SQL server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

The following example removes an Azure AD administrator:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

You can provision an Azure AD admin by calling the following CLI commands:

| Kommando | Beskrivning |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Provisions an Azure Active Directory administrator for Azure SQL server or Azure SQL Data Warehouse. (Must be from the current subscription) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Removes an Azure Active Directory administrator for Azure SQL server or Azure SQL Data Warehouse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Returns information about an Azure Active Directory administrator currently configured for the Azure SQL server or Azure SQL Data Warehouse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Updates the Active Directory administrator for an Azure SQL server or Azure SQL Data Warehouse. |

For more information about CLI commands, see [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> You can also provision an Azure Active Directory Administrator by using the REST APIs. For more information, see [Service Management REST API Reference and Operations for Azure SQL Database Operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Configure your client computers

On all client machines, from which your applications or users connect to Azure SQL Database or Azure SQL Data Warehouse using Azure AD identities, you must install the following software:

- .NET Framework 4.6 or later from [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library for SQL Server (*ADALSQL.DLL*) is available in multiple languages (both x86 and amd64) from the download center at [Microsoft Active Directory Authentication Library for Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

You can meet these requirements by:

- Installing either [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) or [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) meets the .NET Framework 4.6 requirement.
- SSMS installs the x86 version of *ADALSQL.DLL*.
- SSDT installs the amd64 version of *ADALSQL.DLL*.
- The latest Visual Studio from [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) meets the .NET Framework 4.6 requirement, but does not install the required amd64 version of *ADALSQL.DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Create contained database users in your database mapped to Azure AD identities

> [!IMPORTANT]
> Managed instance now supports Azure AD server principals (logins), which enables you to create logins from Azure AD users, groups, or applications. Azure AD server principals (logins) provides the ability to authenticate to your managed instance without requiring database users to be created as a contained database user. For more information, see [managed instance Overview](sql-database-managed-instance.md#azure-active-directory-integration). For syntax on creating Azure AD server principals (logins), see <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

Azure Active Directory-autentisering kräver att databasanvändare skapas som användare av oberoende databas. En användare av oberoende databas baserad på en Azure AD-identitet är en databasanvändare som inte har någon inloggning i huvuddatabasen, och som mappas till en identitet i den Azure AD-katalog som är associerad med databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information om användare av oberoende databas finns i avsnittet om [användare av oberoende databas – så gör du din databas portabel](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Databasanvändare (med undantag för administratörer) kan inte skapas med hjälp av Azure-portalen. RBAC-roller sprids inte till SQL Server, SQL Database eller SQL Data Warehouse. Azure RBAC-roller används för att hantera Azure-resurser och gäller inte för databasbehörigheter. Till exempel beviljar rollen **SQL Server-deltagare** inte åtkomst för att ansluta till SQL Database eller SQL Data Warehouse. Åtkomstbehörigheten måste beviljas direkt i databasen med hjälp av Transact-SQL-instruktioner.

> [!WARNING]
> Specialtecken som kolon `:` eller et-tecken `&` stöds inte när de ingår i användarnamn i instruktionerna T-SQL CREATE LOGIN och CREATE USER.

To create an Azure AD-based contained database user (other than the server administrator that owns the database), connect to the database with an Azure AD identity, as a user with at least the **ALTER ANY USER** permission. Then use the following Transact-SQL syntax:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* can be the user principal name of an Azure AD user or the display name for an Azure AD group.

**Examples:** To create a contained database user representing an Azure AD federated or managed domain user:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

To create a contained database user representing an Azure AD or federated domain group, provide the display name of a security group:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

To create a contained database user representing an application that connects using an Azure AD token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> This command requires that SQL access Azure AD (the "external provider") on behalf of the logged-in user. Sometimes, circumstances will arise that cause Azure AD to return an exception back to SQL. In these cases, the user will see SQL error 33134, which should contain the AAD-specific error message. Most of the time, the error will say that access is denied, or that the user must enroll in MFA to access the resource, or that access between first-party applications must be handled via preauthorization. In the first two cases, the issue is usually caused by Conditional Access policies that are set in the user's AAD tenant: they prevent the user from accessing the external provider. Updating the CA policies to allow access to the application '00000002-0000-0000-c000-000000000000' (the application ID of the AAD Graph API) should resolve the issue. In the case that the error says access between first-party applications must be handled via preauthorization, the issue is because the user is signed in as a service principal. The command should succeed if it is executed by a user instead.

> [!TIP]
> You cannot directly create a user from an Azure Active Directory other than the Azure Active Directory that is associated with your Azure subscription. However, members of other Active Directories that are imported users in the associated Active Directory (known as external users) can be added to an Active Directory group in the tenant Active Directory. By creating a contained database user for that AD group, the users from the external Active Directory can gain access to SQL Database.

For more information about creating contained database users based on Azure Active Directory identities, see [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Removing the Azure Active Directory administrator for Azure SQL server prevents any Azure AD authentication user from connecting to the server. If necessary, unusable Azure AD users can be dropped manually by a SQL Database administrator.

> [!NOTE]
> If you receive a **Connection Timeout Expired**, you may need to set the `TransparentNetworkIPResolution` parameter of the connection string to false. For more information, see [Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

When you create a database user, that user receives the **CONNECT** permission and can connect to that database as a member of the **PUBLIC** role. Initially the only permissions available to the user are any permissions granted to the **PUBLIC** role, or any permissions granted to any Azure AD groups that they are a member of. Once you provision an Azure AD-based contained database user, you can grant the user additional permissions, the same way as you grant permission to any other type of user. Typically grant permissions to database roles, and add users to roles. For more information, see [Database Engine Permission Basics](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). For more information about special SQL Database roles, see [Managing Databases and Logins in Azure SQL Database](sql-database-manage-logins.md).
A federated domain user account that is imported into a managed domain as an external user, must use the managed domain identity.

> [!NOTE]
> Azure AD-användare markeras i databasmetadata med typen E (EXTERNAL_USER) och för grupper med typen X (EXTERNAL_GROUPS). Mer information finns i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Connect to the user database or data warehouse by using SSMS or SSDT  

To confirm the Azure AD administrator is properly set up, connect to the **master** database using the Azure AD administrator account.
To provision an Azure AD-based contained database user (other than the server administrator that owns the database), connect to the database with an Azure AD identity that has access to the database.

> [!IMPORTANT]
> Support for Azure Active Directory authentication is available with [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) and [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. The August 2016 release of SSMS also includes support for Active Directory Universal Authentication, which allows administrators to require Multi-Factor Authentication using a phone call, text message, smart cards with pin, or mobile app notification.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Using an Azure AD identity to connect using SSMS or SSDT

The following procedures show you how to connect to a SQL database with an Azure AD identity using SQL Server Management Studio or SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrated authentication

Use this method if you are logged in to Windows using your Azure Active Directory credentials from a federated domain.

1. Start Management Studio or Data Tools and in the **Connect to Server** (or **Connect to Database Engine**) dialog box, in the **Authentication** box, select **Active Directory - Integrated**. No password is needed or can be entered because your existing credentials will be presented for the connection.

    ![Select AD Integrated Authentication][11]

2. Select the **Options** button, and on the **Connection Properties** page, in the **Connect to database** box, type the name of the user database you want to connect to. (The **AD domain name or tenant ID**” option is only supported for **Universal with MFA connection** options, otherwise it is greyed out.)  

    ![Select the database name][13]

## <a name="active-directory-password-authentication"></a>Active Directory password authentication

Use this method when connecting with an Azure AD principal name using the Azure AD managed domain. You can also use it for federated accounts without access to the domain, for example when working remotely.

Use this method to authenticate to SQL DB/DW with Azure AD for native or federated Azure AD users. A native user is one explicitly created in Azure AD and being authenticated using user name and password, while a federated user is a Windows user whose domain is federated with Azure AD. The latter method (using user & password) can be used when a user wants to use their windows credential, but their local machine is not joined with the domain (for example, using a remote access). In this case, a Windows user can indicate their domain account and password and can authenticate to SQL DB/DW using federated credentials.

1. Start Management Studio or Data Tools and in the **Connect to Server** (or **Connect to Database Engine**) dialog box, in the **Authentication** box, select **Active Directory - Password**.

2. In the **User name** box, type your Azure Active Directory user name in the format **username\@domain.com**. User names must be an account from the Azure Active Directory or an account from a domain federate with the Azure Active Directory.

3. In the **Password** box, type your user password for the Azure Active Directory account or federated domain account.

    ![Select AD Password Authentication][12]

4. Select the **Options** button, and on the **Connection Properties** page, in the **Connect to database** box, type the name of the user database you want to connect to. (See the graphic in the previous option.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Using an Azure AD identity to connect from a client application

The following procedures show you how to connect to a SQL database with an Azure AD identity from a client application.

### <a name="active-directory-integrated-authentication"></a>Active Directory integrated authentication

To use integrated Windows authentication, your domain’s Active Directory must be federated with Azure Active Directory. Your client application (or a service) connecting to the database must be running on a domain-joined machine under a user’s domain credentials.

To connect to a database using integrated authentication and an Azure AD identity, the Authentication keyword in the database connection string must be set to Active Directory Integrated. The following C# code sample uses ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

The connection string keyword `Integrated Security=True` is not supported for connecting to Azure SQL Database. When making an ODBC connection, you will need to remove spaces and set Authentication to 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Active Directory password authentication

To connect to a database using integrated authentication and an Azure AD identity, the Authentication keyword must be set to Active Directory Password. The connection string must contain User ID/UID and Password/PWD keywords and values. The following C# code sample uses ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Learn more about Azure AD authentication methods using the demo code samples available at [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD token

This authentication method allows middle-tier services to connect to Azure SQL Database or Azure SQL Data Warehouse by obtaining a token from Azure Active Directory (AAD). It enables sophisticated scenarios including certificate-based authentication. You must complete four basic steps to use Azure AD token authentication:

1. Register your application with Azure Active Directory and get the client ID for your code.
2. Create a database user representing the application. (Completed earlier in step 6.)
3. Create a certificate on the client computer runs the application.
4. Add the certificate as a key for your application.

Sample connection string:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

For more information, see [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). For information about adding a certificate, see [Get started with certificate-based authentication in Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

The following statements, connect using version 13.1 of sqlcmd, which is available from the [Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` with the `-G` command does not work with system identities, and requires a user principal login.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Nästa steg

- En översikt över åtkomst och kontroll i SQL Database finns i [Åtkomst och kontroll för SQL Database](sql-database-control-access.md).
- En översikt över inloggningar, användare och databasroller i SQL Database finns i [Inloggningar, användare och databasroller](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
