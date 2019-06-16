---
title: Konfigurera och komma åt loggar för Azure Database för PostgreSQL – enskild Server i Azure Portal
description: Den här artikeln beskriver hur du konfigurerar och få åtkomst till serverloggar i Azure Database för PostgreSQL – enskild Server från Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13a8c1ee4b7ca114211f93245d74866e5aa5d52c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067455"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-in-the-azure-portal"></a>Konfigurera och komma åt Azure Database för PostgreSQL – enkel serverloggar i Azure portal

Du kan konfigurera, visa och hämta den [Azure Database för PostgreSQL loggar](concepts-server-logs.md) från Azure-portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom den här guiden, måste du:
- [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Konfigurera loggning
Konfigurera åtkomst till frågeloggar och felloggar. 

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj din Azure Database for PostgreSQL-server.

3. Under den **övervakning** avsnittet i sidopanelen, Välj **serverloggar**. 

   ![Välj serverloggar och välj ”Klicka här om du vill aktivera...”](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Markera rubriken **Klicka här för att aktivera loggar och konfigurera loggparametrarna** att se de serverparametrarna.

5. Ändra de parametrar som du behöver justera. Alla ändringar du gör i den här sessionen är markerade i lila.

   När du har ändrat parametrarna kan du klicka på **spara**. Eller så kan du **Ignorera** dina ändringar. 

   ![Lång lista med parametrar med ändringar att spara eller ta bort](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Gå tillbaka till listan över loggarna genom att klicka på den **stängningsknappen** (X ikonen) på den **serverparametrar** sidan.

## <a name="view-list-and-download-logs"></a>Visa lista och hämta loggar
Du kan visa en lista över tillgängliga loggar och hämta individuella loggfiler i fönstret serverloggar när loggning börjar. 

1. Öppna Azure Portal.

2. Välj din Azure Database for PostgreSQL-server.

3. Under den **övervakning** avsnittet i sidopanelen, Välj **serverloggar**. På sidan visas en lista över loggfilerna, som visas:

   ![Loggar serverlista](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Namngivningskonventionen loggens är **postgresql-åååå-mm-dd_hh0000.log**. Datum och tid som används i filnamnet är tid när loggen har utfärdats. Loggfilerna rotera var en timme eller storlek på 100 MB, beroende på vilket som inträffar först.

4. Om du behöver den **sökrutan** snabbt begränsa till en viss loggning baserat på datum/tid. Sökningen är på namnet på loggen.

   ![Exempelsökning på logg-namn](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Ladda ned enskilda loggfiler med hjälp av den **hämta** knappen (nedåt-ikon) bredvid varje loggfil i tabellraden som visas:

   ![Klicka på nedladdningsikonen](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Nästa steg
- Se [åtkomst till serverloggar i CLI](howto-configure-server-logs-using-cli.md) information om hur du hämtar loggar via programmering.
- Läs mer om [serverloggar](concepts-server-logs.md) i Azure DB för PostgreSQL. 
- Mer information om parameterdefinitioner och PostgreSQL-loggning finns i dokumentationen för PostgreSQL på [felrapportering och loggning](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

