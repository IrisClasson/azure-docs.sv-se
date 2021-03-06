---
title: Infrastruktur dubbel kryptering – Azure Portal – Azure Database for PostgreSQL
description: Lär dig hur du konfigurerar och hanterar infrastruktur Double Encryption för din Azure Database for PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 6612fe38adcd3c8002dd4a11122b5bb2e797a4dd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102182"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Infrastruktur dubbel kryptering för Azure Database for PostgreSQL

Lär dig hur du använder den här inställningen för att konfigurera och hantera infrastruktur dubbel kryptering för din Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Skapa en Azure Database for PostgreSQL-server med Double Infrastructure Encryption – Portal

Följ dessa steg om du vill skapa en Azure Database for MySQL-server med Double Infrastructure Encryption från Azure Portal:

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for PostgreSQL**. Du kan också ange PostgreSQL i sökrutan för att hitta tjänsten. Aktiverat distributions alternativet för **enskild server** .

   !["Azure Database for PostgreSQL" på menyn](./media/quickstart-create-database-portal/1-create-database.png)

3. Ange grundläggande information om servern. Välj **ytterligare inställningar** och aktivera kryss rutan **infrastruktur med dubbla kryptering** för att ange parametern.

    ![Azure Database for PostgreSQL val](./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png)

4. Välj **Granska + skapa** för att etablera servern.

    ![Azure Database for PostgreSQL Sammanfattning](./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png)

5. När servern har skapats kan du verifiera infrastrukturens dubbla kryptering genom att kontrol lera statusen i bladet **data krypterings** Server.

    ![Azure Database for MySQL validering](./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Skapa en Azure Database for PostgreSQL-server med dubbel kryptering med infrastruktur – CLI

Följ dessa steg om du vill skapa en Azure Database for MySQL-server med Double Infrastructure Encryption från CLI:

I det här exemplet skapas en resurs grupp med namnet `myresourcegroup` på `westus` platsen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
I följande exempel skapas en PostgreSQL 11-server i västra USA som heter `mydemoserver` i din resurs grupp `myresourcegroup` med Server Administratörs inloggning `myadmin` . Det här är **4:e generationens server för ** **generell användning** med 2 **virtuella kärnor**. Detta kommer också att aktivera infrastruktur dubbel kryptering för servern som skapats. Ersätt `<server_admin_password>` med ditt eget värde.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Nästa steg

Mer information om data kryptering finns i [Azure Database for PostgreSQL data infrastruktur Double Encryption](concepts-Infrastructure-double-encryption.md).

