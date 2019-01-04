---
title: Skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure CLI
description: Den här artikeln beskriver hur du skapar och hanterar en Azure Database för MySQL-brandväggsregler med hjälp av Azure CLI-kommandoraden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: e4aabaf2673f6211523653f9d0a0ecf1769f83a3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53549011"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan administratörer hantera åtkomst till en Azure Database for MySQL-Server från en specifik IP-adress eller ett intervall med IP-adresser. Med praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista, och visa brandväggsregler för att hantera servern. En översikt över Azure Database för MySQL-brandväggar, se [Azure Database for MySQL-serverbrandväggsregler](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Förutsättningar
* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* En [Azure Database för MySQL-servern och databasen](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Brandväggen regeln kommandon:
Den **az mysql server firewall-rule** används med Azure CLI för att skapa, ta bort, lista, visa och uppdatera brandväggsregler.

Kommandon:
- **Skapa**: Skapa en brandväggsregel för Azure MySQL-server.
- **Ta bort**: Ta bort en brandväggsregel för Azure MySQL-server.
- **Lista**: Lista över brandväggsregler för Azure MySQL-servern.
- **Visa**: Visa information om en Azure MySQL-server brandväggsregel.
- **Uppdatera**: Uppdatera en brandväggsregel för Azure MySQL-server.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Logga in på Azure och lista din Azure Database för MySQL-servrar
På ett säkert sätt ansluta Azure CLI med Azure-kontot med hjälp av den **az-inloggning** kommando.

1. Kör följande kommando på kommandoraden:
    ```azurecli
    az login
    ```
Detta kommando visar en kod som ska användas i nästa steg.

2. Använd en webbläsare för att öppna sidan [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin), och ange koden.

3. Logga in med dina autentiseringsuppgifter för Azure när du ombeds göra det.

4. När din inloggning har behörighet, skrivs en lista över prenumerationer i konsolen. Kopiera ID för den önskade prenumerationen för att ställa in den aktuella prenumerationen du använder. Använd den [az-kontogrupper](/cli/azure/account#az-account-set) kommando.
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Lista över Azure-databaser för MySQL-servrar för din prenumeration och resursgrupp grupp om du är osäker på namnen. Använd den [az mysql server list](/cli/azure/mysql/server#az-mysql-server-list) kommando.

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Observera det attributet i en lista som du måste ange MySQL-server för att arbeta med. Om det behövs, bekräfta informationen för den här servern och med attributet för att kontrollera att den är korrekt. Använd den [az mysql server show](/cli/azure/mysql/server#az-mysql-server-show) kommando.

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista brandväggsregler på Azure Database for MySQL-Server 
Med hjälp av namnet på servern och resursgruppens namn, en lista över de befintliga brandväggsreglerna för server på servern. Använd den [az mysql server firewall lista](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) kommando.  Lägg märke till att attributet server name har angetts i den **--server** växla och inte i den **--name** växla. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar regler, om sådant finns, i JSON-format (som standard). Du kan använda den **--utdatatabellen** växla till matar ut resultaten i ett mer lättläst tabellformat.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Skapa en brandväggsregel på Azure Database for MySQL-Server
Med hjälp av Azure MySQL-servernamn och resursgruppens namn, skapa en ny brandväggsregel på servern. Använd den [az mysql-serverbrandväggen skapa](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) kommando. Ange ett namn för regeln, samt start-IP och slut-IP (om du vill ge åtkomst till ett intervall med IP-adresser) för regeln.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Ange samma IP-adress som den första IP- och slut-IP, som i följande exempel för att tillåta åtkomst för en IP-adress.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ange IP-adressen 0.0.0.0 som den första IP- och slut-IP, som i följande exempel för att tillåta program från Azure-IP-adresser för att ansluta till din Azure Database for MySQL-server.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

Vid en lyckad distribution skapa varje kommando utdata visar information om brandväggsregeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel, visar utdata felmeddelandetext i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Uppdatera en brandväggsregel på Azure Database for MySQL-server 
Med hjälp av Azure MySQL-servernamn och resursgruppens namn, uppdatera en befintlig brandväggsregel på servern. Använd den [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) kommando. Ange namnet på befintlig brandväggsregeln som indata, samt början IP- och IP-attribut som ska uppdateras.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Vid en lyckad distribution visas utdata från kommandot information om brandväggsregeln som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel, visar utdata felmeddelandetext i stället.

> [!NOTE]
> Om brandväggsregeln inte finns, skapas regeln genom att kommandot update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Visa brandväggen Regelinformation på Azure Database for MySQL-Server
Använda Azure MySQL-servernamn och resursgruppens namn kan visa befintliga brandväggen Regelinformation från servern. Använd den [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) kommando. Ange namnet på befintlig brandväggsregeln som indata.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Vid en lyckad distribution visas utdata från kommandot information om brandväggsregeln som du har angett i JSON-format (som standard). Om det uppstår ett fel, visar utdata felmeddelandetext i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Ta bort en brandväggsregel på Azure Database for MySQL-Server
Med hjälp av Azure MySQL-servernamn och resursgruppens namn, ta bort en befintlig brandväggsregel från servern. Använd den [az mysql-serverbrandväggen ta bort](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) kommando. Ange namnet på befintlig brandväggsregeln.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Vid en lyckad distribution finns det inga utdata. Vid fel visar felmeddelandetext.

## <a name="next-steps"></a>Nästa steg
- Läser mer om [Azure Database for MySQL-serverbrandväggsregler](./concepts-firewall-rules.md).
- [Skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure-portalen](./howto-manage-firewall-using-portal.md).
