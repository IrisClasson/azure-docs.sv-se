---
title: 'Snabbstart: Skala ut beräkning i Azure SQL Data Warehouse – Azure-portalen | Microsoft Docs'
description: Skala beräkning i Azure SQL Data Warehouse i Azure Portal. Skala ut beräkning för bättre prestanda eller skala ned beräkning om du vill sänka kostnaderna.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4197d7a4724199f8c42fc09a6d1ed18af46cc4f8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575356"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Snabbstart: Skala beräkning i Azure SQL Data Warehouse i Azure-portalen

Skala beräkning i Azure SQL Data Warehouse i Azure Portal. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för att få bättre prestanda eller skala ned beräkning om du vill sänka kostnaderna. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan skala ett informationslager som du redan har, eller använda [Snabbstart: Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.  I den här snabbstarten skalas **mySampleDataWarehouse**.

>[!Note]
>Ditt informationslager måste vara online för att kunna skalas. 

## <a name="scale-compute"></a>Skala beräkning

SQL Data Warehouses beräkningsresurser kan skalas genom att öka eller minska informationslagerenheterna. I snabbstarten [Skapa och ansluta – portal] (create-data-warehouse-portal.md) skapade du **mySampleDataWarehouse** och initierade det med 400 DWU:er. Följande steg justerar DWU för **mySampleDataWarehouse**.

Så här ändrar du informationslagerenheter:

1. Klicka på **SQL-informationslager** till vänster på Azure Portal.
2. Välj **mySampleDataWarehouse** på sidan **SQL-databaser**. Informationslagret öppnas.
3. Klicka på **Skala**.

    ![Klicka på Skala](media/quickstart-scale-compute-portal/click-scale.png)

2. På panelen Skala ändrar du DWU-inställningen genom att dra skjutreglaget åt vänster eller höger.

    ![Dra skjutreglaget](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Klicka på **Ja** för att bekräfta eller **Nej** för att avbryta.

    ![Klicka på Spara](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig att skala databearbetningen för informationslagret. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
>[Läs in data i en SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
