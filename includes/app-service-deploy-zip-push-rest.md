---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 7aa0d232cf53eef9bd28c36b66e8fdae22a28db9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132553"
---
## <a name="rest"></a>Distribuera ZIP-filen med REST API: er 

Du kan använda den [distributionstjänst för REST API: er](https://github.com/projectkudu/kudu/wiki/REST-API) att distribuera .zip-filen till din app i Azure. Om du vill distribuera, skicka en POST-begäran till https://<app_name>.scm.azurewebsites.net/api/zipdeploy. POST-begäran måste innehålla .zip-filen i meddelandetexten. Autentiseringsuppgifterna för distribution för din app tillhandahålls i begäran med hjälp av grundläggande HTTP-autentisering. Mer information finns i den [referens för distribution av .zip-push](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

För grundläggande HTTP-autentisering måste du dina autentiseringsuppgifter för distribution av App Service. Om du vill se hur du anger dina autentiseringsuppgifter för distribution, se [ange och Återställ användarnivå autentiseringsuppgifter](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Med cURL

I följande exempel används verktyget cURL för att distribuera en ZIP-fil. Ersätt platshållarna `<username>`, `<password>`, `<zip_file_path>`, och `<app_name>`. När du uppmanas av cURL anger du lösenordet.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Den här begäran utlöser push-distribution från den uppladdade ZIP-fil. Du kan granska aktuella och tidigare distributioner med hjälp av den `https://<app_name>.scm.azurewebsites.net/api/deployments` slutpunkt, enligt följande cURL-exempel. Ersätter `<app_name>` med namnet på din app och `<deployment_user>` med användarnamnet för dina autentiseringsuppgifter för distribution.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Med PowerShell

I följande exempel används [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) att skicka en begäran som innehåller ZIP-filen. Ersätt platshållarna `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, och `<app_name>`.

```powershell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

Den här begäran utlöser push-distribution från den uppladdade ZIP-fil. Kör följande kommandon för att granska de aktuella och tidigare distributionerna. Ersätter den `<app_name>` platshållare.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
