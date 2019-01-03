---
title: Ansluta till Azure SQL Data Warehouse sqlcmd | Microsoft Docs
description: Du kan använda kommandoradsverktyget SQLCMD för att ansluta till och fråga en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 2534d668132db4c66b5e54c33849b76a8f93c348
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976417"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Anslut till SQL Data Warehouse med sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Använd kommandoradsverktyget [sqlcmd][sqlcmd] för att ansluta till och hämta information från ett Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Anslut
Du kommer igång med [sqlcmd][sqlcmd] genom att öppna kommandotolken och ange **sqlcmd** följt av anslutningssträngen för din SQL Data Warehouse-databas. Anslutningssträngen kräver följande parametrar:

* **Server (-S):** Server i formatet `<`servernamn`>`. database.windows.net
* **Databas (-d):** Databasnamnet.
* **Aktivera identifierare (– jag):** Identifierare måste aktiveras för att ansluta till en instans av SQL Data Warehouse.

Om du vill använda SQL Server-autentisering måste du lägga till användarnamn/lösenordsparametrar:

* **Användare (-U):** Serveranvändare i formatet `<`användare`>`
* **Lösenord (-P):** Lösenordet som är associerat med användaren.

Anslutningssträngen kan se ut ungefär så här:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Om du vill använda Azure Active Directory-integrerad autentisering måste du lägga till Azure Active Directory-parametrar:

* **Azure Active Directory-autentisering (-G):** använder Azure Active Directory för autentisering

Anslutningssträngen kan se ut ungefär så här:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Du måste [aktivera Azure Active Directory-autentisering](sql-data-warehouse-authentication.md) för att kunna autentisera med Active Directory.
> 
> 

## <a name="2-query"></a>2. Söka i data
Du kan utfärda alla Transact-SQL-uttryck som stöds mot instansen efter anslutning.  I det här exemplet skickas frågor i interaktivt läge.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Dessa nästa exempel visar hur du kan köra frågor i batch-läge med alternativet -Q eller skicka din SQL till sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Nästa steg
Se [sqlcmd-dokumentationen][sqlcmd] för mer information om tillgängliga alternativ i sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
