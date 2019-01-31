---
title: PowerShell-cmdletar för Azure SQL Data Warehouse
description: Hitta de översta PowerShell-cmdletarna för Azure SQL Data Warehouse, inklusive hur du pausar och återupptar en databas.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 412c2faead0102d3995e3df9db31b81c4dd4aa22
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452013"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-cmdletar och REST API: er för SQL Data Warehouse
Många administrationsuppgifter för SQL Data Warehouse kan hanteras med hjälp av Azure PowerShell-cmdletar eller REST API: er.  Nedan följer några exempel på hur du använder PowerShell-kommandon för att automatisera vanliga uppgifter i din SQL Data Warehouse.  Några bra REST-exempel finns i artikeln [hantera skalbarhet med REST][Manage scalability with REST].

> [!NOTE]
> För att kunna använda Azure PowerShell med SQL Data Warehouse, du behöver ha Azure PowerShell version 1.0.3 eller senare.  Du kan kontrollera din version genom att köra **Get-Module - ListAvailable-Name Azure**.  Den senaste versionen kan installeras från [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Mer information om hur du installerar den senaste versionen finns i [Installera och konfigurera Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Kom igång med Azure PowerShell-cmdlets
1. Öppna Windows PowerShell.
2. I PowerShell-Kommandotolken kör du följande kommandon för att logga in till Azure Resource Manager och välj din prenumeration.
   
    ```PowerShell
    Connect-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Pausa SQL Data Warehouse-exempel
Pausa en databas med namnet ”Database02” finns på en server med namnet ”Server01”.  Servern är i ett Azure-resursgrupp med namnet ”ResourceGroup1”.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
En variant det här exemplet kommer det hämtade objektet som ska [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  Därför kan har databasen pausats. Det slutliga kommandot visas resultatet.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Starta SQL Data Warehouse-exempel
Återuppta drift av en databas med namnet ”Database02” finns på en server med namnet ”Server01”. Servern finns i en resursgrupp med namnet ”ResourceGroup1”.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

En variant det här exemplet hämtar en databas med namnet ”Database02” från en server med namnet ”Server01” som finns i en resursgrupp med namnet ”ResourceGroup1”. Det rör objektet hämtades till [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Observera att om din server är foo.database.windows.net, använder du ”foo” som - servernamn i PowerShell-cmdlets.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andra stöd för PowerShell-cmdletar
Dessa PowerShell-cmdletar stöds med Azure SQL Data Warehouse.

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>Nästa steg
Mer PowerShell-exempel finns:

* [Skapa ett SQL Data Warehouse med hjälp av PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Återställning av databasen][Database restore]

Andra uppgifter som kan automatiseras med PowerShell, se [cmdlet: ar för Azure SQL Database][Azure SQL Database Cmdlets]. Observera att alla Azure SQL Database-cmdlets har stöd för Azure SQL Data Warehouse.  En lista över aktiviteter som kan automatiseras med REST finns i [åtgärder för Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase
[Get-AzureRmSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDeletedDatabaseBackup
[Get-AzureRmSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDatabaseRestorePoints
[New-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzureRmSqlDatabase
[Remove-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlDatabase
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzureRmSqlDatabase
[Resume-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzureRmSqlDatabase
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlDatabase
[Suspend-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzureRmSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
