---
title: Dela Galleri bilder över klienter i Azure
description: Lär dig hur du delar VM-avbildningar i Azure-klienter med hjälp av delade avbildnings gallerier.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: 542dfbc28621e02cd2842fb174e986bfeb91e6ec
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501686"
---
# <a name="share-gallery-vm-images-across-tenants-in-azure"></a>Dela Galleri VM-avbildningar över klienter i Azure

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Skapa en skalningsuppsättning med Azure CLI

Logga in tjänstens huvud namn för klient organisation 1 med hjälp av appID, appens nyckel och ID för klient 1. Du kan använda `az account show --query "tenantId"` för att hämta klient-ID: n vid behov.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Logga in tjänstens huvud namn för klient 2 med hjälp av appID, appens nyckel och ID: t för klient organisationen 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Skapa skalnings uppsättningen. Ersätt informationen i exemplet med din egen.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem kan du [Felsöka delade avbildnings gallerier](troubleshooting-shared-images.md).
