---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 2349c92fb74b546eaa929752f3d2343b9c97e6d1
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011037"
---
> [!NOTE]
> `PHI`Använd `domain=phi` parametern och modell versionen eller senare för att identifiera (skyddad hälso information) `2020-04-01` .
>
> Exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
Följande enhets kategorier returneras när begär Anden skickas till `/v3.1-preview.1/entities/recognition/pii` slut punkten.

| Kategori   | Underkategori | Beskrivning                          | Startar modell version | Anteckningar |
|------------|-------------|--------------------------------------|------------------------|---|
| Person     | E.t.         | Namn på personer.  | `2019-10-01`  | Returneras också med `domain=phi` . |
| PersonType | E.t.         | Jobb typer eller roller som innehas av en person. | `2020-02-01` | |
| PhoneNumber | E.t. | Telefonnummer (endast USA och EU-telefonnummer). | `2019-10-01` | Returneras också med`domain=phi` |
|Organisation  | E.t. | Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer.  | `2019-10-01` | Nationella objekt och religions ingår inte i den här entitetstypen.  |
|Organisation | Sjukdom | Medicinska företag och grupper. | `2020-04-01` |  |
|Organisation | Börs kurs | Fond börs grupper. | `2020-04-01` |  |
| Organisation | Sport | Idrotts relaterade organisationer. | `2020-04-01` |  |
| Adress | E.t. | Fullständiga post adresser.  | `2020-04-01` | Returneras också med `domain=phi` . |
| EU GPS-koordinater | E.t. | GPS-koordinater för platser inom Europeiska unionen.  | `2019-10-01` |  |
| E-post | E.t. | E-postadresser. | `2019-10-01` | Returneras också med `domain=phi` .   |
| URL | E.t. | URL: er till webbplatser. | `2019-10-01` | Returneras också med `domain=phi` . |
| IP-adress | E.t. | Nätverks-IP-adresser. | `2019-10-01` | Returneras också med `domain=phi` . |
| DateTime | E.t. | Datum och tidpunkter på dagen. | `2019-10-01` |  | 
| DateTime | Date | Kalender datum. | `2019-10-01` | Returneras också med `domain=phi` . |
| Kvantitet | E.t. | Siffror och numeriska kvantiteter. | `2019-10-01` |  |
| Kvantitet | Ålder | Personer. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure-information

Den här enhets kategorin innehåller identifierbar Azure-information, inklusive autentiseringsinformation och anslutnings strängar. Tillgängligt från och med modell version `2019-10-01` . Returnerades inte med `domain=phi` parametern.

| Underkategori                           | Beskrivning                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB auth-nyckel             | Auktoriseringsregel för en Azure DocumentDB-Server.                           |
| Anslutnings sträng för Azure IAAS Database och Azure SQL-anslutningssträng | Anslutnings sträng för en Azure Infrastructure as a service-databas (IaaS) och SQL-anslutningssträng. |
| Azure SQL-anslutningssträng           | Anslutnings sträng för en databas i Azure SQL Database.                                |
| Azure IoT-anslutningssträng           | Anslutnings sträng för Azure sakernas internet (IoT).                        |
| Lösen ord för Azure publicerings inställning        | Lösen ord för Azures publicerings inställningar.                                        |
| Azure Redis Cache anslutnings sträng   | Anslutnings sträng för en Azure-cache för Redis.                             |
| Azure SAS                             | Anslutnings sträng för Azure program vara som en tjänst (SAS).                     |
| Azure Service Bus anslutnings sträng   | Anslutnings sträng för en Azure Service Bus.                                 |
| Azure Storage konto nyckel             | Konto nyckel för ett Azure Storage-konto.                                   |
| Azure Storage konto nyckel (allmän)   | Allmän konto nyckel för ett Azure Storage-konto.                           |
| SQL Server anslutnings sträng          | Anslutnings sträng för en SQL-Server.                                         |

## <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
