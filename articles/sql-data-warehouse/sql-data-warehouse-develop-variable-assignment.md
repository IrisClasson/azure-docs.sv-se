---
title: Tilldela variabler i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 62c4273a02e02aff268a96e1b13483088ba33f87
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861685"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Tilldela variabler i Azure SQL Data Warehouse

Tips för att tilldela T-SQL-variabler i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="setting-variables-with-declare"></a>Ange variabler med DECLARE

Variabler i SQL Data Warehouse är inställda med hjälp av den `DECLARE` instruktionen eller `SET` instruktionen. Initiera variabler med DECLARE är ett av de mest flexibla sätten att ange ett variabelvärde i SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Du kan också använda DECLARE för att ange fler än en variabel i taget. Du kan inte använda SELECT- eller UPDATE för att göra följande:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Du kan inte initiera och använder en variabel i samma DECLARE-instruktion. För att illustrera punkten i följande exempel är **inte** tillåts eftersom @p1 är både initierats och användas i samma DECLARE-instruktion. I följande exempel ger ett fel.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ange värden med SET

Är en vanlig metod för att ange en variabel.

Följande instruktioner är alla giltiga sätt att ange en variabel med:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Du kan bara ange en variabel i taget med. Dock är sammansatt operatorer tillåtna.

## <a name="limitations"></a>Begränsningar

Du kan inte använda UPPDATERINGEN för variabeltilldelning.

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).
