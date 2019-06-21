---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187355"
---
## <a name="update-resources"></a>Uppdatera resurser

Det finns vissa begränsningar vad kan uppdateras. Följande objekt kan uppdateras: 

Delade bildgalleriet:
- Beskrivning

definition av avbildningen:
- Rekommenderade virtuella processorer
- Rekommenderat minne
- Beskrivning
- Slutet av liv datum

Avbildningsversion:
- Regionala replikantal
- Målregioner
- Undantag från den senaste
- Slutet av liv datum

Om du planerar att lägga till repliken regioner, ta inte bort hanterade Källavbildningen. Hanterade Källavbildningen krävs för att replikera versionsnumret för avbildningen till ytterligare regioner. 

Uppdatera beskrivningen av ett galleri med ([az sig uppdatering](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Uppdatera beskrivningen av en avbildning definition med hjälp av [az sig bild-Definitionsuppdatering](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Uppdatera en Bildversion för att lägga till en region för att replikera till med hjälp av [az sig versionsnumret för avbildningen uppdatering](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Den här ändringen kan ta ett tag när avbildningen replikeras till den nya regionen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Ta bort resurser

Du måste ta bort resurser i omvänd ordning genom att ta bort versionsnumret för avbildningen först. När du har tagit bort alla versioner bild kan du ta bort avbildningsdefinitionen. När du har tagit bort alla definitioner för avbildning kan du ta bort galleriet. 

Ta bort en avbildning version med hjälp av [az-versionsnumret för avbildningen sig ta bort](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Ta bort en avbildning definition med hjälp av [az sig avbildningsdefinitionen ta bort](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Ta bort en avbildning galleriet med hjälp av [az sig ta bort](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
