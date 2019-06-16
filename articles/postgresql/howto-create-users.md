---
title: Skapa användare i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ce6188732720bc43c5849fa492237c7ab98487c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067504"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Skapa användare i Azure Database för PostgreSQL – enskild Server
Den här artikeln beskrivs hur du kan skapa användare i en Azure Database for PostgreSQL-server.

## <a name="the-server-admin-account"></a>Serveradministratörskontot
När du först skapade Azure Database för PostgreSQL angav du ett server-administratörens användarnamn och lösenord. Mer information kan du följa den [snabbstarten](quickstart-create-server-database-portal.md) att se den stegvisa metoden. Eftersom ett administratörsanvändarnamn för server är ett anpassat namn kan hitta du ett administratörsanvändarnamn för valda servern från Azure-portalen.

Azure Database for PostgreSQL-server skapas med 3 standardrollerna definierats. Du kan se dessa roller genom att köra kommandot: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- din serveradministratörsanvändaren

Din serveradministratörsanvändaren är medlem i rollen azure_pg_admin. Serveradministratörskontot är dock inte en del av rollen azure_superuser. Eftersom den här tjänsten är en hanterad PaaS-tjänst kan är endast Microsoft en del av rollen superanvändare. 

PostgreSQL-motorn använder behörighet för att styra åtkomsten till databasobjekt, enligt beskrivningen i den [PostgreSQL produktdokumentationen](https://www.postgresql.org/docs/current/static/sql-createrole.html). Azure Database för PostgreSQL beviljas server administratörsanvändare dessa privilegier: INLOGGNINGEN MISSLYCKADES, NOSUPERUSER, ÄRVER, CREATEDB, CREATEROLE, NOREPLICATION

Användarkontot för server-administratör kan användas för att skapa ytterligare användare och tilldela dessa användare i rollen azure_pg_admin. Serveradministratörskontot kan också användas för att skapa mindre privilegierade användare och roller som har åtkomst till enskilda databaser och scheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Så här skapar du ytterligare administrativa användare i Azure Database för PostgreSQL
1. Hämta anslutning administration och information användarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du enkelt servernamnet och inloggningsuppgifter från servern **översikt** sidan eller **egenskaper** sidan på Azure portal. 

2. Använda administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel pgAdmin eller psql.
   Om du är osäker på hur du ansluter kan du läsa [snabbstarten](./quickstart-create-server-database-portal.md)

3. Redigera och kör följande SQL-kod. Ersätt ditt nya användarnamn för värdet för platshållaren < new_user > och ersätter du platshållaren lösenordet med din egen starkt lösenord. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Skapa databasanvändare i Azure Database för PostgreSQL

1. Hämta anslutning administration och information användarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du enkelt servernamnet och inloggningsuppgifter från servern **översikt** sidan eller **egenskaper** sidan på Azure portal. 

2. Använda administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel pgAdmin eller psql.

3. Redigera och kör följande SQL-kod. Ersätt platshållarvärdet `<db_user>` med avsedda nytt användarnamn och platshållarvärdet `<newdb>` med databasnamn på din. Ersätt platshållaren lösenordet med din egen starkt lösenord. 

   Den här sql-syntax i koden skapar en ny databas med namnet testdb, exempelsyfte. Skapar sedan en ny användare i PostgreSQL-tjänsten och ger Anslut behörigheter till den nya databasen för den användaren. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Med ett administratörskonto kan behöva du ge ytterligare behörigheter för att skydda objekten i databasen. Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/current/static/ddl-priv.html) finns mer information om databasroller och behörigheter. Exempel: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Logga in på din server, anger den avsedda databas med nytt användarnamn och lösenord. Det här exemplet visar psql-kommandoraden. Med det här kommandot uppmanas du lösenordet för användarnamnet. Ersätt dina egna servernamnet, databasnamnet och användarnamn.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Nästa steg
Öppna brandväggen för IP-adresserna för de nya användarna datorer så att de kan ansluta: [Skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure portal](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användarkonton, finns i dokumentationen för PostgreSQL [databasroller och privilegier](https://www.postgresql.org/docs/current/static/user-manag.html), [BEVILJA Syntax](https://www.postgresql.org/docs/current/static/sql-grant.html), och [privilegier](https://www.postgresql.org/docs/current/static/ddl-priv.html).
