---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166068"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Tagga en virtuell dator via mallar
Först ska vi titta på Taggning via mallar. [Den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) placerar taggar på följande resurser: Beräkning (virtuell dator), lagring (Storage-konto) och nätverk (offentlig IP-adress, virtuellt nätverk och gränssnitt). Den här mallen är för en virtuell Windows-dator, men kan anpassas för virtuella Linux-datorer.

Klicka på den **distribuera till Azure** knappen från den [Mallänk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Detta kommer gå till den [Azure-portalen](https://portal.azure.com/) där du kan distribuera den här mallen.

![Enkel distribution med taggar](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Den här mallen innehåller följande taggar: *Avdelning*, *programmet*, och *skapats av*. Du kan lägga till och redigera dessa taggar direkt i mallen om du vill ha olika taggnamn.

![Azure-taggar i en mall](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Som du ser definieras taggarna som nyckel/värde-par, avgränsade med kolon (:). Taggar måste definieras i följande format:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Spara mallfilen när du har redigerat med taggar valfri.

I den **redigera parametrar** avsnittet ska du fylla i värdena för taggarna.

![Redigera taggar i Azure-portalen](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klicka på **skapa** att distribuera den här mallen med dina taggvärden.

## <a name="tagging-through-the-portal"></a>Tagga via portalen
När du har skapat dina resurser med taggar kan du visa, lägga till och ta bort taggar i portalen.

Välj ikonen taggar för att visa dina taggar:

![Taggar ikonen i Azure-portalen](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Lägg till en ny tagg via portalen genom att definiera din egen nyckel/värde-par och spara den.

![Lägg till ny tagg i Azure-portalen](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Din ny tagg bör nu visas i en lista över taggar för resursen.

![Ny tagg som sparats i Azure-portalen](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

