---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: fe1b4699a300831294c26b103d322fb83ad87d3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116694"
---
Om du vill konfigurera en HTTP-proxy för utgående förfrågningar kan använda dessa två argument:

| Namn | Datatyp | Beskrivning |
|--|--|--|
|HTTP_PROXY|string|proxy för att använda, till exempel `http://proxy:8888`<br>< proxy-url >|
|HTTP_PROXY_CREDS|string|autentiseringsuppgifter krävs för att autentisera mot proxy, till exempel användarnamn: lösenord.|
|`<proxy-user>`|string|Användaren för proxyservern.|
|`proxy-password`|string|Lösenordet som associeras med `<proxy-user>` för proxyn.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```