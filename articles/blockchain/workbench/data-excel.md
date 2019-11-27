---
title: Använda Azure Blockchain Workbench-data i Microsoft Excel
description: Lär dig hur du läser in och visar Azure blockchain Workbench Preview SQL DB-data i Microsoft Excel.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: a84858ead83782cc9b6ef1b1d7f905172600fb8a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326051"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Visa Azure Blockchain Workbench-data med Microsoft Excel

Du kan använda Microsoft Excel för att visa data i SQL DB i Azure Blockchain Workbench. Den här artikeln förklarar de steg du följer för att:

* Ansluta till Blockchain Workbench-databasen från Microsoft Excel
* Visa tabeller och vyer i Blockchain Workbench-databasen
* Läsa in Blockchain Workbench-vydata till Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Ansluta till Blockchain Workbench-databasen

Så här ansluter du till en Blockchain Workbench-databas:

1. Öppna Microsoft Excel.
2. Välj **Hämta data** på fliken **Data**.
3. Välj **Från Azure** och sedan **Från Azure SQL Database**.

   ![Ansluta till Azure SQL-databas](./media/data-excel/connect-sql-db.png)

4. I dialogrutan **SQL Server-databas**:

    * För **Server** anger du namnet på Blockchain Workbench-servern.
    * För **Databas (valfritt)** anger du namnet på databasen.

   ![Ange databasserver och databas](./media/data-excel/provide-server-db.png)

5. Välj **Databas** i navigeringsfältet i dialogrutan **SQL Server-databas**. Ange ditt **användarnamn** och **lösenord** och välj sedan **Anslut**.

    > [!NOTE]
    > Om du använder autentiseringsuppgifterna som skapades i samband med Azure Blockchain Workbench-distributionen är **Användarnamn** `dbadmin`. **Lösenord** är det lösenord som du skapade när du distribuerade Blockchain Workbench.
    
   ![Ange autentiseringsuppgifterna för åtkomst till databasen](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Visa tabeller och vyer i databasen

Dialogrutan Excel Navigator (Excel-navigatör) öppnas när du har anslutit till databasen. Du kan använda navigatören för att titta närmare på tabellerna och vyerna i databasen. Vyerna är utformade för rapportering och deras namn har prefixet **vw**.

   ![Förhandsgranskning av en vy i Excel-navigatören](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Läsa in vydata till en Excel-arbetsbok

I nästa exempel ser du hur du kan läsa in data från en vy till en Excel-arbetsbok.

1. Välj vyn **vwContractAction** i rullningslisten i **Navigatör**. Förhandsgranskningen av **vwContractAction** visar alla åtgärder som rör ett kontrakt i Blockchain Workbench-databasen.
2. Välj **Läs in** för att hämta alla data i vyn och infoga dem i Excel-arbetsboken.

   ![Data läses in från en vy](./media/data-excel/view-data.png)

Nu när alla data har lästs in kan du använda Excel-funktioner för att skapa egna rapporter med hjälp av metadata och transaktionsdata från Azure Blockchain Workbench-databasen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Databasvyer i Azure Blockchain Workbench](database-views.md)