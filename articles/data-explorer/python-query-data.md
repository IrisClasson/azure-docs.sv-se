---
title: Fråga mot data med hjälp av Python-biblioteket i Azure Data Explorer
description: I den här artikeln får du lära dig hur du frågar efter data från Azure Datautforskaren med hjälp av python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ebd65f2dcbb0040b764290627bbfd2901aa9a7d3
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443983"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Fråga mot data med hjälp av Python-biblioteket i Azure Data Explorer

I den här artikeln frågar du efter data med hjälp av Azure-Datautforskaren. Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata.

Azure Data Explorer tillhandahåller ett [dataklientbibliotek för Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Med det här biblioteket kan du fråga efter data från din kod. Anslut till en tabell i *Hjälp klustret* som vi har konfigurerat för att hjälpa till med inlärning. Du kan fråga en tabell i klustret och returnera resultatet.

Den här artikeln är även tillgänglig som en [Azure-anteckningsbok](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.4 +](https://www.python.org/downloads/)

* En e-postadress för ett organisationskonto som är medlem i Azure Active Directory (AAD)

## <a name="install-the-data-library"></a>Installera databiblioteket

Installera *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Lägg till importuttryck och konstanter

Importera klasser från biblioteket, och även dataanalysbiblioteket *Pandas*.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Azure Data Explorer använder ditt AAD-klient-ID för att autentisera ett program. Du hittar ditt klient-ID genom att använda följande URL. Byt ut *YourDomain* mot din domän.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicka på den här URL:en för att se resultatet. Den första raden ser ut så här.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Klient-ID är i det här fallet `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Ange värdet för AAD_TENANT_ID innan du kör den här koden.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

Skapa nu anslutningssträngen. I det här exemplet används enhetsautentisering för åtkomst till klustret. Du kan också använda ett [AAD-programcertifikat](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [en AAD-programnyckel](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) och [användare och lösenord för AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Ansluta till Azure Data Explorer och köra en fråga

Kör en fråga mot klustret och lagra utdata i en dataram. När den här koden körs returneras ett meddelande som ser ut ungefär så här: *Om du vill logga in öppnar du en webbläsare och går till sidan https://microsoft.com/devicelogin och anger koden F3W4VWZDM för att autentisera*. Följ stegen för att logga in och gå sedan tillbaka för att köra nästa kodblock.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Utforska data i DataFrame

När du gör en inloggning returnerar frågan ett resultat och det lagras i en dataram. Du kan arbeta med resultaten precis i andra dataramar.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Du bör se de tio främsta resultaten från tabellen StormEvents.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mata in data med Azure Datautforskaren python-biblioteket](python-ingest-data.md)
