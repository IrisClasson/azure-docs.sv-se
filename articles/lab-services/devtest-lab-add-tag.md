---
title: Lägga till taggar i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en tagg i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e4d9aeb527461cc7292235fef1de0abdfa4242bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311376"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Lägga till taggar i ett labb i Azure DevTest Labs

Du kan skapa anpassade taggar och tillämpa dem på dina labb-resurser för att kategorisera logiskt dina resurser. Senare kan du snabbt och se enkelt alla resurser i din prenumeration som har den taggen. Taggar är användbara när du behöver organisera resurser för fakturering eller hantering.

Resurser som stöds av taggar är

* Compute virtuella datorer
* Nätverkskort
* IP-adresser
* Lastbalanserare
* Lagringskonton
* Hanterade diskar

Du kan använda taggar när du [skapa ett labb](devtest-lab-create-lab.md) och hantera dem senare via bladet taggar under konfiguration och inställningar.

Varje tagg består av en **namn**/**värdet** par. Du kan till exempel skapa en tagg med namnet *costcenter* som har värdet *34543*. En tagg som detta kan hjälpa dig att senare identifiera labbresurser som debiteras till den här viss del av din organisation. Du får välja namn och värden som passar för hur du vill organisera din prenumeration.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Steg för att hantera taggar i en befintlig labb

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** i listan. Labbet kanske redan visas på instrumentpanelen under **alla resurser**.
1. I listan över labbar Välj labb där du vill lägga till eller hantera taggar.
1. På testmiljön **översikt** Välj **konfiguration och principer**.

    ![Knappen för konfiguration och principer](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Till vänster under **hantera**väljer **taggar**.
1. Om du vill skapa en ny post för den här övningen, ange en **namn**/**värdet** parkopplas och välj **spara**. Du kan också välja en befintlig tagg från listan för att visa eller hantera resurser som är associerade med taggen.

    ![Hantera taggar](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Förstå begränsningar för taggar

Följande begränsningar gäller för taggar:

* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

[Använd taggar för att organisera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) innehåller bättre information om hur du använder taggar i Azure, inklusive hur du hanterar taggar med PowerShell eller Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Du kan använda begränsningar och konventioner på din prenumeration med hjälp av anpassade principer. En princip som du definierar kan kräva att alla resurser har ett värde för en viss tagg. Mer information finns i [ange principer och scheman](devtest-lab-set-lab-policy.md).
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
