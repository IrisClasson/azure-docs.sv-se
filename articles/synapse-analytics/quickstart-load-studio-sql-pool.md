---
title: 'Snabb start: Mass inläsning av data med Synapse kv'
description: Använd Synapse Studio för att läsa in data i Synapse SQL
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: fb618f155e659ecd1fa5760117193a9045e6da35
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535135"
---
# <a name="bulk-loading-with-synapse-sql"></a>Mass inläsning med Synapse SQL

Det har aldrig varit enklare att läsa in data när du använder guiden för Mass inläsning i Synapse Studio. I den här guiden får du hjälp med att skapa ett T-SQL-skript med [copy-instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) för Mass inläsning av data. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Start punkter i guiden för Mass inläsning

Du kan nu enkelt Mass inläsning av data med hjälp av SQL-pooler med enkel högerklickning i följande områden i Synapse Studio:

- En fil eller mapp från ett Azure Storage-konto som är kopplat till din arbets yta genom ![ att högerklicka på en fil eller mapp från ett lagrings konto](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Förutsättningar

- Den här guiden genererar en COPY-instruktion som använder AAD-vidarekoppling för authentcation. Din [AAD-användare måste ha åtkomst](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples#d-azure-active-directory-authentication-aad) till arbets ytan med minst Azure-rollen Storage BLOB data Contributor till ADLS Gen2 kontot.

- Du måste ha de [behörigheter som krävs för att använda kopierings instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#permissions) och skapa tabell behörigheter om du skapar en ny tabell som ska läsas in på.

- Den länkade tjänst som är kopplad till ADLS Gen2 kontot **måste ha åtkomst till** / **filmappen** för att kunna läsas in. Om den länkade tjänstens autentiseringsmekanism exempelvis är hanterad identitet, måste den hanterade arbets ytans identitet ha minst behörighet för lagrings-BLOB-läsare på lagrings kontot.

- Om VNet är aktiverat på din arbets yta, se till att den integrerade körningen som är kopplad till det ADLS Gen2-konto som är länkade till data källans källdata och felfilen har aktive rad redigering aktive rad. Interaktiv redigering krävs för automatisk schema identifiering, för att Visa käll filens innehåll och bläddra ADLS Gen2 lagrings konton i guiden.

### <a name="steps"></a>Steg

1. Välj det lagrings konto och den fil eller mapp som du läser in från panelen käll lagrings plats: ![ välja käll plats](./sql/media/bulk-load/bulk-load-source-location.png)

2. Välj fil formats inställningar, inklusive det lagrings konto där du vill skriva avvisade rader (felfilen). För närvarande stöds endast CSV-och Parquet-filer.

    ![Välja fil formats inställningar](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Du kan klicka på "Förhandsgranska data" för att se hur KOPIERINGs instruktionen kommer att parsa filen så att du kan konfigurera fil formats inställningarna. Klicka på "Förhandsgranska data" varje gång du ändrar fil formats inställningen för att se hur KOPIERINGs instruktionen kommer att parsa filen med den uppdaterade inställningen: för hands version av ![ data](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

4. Välj den SQL-pool som du använder för att läsa in, inklusive om belastningen ska användas för en befintlig tabell eller ny tabell: Välj ![ mål plats](./sql/media/bulk-load/bulk-load-target-location.png)

5. Klicka på Konfigurera kolumn mappning för att kontrol lera att du har rätt kolumn mappning. För nya tabeller är det viktigt att konfigurera kolumn mappningen för att uppdatera mål kolumnens data typer: ![ Konfigurera kolumn mappning](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Klicka på "öppna skript" och ett T-SQL-skript genereras med KOPIERINGs instruktionen att läsa in från data Lake: ![ Öppna SQL-skriptet](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Nästa steg

- Se artikeln [Kopiera instruktion](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) för mer information om kopierings funktioner
- Se artikeln [Översikt över data inläsning](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt)
