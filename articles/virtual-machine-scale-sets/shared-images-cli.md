---
title: Använd delade VM-avbildningar för att skapa en skalningsuppsättning i Azure | Microsoft Docs
description: Lär dig hur du använder Azure CLI för att skapa delade VM-avbildning som ska användas för att distribuera VM-skalningsuppsättningar i Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: c002e7c107c4dcbcd7eeff9579fae6893483392e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "66156177"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Skapa och använda delade avbildningar för VM-skalningsuppsättningar med Azure CLI 2.0

När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. [Delade Image Galleries](shared-image-galleries.md) förenklar anpassad avbildning delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. Delad galleriet kan du dela din anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner inom en AAD-klient. Välj vilka bilder som du vill dela, vilka regioner som du vill göra dem tillgängliga i och som du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar. Galleriet är en resurs på toppnivå som ger fullständig rollbaserad åtkomstkontroll (RBAC). Bilder kan versionshanteras och du kan välja att replikera varje versionsnumret för avbildningen till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade avbildningar. 

>[!NOTE]
> Den här artikeln beskriver hur du använder en generaliserad avbildning av hanterade. Det går inte att skapa en skalningsuppsättning från en särskild VM-avbildning.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Skapa en skalningsuppsättning från den anpassad virtuella datoravbildningen
Skapa en skalningsuppsättning med [ `az vmss create` ](/cli/azure/vmss#az-vmss-create). Istället för en plattformsavbildning som *UbuntuLTS* eller *CentOS*, anger du namnet på din anpassade virtuella datoravbildning. Följande exempel skapar en skalningsuppsättning med namnet *myScaleSet* som använder den anpassade avbildningen med namnet *myImage* från föregående steg:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg

Du kan också skapa delade bildgalleriet resursen med hjälp av mallar. Det finns flera Azure-Snabbstartsmallar: 

- [Skapa en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en Definition för bilden i en delad Avbildningsgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en Bildversion i en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från Avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Om du stöter på problem, kan du [Felsöka delade bildgallerier](troubleshooting-shared-images.md).
