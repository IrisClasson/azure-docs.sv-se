---
title: 'Snabbstart: Tabell-API med Python – Azure Cosmos DB'
description: Den här snabbstarten visar hur du använder tabell-API i Azure Cosmos DB för att skapa ett program med Azure-portalen och Python
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: 358f55594037f4a468d31265f4e65a6ec4d7ef28
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020138"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Snabbstart: Skapa en tabell-API-app med Python och Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Den här snabbstarten visar hur du använder [tabell-API](table-introduction.md) i Python och Azure Cosmos DB för att skapa en app genom att klona ett exempel från GitHub. Den här snabbstarten visar även hur du skapar ett Azure Cosmos DB-konto och hur du använder Datautforskaren för att skapa tabeller och entiteter i den webbaserade Azure-portalen.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par, breda kolumner och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Dessutom:

* Om du inte redan har Visual Studio 2019 installerat kan du hämta och använda den **kostnads fria** [versionen av Visual Studio 2019 community](https://www.visualstudio.com/downloads/). Se till att du väljer arbetsbelastningarna **Azure-utveckling** och **Python-utveckling** under konfigurationen av Visual Studio.
* Välj även alternativet Python 2 i arbetsbelastningen **Python-utveckling** eller ladda ned Python 2.7 från [python.org](https://www.python.org/downloads/release/python-2712/).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

> [!IMPORTANT] 
> Du måste skapa ett nytt tabell-API-konto för att arbeta med de allmänt tillgängliga tabell-API SDK:erna. Tabell-API-konton som skapas i förhandsversionen stöds inte av de allmänt tillgängliga SDK:erna.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Lägg till en tabell

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Lägg till exempeldata

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi klona en Table-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Öppna därefter lösningsfilen i Visual Studio. 

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen. På så vis kan appen kommunicera med den värdbaserade databasen. 

1. På [Azure-portalen](https://portal.azure.com/) väljer du **Anslutningssträng**. 

    ![Visa och kopiera ANSLUTNINGSSTRÄNG i fönstret Anslutningssträng](./media/create-table-python/connection-string.png)

2. Kopiera KONTONAMN med knappen till höger.

3. Öppna filen config.py och klistra in KONTONAMN från portalen för värdet STORAGE_ACCOUNT_NAME på rad 19.

4. Gå tillbaka till portalen och kopiera PRIMÄRNYCKEL.

5. Klistra in PRIMÄRNYCKEL från portalen i värdet STORAGE_ACCOUNT_KEY på rad 20.

3. Spara filen config.py.

## <a name="run-the-app"></a>Kör appen

1. I Visual Studio högerklickar du på projektet i **Solution Explorer**, väljer den aktuella Python-miljön och högerklickar.

2. Välj Installera python-paket och ange sedan **Azure-Storage-Table**

3. Tryck på F5 för att köra programmet. Appen visas i din webbläsare. 

Du kan nu gå tillbaka till datautforskaren och se frågan, ändra och arbeta med dessa nya data. 

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du fått lära dig att skapa ett Azure Cosmos DB-konto, skapa en tabell med datautforskaren och att köra en app.  Du kan nu ställa frågor mot dina data med tabell-API:t.  

> [!div class="nextstepaction"]
> [Importera tabelldata till tabell-API](table-import.md)
