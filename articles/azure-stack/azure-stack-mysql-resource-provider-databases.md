---
title: Med hjälp av databaser som tillhandahålls av MySQL nätverkskort RP på AzureStack | Microsoft Docs
description: Hur du skapar och hanterar MySQL-databaser som etablerats med hjälp av MySQL-Resursprovider för nätverkskort
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 3063a3f3f199a591fa94c3a665ea9ecc5457e502
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247603"
---
# <a name="create-mysql-databases"></a>Skapa MySQL-databaser

Du kan skapa och hantera självbetjäning databaser i användarportalen. En Azure Stack-användare behöver en prenumeration med ett erbjudande som innehåller MySQL-databastjänst.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Testa distributionen genom att skapa en MySQL-databas

1. Logga in på användarportalen för Azure Stack.
2. Välj **+ skapa en resurs** > **Data + lagring** > **MySQL-databas** > **Lägg till**.
3. Under **skapa MySQL-databas**, ange namnet på databasen och konfigurera andra inställningar som krävs för din miljö.

    ![Skapa ett test-MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Under **Create Database**väljer **SKU**. Under **Välj en MySQL-SKU**, Välj SKU för din databas.

    ![Välj en MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >När värdservrar läggs till Azure Stack, är de tilldelade en SKU. Databaser skapas i poolen med som är värd för servrar i en SKU.

5. Under **inloggning**väljer ***konfigurera nödvändiga inställningar***.
6. Under **väljer en inloggning**, du kan välja en befintlig inloggning eller välja **+ skapa en ny inloggning** att ställa in en ny inloggning.  Ange en **databasinloggning** namn och **lösenord**, och välj sedan **OK**.

    ![Skapa en ny databasinloggning](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Längden på inloggningen Databasnamnet får inte överstiga 32 tecken i MySQL 5.7. Det får vara högst 16 tecken i tidigare versioner.

7. Välj **skapa** att slutföra konfigurationen av databasen.

När databasen har distribuerats kan du ta del av den **Connection String** under **Essentials**. Du kan använda den här strängen i alla program som behöver åtkomst till MySQL-databasen.

![Hämta anslutningssträngen för MySQL-databas](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Uppdatera lösenordet för administratörer

Du kan ändra lösenordet genom att ändra MySQL-serverinstansen.

1. Välj **administrativa resurser** > **MySQL som är värd för servrar**. Välj värdservern.
2. Under **inställningar**väljer **lösenord**.
3. Under **lösenord**, ange det nya lösenordet och välj sedan **spara**.

![Uppdatera administratörslösenordet](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Nästa steg

[Uppdatera MySQL-resursprovidern](azure-stack-mysql-resource-provider-update.md)
