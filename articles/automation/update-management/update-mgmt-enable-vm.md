---
title: Aktivera Azure Automation Uppdateringshantering från en virtuell Azure-dator
description: Den här artikeln beskriver hur du aktiverar Uppdateringshantering från en virtuell Azure-dator.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 27832190125840e367edbfb2db8e4134f98b192d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450558"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Aktivera Uppdateringshantering från en virtuell Azure-dator

I den här artikeln beskrivs hur du kan använda en virtuell Azure-dator för att aktivera [uppdateringshantering](update-mgmt-overview.md) -funktionen på andra datorer. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Uppdateringshantering.

> [!NOTE]
> När du aktiverar Uppdateringshantering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](../index.yml) för att hantera datorer.
* En [virtuell dator](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>Aktivera funktionen för distribution

1. I [Azure Portal](https://portal.azure.com)väljer du **virtuella datorer** eller söker efter och väljer **virtuella datorer** från start sidan.

2. Välj den virtuella dator som du vill aktivera Uppdateringshantering för. Virtuella datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. Du

3. På sidan virtuell dator under **åtgärder**väljer du **uppdateringshantering**.

4. Du måste ha `Microsoft.OperationalInsights/workspaces/read` behörighet för att avgöra om den virtuella datorn är aktive rad för en arbets yta. Mer information om ytterligare behörigheter som krävs finns i [behörigheter som krävs för att aktivera datorer](../automation-role-based-access-control.md#feature-setup-permissions). Information om hur du aktiverar flera datorer samtidigt finns i [aktivera uppdateringshantering från ett Automation-konto](update-mgmt-enable-automation-account.md).

5. Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera uppdateringshantering. När du har aktiverat Uppdateringshantering kan det ta ungefär 15 minuter innan du kan visa uppdaterings utvärderingen från den virtuella datorn.

    ![Aktivera uppdateringshantering](media/update-mgmt-enable-vm/manageupdates-update-enable.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](update-mgmt-manage-updates-for-vm.md).

* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](../troubleshoot/update-management.md).
