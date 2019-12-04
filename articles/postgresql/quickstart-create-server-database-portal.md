---
title: 'Snabb start: skapa server-Azure Portal-Azure Database for PostgreSQL-enskild server'
description: Snabb starts guide för att skapa och hantera en Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal användar gränssnittet.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/25/2019
ms.openlocfilehash: fa4b9fb9be6ac4f541448abef1f676875a7ddcfc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774991"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Snabbstart: Skapa en Azure Database for PostgreSQL-server i Azure Portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database for PostgreSQL-server med hjälp av Azure Portal på ungefär fem minuter.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till [portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

En Azure Database for PostgreSQL-server skapas med en konfigurerad uppsättning [beräknings- och lagringsresurser](./concepts-pricing-tiers.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **Databaser** > **Azure-databas för PostgreSQL**.

    !["Azure Database for PostgreSQL" på menyn](./media/quickstart-create-database-portal/1-create-database.png)

3. Välj distributions alternativet **enskild server** .

   ![Välj Azure Database for PostgreSQL – distributions alternativ för enskild server](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Fyll i formuläret **grundläggande** med följande information:

    ![Skapa en server](./media/quickstart-create-database-portal/create-basics.png)

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    servernamn |*mydemoserver*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domännamnet *postgres.database.azure.com* läggs till i det servernamn du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla minst 3 och upp till 63 tecken.
    Data Källa | *Alternativet* | Välj *ingen* om du vill skapa en ny server från grunden. (Du väljer *Säkerhetskopiering* om du skapar en server från en geo-säkerhetskopia av en befintlig Azure Database for PostgreSQL-server).
    Administratörens användar namn |*myadmin*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggningsnamnet för administratören får inte vara **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest,** eller **public**. Det får inte börja med **pg_** .
    Lösenord |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till och med 9) och icke-alfanumeriska tecken (!, $, #, % osv.).
    Plats|Den region som är närmast dina användare| Den plats som är närmast dina användare.
    Version|Senaste huvudversion| Den senaste PostgreSQL-huvudversionen, om du inte har andra särskilda krav.
    Compute + Storage | **Generell användning**, **Gen 5**, **2 virtuella kärnor**, **5 GB**, **7 dagar**, **Geografiskt redundant** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server**. Välj sedan fliken **generell användning** . *gen 5*, *4 virtuella kärnor*, *100 GB*och *7 dagar* är standardvärden för **beräknings generering**, **vCore**, **lagring**och **bevarande period för säkerhets kopior**. Du kan lämna skjutreglagen som de är eller justera dem. Välj **Geografiskt redundant** bland **redundansalternativen för säkerhetskopiering** om du vill använda geo-redundant lagring för dina serversäkerhetskopior. Spara den valda prisnivån genom att välja **OK**. På nästa skärmbild visas dessa val.

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad. Mer information finns på [sidan med priser](https://azure.microsoft.com/pricing/details/postgresql/).
   > 

    ![Fönstret ”Prisnivå”](./media/quickstart-create-database-portal/2-pricing-tier.png)

5. Välj **Granska + skapa** för att granska dina val. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.

6. Välj ikonen **Aviseringar** (en bjällra) i verktygsfältet för att övervaka distributionsprocessen. När distributionen är färdig kan du välja **Fäst på instrumentpanelen**. Då skapas en panel för den här servern på instrumentpanelen i Azure Portal som fungerar som en genväg till serverns **översiktssida**. Om du väljer **Gå till resurs** öppnas serverns **översiktssida**.

    ![Fönstret ”Aviseringar”](./media/quickstart-create-database-portal/3-notifications.png)
   
   Som standard skapas en **postgres**-databas under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. (Den andra standarddatabasen är **azure_maintenance**. Dess funktion är att skilja hanterade tjänstprocesser från användaråtgärder. Du har inte åtkomst till den här databasen.)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database för PostgreSQL skapar en brandvägg på server-nivå. Den förhindrar att externa program och verktyg ansluter till servern eller databaser på servern, om du inte konfigurerar en regel som öppnar brandväggen för specifika IP-adresser. 

1. Leta upp servern när distributionen är klar. Om det behövs kan du söka efter den. Välj exempelvis **Alla resurser** på menyn till vänster. Ange Server namnet, till exempel **mydemoserver**, för att söka efter den nyligen skapade servern. Välj servernamnet i sökresultatlistan. **Översikt**-sidan för din server öppnas och innehåller alternativ ytterligare konfiguration.
 
    ![Sökning efter servernamn](./media/quickstart-create-database-portal/4-locate.png)

2. På serversidan väljer du **Anslutningssäkerhet**.

3. Under **Brandväggsregler** väljer du den tomma textrutan i kolumnen **Regelnamn** och börjar skapa brandväggsregeln. 

   Fyll i fälten med ett namn och IP-adressintervall samt start- och slutdatum för klienter som kommer att ha åtkomst till din server. Om det är en enskild IP-adress använder du samma värde för start-IP och slut-IP.

   ![Konfigurera brandväggsregler](./media/quickstart-create-database-portal/5-firewall-2.png)
     

4. Välj **Spara** i det övre verktygsfältet på sidan **Anslutningssäkerhet**. Vänta tills meddelandet visas som talar om att uppdateringen av anslutningssäkerhet har slutförts innan du fortsätter.

    > [!NOTE]
    > Anslutningar till din Azure Database för PostgreSQL-server kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 5432.
    >

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När du skapar din Azure Database för PostgreSQL-server skapas även en standarddatabas med namnet **postgres**. Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan ha antecknat de här värdena tidigare i snabbstartsartikeln. I annat fall hittar du enkelt servernamnet och inloggningsuppgifterna på serversidan **Översikt** i portalen.

Öppna serverns **Översikt**-sida. Anteckna **Servernamn** och **Inloggningsnamn för serveradministratören**. Håll markören över varje fält så att kopieringssymbolen visas till höger om texten. Välj kopieringssymbolen för att kopiera värdena.

 ![Serversidan ”Översikt”](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database för PostgreSQL-server
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Följande kommando ansluter exempelvis till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Om du föredrar att använda en URL-sökväg för att ansluta till postgres, kodar URL @-tecknet i användar namnet med `%40`. Anslutnings strängen för psql skulle exempelvis vara, 
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

   När du har anslutit visar psql-verktyget en postgres-kommandotolk där du skriver sql-kommandon. Vid den första anslutningen kanske en varning visas eftersom det psql-verktyg du använder kan vara en annan version än Azure Database for PostgreSQL-serverversionen. 

   Exempel på psql-utdata:
   ```bash
   psql (9.5.7, server 9.6.2)
   WARNING: psql major version 9.5, server major version 9.6.
    Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=> 
   ```

   > [!TIP]
   > Om brandväggen inte är konfigurerad att tillåta IP-adressen för din klient uppstår följande fel:
   > 
   > "psql: allvarligt: No pg_hba. conf-posten för värd `<IP address>`, användare" Fjärråtkomsthanteraren ", databas" postgres ", SSL på OÅTERKALLELIG: SSL-anslutning krävs. Specify SSL options and retry.
   > 
   > Kontrol lera att klientens IP-adress tillåts i steg ovan för brand Väggs regler.

2. Skapa en tom databas med namnet "mypgsqldb" i kommandotolken genom att skriva följande kommando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. I kommandotolken kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
    ```bash
    \c mypgsqldb
    ```

4. Ange `\q` och tryck på retur för att avsluta psql. 

Du anslöt till Azure Database for PostgreSQL-servern via psql, och skapade en tom användardatabas. Fortsätt till nästa avsnitt för att ansluta med hjälp av ett annat vanligt verktyg, pgAdmin.

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Ansluta till PostgreSQL-servern med hjälp av pgAdmin

pgAdmin är ett verktyg med öppen källkod som används med PostgreSQL. Du kan installera pgAdmin från [pgAdmin-webbplatsen](https://www.pgadmin.org/). Den pgAdmin-version som du använder kan skilja sig från vad som används i den här snabbstarten. Läs dokumentationen för pgAdmin om du behöver ytterligare hjälp.

1. Öppna pgAdmin-programmet på klientdatorn.

2. Från verktygsfältet går du till **Objekt**, hovrar över **Skapa** och väljer **Server**.

3. I dialogrutan **Skapa – Server** på fliken **Allmänt** anger du ett unikt eget namn för servern, t.ex. **mydemoserver**.

    ![Fliken ”Allmänt”](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. Fyll i inställningstabellen i dialogrutan **Skapa – Server** på fliken **Anslutning**.

   ![Fliken ”Anslutning”](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-parameter |Värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | servernamn | Det värde för servernamn som du använde tidigare när du skapade Azure Database för PostgreSQL-server. Vår exempelserver är **mydemoserver.postgres.database.azure.com.** Använd det fullständiga domännamnet ( **\*.postgres.database.azure.com**) som i det här exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    Port | 5432 | Porten som ska användas när du ansluter till Azure Database för PostgreSQL-servern. 
    Underhållsdatabas | *postgres* | Systemgenererat standardnamn för databasen.
    Användarnamn | Inloggningsnamn för serveradministratör | Ange det användarnamn för serveradministratörsinloggning som du angav tidigare när du skapade Azure Database för PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *användar namn\@servername*.
    Lösenord | Ditt administratörslösenord | Det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    Roll | Lämna tomt | Du behöver inte ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | *Kräv* | Du kan ställa in SSL-läget på pgAdmin SSL-fliken. Som standard skapas alla Azure Database for PostgreSQL-servrar med tvingande SSL aktiverat. Om du vill inaktivera tvingande SSL kan läsa informationen om [tvingande SSL](./concepts-ssl-connection-security.md).
    
5. Välj **Spara**.

6. I fönstret **Webbläsare** till vänster expanderar du noden **Servrar**. Välj din server, till exempel **mydemoserver**, för att ansluta till den.

7. Expandera servernoden och expandera sedan **Databaser** under den. Listan bör innehålla din befintliga *postgres*-databas och eventuella andra databaser som du har skapat. Du kan skapa flera databaser per server med Azure Database for PostgreSQL.

8. Högerklicka på **databaser**, Välj menyn **skapa** och välj sedan **databas**.

9. Ange ett valfritt databas namn i fältet **databas** , till exempel **mypgsqldb2**.

10. Välj **Ägare** för databasen från den nedrullningsbara listan. Välj inloggnings namnet för Server administratören, till exempel **min administratör**.

    ![Skapa en databas i pgAdmin](./media/quickstart-create-database-portal/11-pgadmin-database.png)

11. Välj **Spara** för att skapa en tom databas.

12. I fönstret **Webbläsare** ser du den databas som du skapade i listan över databaser under servernamnet.


## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i snabbstarten på något av två sätt. Du kan ta bort den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som innehåller alla resurser i resursgruppen. Om du vill bevara alla andra resurser tar du bara bort serverresursen.

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Rensa inte upp resurserna som du skapade i den här snabbstarten om du tänker fortsätta att arbeta med efterföljande snabbstarter. Om du inte planerar att fortsätta följer du dessa steg för att ta bort alla resurser som har skapats i den här snabbstarten i portalen.

Ta bort hela resursgruppen, inklusive den nya servern:
1. Leta reda på resursgruppen i portalen. Välj **Resursgrupper** på den vänstra menyn. Välj sedan namnet på resursgruppen, som i vårt exempel **myresourcegroup**.

2. Välj **Ta bort** på din resursgruppssida. Ange namnet på resurs gruppen, till exempel **myresourcegroup**, i text rutan för att bekräfta borttagningen. Välj **Ta bort**.

Ta bort bara den nyligen skapade servern:
1. Leta upp servern i portalen om du inte har den öppen. Välj **Alla resurser** på menyn till vänster. Sök sedan efter den server som du skapade.

2. Välj **Ta bort** på sidan **Översikt**.

    ![Knappen ”Ta bort”](./media/quickstart-create-database-portal/12-delete.png)

3. Bekräfta namnet på servern som du vill ta bort och visa de databaser under den som påverkas. Ange Server namnet i text rutan, till exempel **mydemoserver**. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
