---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673319"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Hämta Azure Storage anslutnings sträng

Tidigare skapade du ett Azure Storage-konto som ska användas av Function-appen. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. Genom att Hämta inställningen till den *lokala. Settings. JSON* -filen kan du använda den för att skriva till en lagrings kö i samma konto när funktionen körs lokalt. 

1. Kör följande kommando från roten i projektet och Ersätt `<app_name>` med namnet på din Function-app från föregående snabb start. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Öppna *Local. Settings. JSON* och leta upp värdet med namnet `AzureWebJobsStorage` , som är anslutnings strängen för lagrings kontot. Du använder namnet `AzureWebJobsStorage` och anslutnings strängen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *Local. Settings. JSON* innehåller hemligheter som hämtats från Azure, ska du alltid utesluta den här filen från käll kontroll. Filen *. gitignore* som skapas med ett lokalt Functions-projekt utesluter filen som standard.