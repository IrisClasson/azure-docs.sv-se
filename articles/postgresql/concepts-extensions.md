---
title: Använda PostgreSQL-tillägg i Azure Database för PostgreSQL – enskild Server
description: Beskriver möjlighet att utöka funktionerna i din databas med tillägg i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: efa4cc070f47174634c8dc67b37f10bc3d112d08
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293207"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-tillägg i Azure Database för PostgreSQL – enskild Server
PostgreSQL ger möjlighet att utöka funktionerna i din databas med tillägg. Tillägg kan paketera flera relaterade SQL-objekt tillsammans i ett enda paket som kan läsas in eller tas bort från databasen med ett enda kommando. Tillägg kan fungera som gör de inbyggda funktionerna för efter att läsas in i databasen. Läs mer på PostgreSQL-tillägg, [paketering relaterade objekt i ett tillägg](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Hur du använder PostgreSQL-tillägg
PostgreSQL-tillägg måste installeras i databasen innan du kan använda dem. Installera ett visst tillägg genom att köra den [skapa tillägg](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) från psql-verktyget för att läsa in paketerade objekten i databasen.

Azure Database för PostgreSQL stöder för närvarande en delmängd av viktiga tillägg som anges ovan. Tillägg utöver de som visas stöds inte. Du kan inte skapa dina egna tillägg med Azure Database för PostgreSQL-tjänsten.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Tillägg som stöds av Azure Database för PostgreSQL
I tabellerna nedan listas de standard PostgreSQL-tillägg som för närvarande stöds av Azure Database för PostgreSQL. Den här informationen är också tillgängligt genom att köra `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Datatillägg för typer

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Ger en datatyp för automatisk-krypterade lösenord. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Innehåller datatypen är skiftlägeskänslig tecken. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Ger en datatyp för flerdimensionella kuber. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Innehåller en datatyp för att lagra uppsättningar med nyckel/värde-par. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Innehåller datatyper för internationella produkten numrering standarder. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Ger en datatyp för den hierarkiska träd-liknande strukturer. |

### <a name="functions-extensions"></a>Functions-tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Innebär att beräkna bra cirkel avstånd på jordens yta. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Har flera funktioner för att fastställa likheter och avståndet mellan strängar. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Tillhandahåller funktioner och operatorer för att manipulera null kostnadsfria matriser med heltal. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Tillhandahåller kryptografiska funktioner. |
> | [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hanterar partitionerade tabeller genom tid eller -ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Innehåller funktioner och operatorer för att fastställa likheten mellan alfanumerisk text baserat på trigram matchning. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Innehåller funktioner som manipulerar hela tabeller, inklusive korstabell. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genererar universell unik identifierare (UUID). |
> | [orafce](https://github.com/orafce/orafce) | Tillhandahåller en deluppsättning av funktioner och paket som emuleras från kommersiella databaser. |

### <a name="full-text-search-extensions"></a>Fulltextsökning tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Innehåller en mall för text search ordlista för heltal. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | En text search ordlista som tar bort uttal (diakritiska tecken) från lexemes. |

### <a name="index-types-extensions"></a>Index typer tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [b-trädet\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Innehåller exempel GIN operatorn klasser som implementerar B-trädet som beteendet för vissa datatyper. |
> | [b-trädet\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Innehåller GiST index operatorn klasser som implementerar B-trädet. |

### <a name="language-extensions"></a>Språktillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL kan läsas in procedurmässig språk. |
> | [plv8](https://plv8.github.io/) | Ett Javascript-språktillägg för PostgreSQL som kan användas för lagrade procedurer, utlösare, osv. |

### <a name="miscellaneous-extensions"></a>Diverse tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Ger möjlighet att undersöka vad som händer i delade buffert cachen i realtid. |
> | [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Är ett sätt att läsa in relationsdata till bufferten-cachen. |
> | [PG\_stat\_uttryck](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Ger möjlighet att spåra statistik för körning av alla SQL-instruktioner som körs av en server. (Se nedan för en anteckning i det här tillägget). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Ger ett sätt för att visa på radnivå låsning information. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Ger ett sätt för att visa statistik i tuppeln på servernivå. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Främmande data omslutning används för att komma åt data som lagras i externa PostgreSQL-servrar. (Se nedan för en anteckning i det här tillägget).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Ger dig möjlighet att skapa hypotetiskt index som inte kostar CPU eller disk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | En modul som stöder anslutningar till andra PostgreSQL-databaser i en databas-session. (Se nedan för en anteckning i det här tillägget). |


### <a name="postgis-extensions"></a>PostGIS tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topologi, postgis\_tiger\_geocoder, postgis\_sfcgal | Spatial- och geografiska objekt för PostgreSQL. |
> | adress\_standardizer, adress\_standardizer\_data\_oss | Används för att parsa en adress till innehåll. Används för att stödja geokodning adress normalisering steg. |
> | [pgrouting](https://pgrouting.org/) | Utökar PostGIS / PostgreSQL geospatiala databasen att tillhandahålla geospatiala routning funktioner. |


### <a name="time-series-extensions"></a>Time series-tillägg

> [!div class="mx-tableFixed"]
> | **Tillägget** | **Beskrivning** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | En time series-SQL-databas som stöd för automatisk partitionering för snabbare mata in och frågor. Innehåller tid inriktad analytiska funktioner, optimeringar, och skalar PostgreSQL för time series-arbetsbelastningar. TimescaleDB utvecklas av och ett registrerat varumärke som tillhör [tidsskalan, Inc.](https://www.timescale.com/) (Se nedan för en anteckning i det här tillägget). |


## <a name="pgstatstatements"></a>pg_stat_statements
Den [pg\_stat\_instruktioner tillägget](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) är förinstallerade på varje Azure Database for PostgreSQL-server att ge dig ett sätt att spåra statistik för körning av SQL-uttryck.
Inställningen `pg_stat_statements.track`, som styr vilka instruktioner räknas av tillägget, standardvärdet är `top`, vilket innebär att alla instruktioner direkt från klienter spåras. Två andra spårnings-nivåerna är `none` och `all`. Den här inställningen kan konfigureras som en parameter för server via den [Azure-portalen](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Det finns en kompromiss mellan körning frågeinformationen pg_stat_statements ger och påverkan på serverprestanda som loggas varje SQL-uttryck. Om du inte aktivt använder tillägget pg_stat_statements, rekommenderar vi att du ställer in `pg_stat_statements.track` till `none`. Observera att vissa tredjeparts övervakningstjänster vara beroende av pg_stat_statements att leverera prestandainsikter för frågan, så kontrollera om så är fallet för dig eller inte.

## <a name="dblink-and-postgresfdw"></a>dblink och postgres_fdw
dblink och postgres_fdw kan du ansluta från en PostgreSQL-server till en annan eller till en annan databas på samma server. Mottagande servern måste tillåta anslutningar från den sändande servern via dess brandvägg. När du använder dessa tillägg för att ansluta till Azure Database for PostgreSQL-servrar, kan detta göras genom att ställa in ”Tillåt åtkomst till Azure-tjänster” till ON. Detta krävs också om du vill använda tilläggen för att gå tillbaka till samma server. Inställningen ”Tillåt åtkomst till Azure-tjänster” finns på sidan för Azure portal för Postgres-servern under anslutningssäkerhet. Aktivera ”Tillåt åtkomst till Azure-tjänster” på vitlistor alla Azure-IP-adresser.

För närvarande stöds utgående anslutningar från Azure Database för PostgreSQL inte, förutom anslutningar till andra Azure Database for PostgreSQL-servrar.

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB är en time series-databas som är packade som ett tillägg för PostgreSQL. TimescaleDB innehåller tid inriktad analytiska funktioner, optimeringar, och skalar Postgres för time series-arbetsbelastningar.

[Mer information om TimescaleDB](https://docs.timescale.com/latest), ett registrerat varumärke som tillhör [tidsskalan, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Installera TimescaleDB
Om du vill installera TimescaleDB, måste du inkludera den i serverns delade true bibliotek. En ändring av Postgres's `shared_preload_libraries` parametern kräver ett **omstart av servern** ska börja gälla. Du kan ändra parametrarna med hjälp av den [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB kan aktiveras i Azure Database för PostgreSQL-version 9.6 och 10

Med hjälp av den [Azure-portalen](https://portal.azure.com/):

1. Välj din Azure Database for PostgreSQL-server.

2. Välj på sidopanelen, **serverparametrar**.

3. Sök efter den `shared_preload_libraries` parametern.

4. Välj **TimescaleDB**.

5. Välj **spara** att bevara dina ändringar. Du får ett meddelande när ändringen har sparats. 

6. Efter meddelandet, **starta om** servern för att tillämpa ändringarna. Läs hur du startar om en server i [starta om en Azure Database for PostgreSQL-server](howto-restart-server-portal.md).


Du kan nu aktivera TimescaleDB i Postgres-databasen. Ansluta till databasen och kör du följande kommando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Om du ser ett fel, bekräfta att du [startas om din server](howto-restart-server-portal.md) när du har sparat shared_preload_libraries. 

Nu kan du skapa en TimescaleDB hypertable [från grunden](https://docs.timescale.com/getting-started/creating-hypertables) eller migrera [befintliga time series-data i PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Nästa steg
Om du inte ser ett tillägg som du vill använda, berätta för oss. Rösta på befintliga begäranden eller skapa nya feedback från intressenter i vår [Feedbackforum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
