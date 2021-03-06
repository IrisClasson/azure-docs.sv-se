---
title: Ansluta till Azure Media Services v3 API – python
description: Den här artikeln visar hur du ansluter till Media Services v3 API med python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: devx-track-python
ms.openlocfilehash: 834d87f607ca65a9d9e0ebee12d2772f5baf7949
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849649"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ansluta till Media Services v3 API – python

Den här artikeln visar hur du ansluter till Azure Media Services v3 python SDK med hjälp av inloggnings metoden för tjänstens huvud namn.

## <a name="prerequisites"></a>Förutsättningar

- Hämta python från [python.org](https://www.python.org/downloads/)
- Se till att ställa in `PATH` miljövariabeln
- [Skapa ett Media Services-konto](./create-account-howto.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.
- Följ stegen i avsnittet [åtkomst-API: er](./access-api-howto.md) . Registrera prenumerations-ID, program-ID (klient-ID), den autentiseringsnyckel (hemlighet) och klient-ID som du behöver i senare steg.

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Azure Media Services med hjälp av python måste du installera dessa moduler.

* `azure-mgmt-resource`Modulen, som innehåller Azure-moduler för Active Directory.
* `azure-mgmt-media`Modulen, som innehåller Media Services entiteter.

Öppna ett kommando rads verktyg och Använd följande kommandon för att installera modulerna.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Anslut till python-klienten

1. Skapa en fil med ett `.py` tillägg
1. Öppna filen i din favorit redigerare
1. Lägg till koden som följer efter filen. Koden importerar de nödvändiga modulerna och skapar objektet Active Directory autentiseringsuppgifter som du måste ansluta till Media Services.

      Ange Variablernas värden till de värden som du har fått från [åtkomst till API: er](./access-api-howto.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Kör filen

## <a name="next-steps"></a>Nästa steg

- Använd [python SDK](https://aka.ms/ams-v3-python-sdk).
- Granska Media Services [Python-referensdokumentationen](https://aka.ms/ams-v3-python-ref).
