---
title: Aktivera Azure Automation Uppdateringshantering från Runbook
description: Den här artikeln beskriver hur du aktiverar Uppdateringshantering från en Runbook.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: a5b6fe5cf83eaa18b34a00767e14e75737aa055e
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450666"
---
# <a name="enable-update-management-from-a-runbook"></a>Aktivera Uppdateringshantering från en runbook

I den här artikeln beskrivs hur du kan använda en Runbook för att aktivera [uppdateringshantering](update-mgmt-overview.md) funktionen för virtuella datorer i din miljö. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Uppdateringshantering. 

> [!NOTE]
> När du aktiverar Uppdateringshantering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](../index.yml) för att hantera datorer.
* En [virtuell dator](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

1. I ditt Automation-konto väljer du **uppdateringshantering** under **uppdateringshantering**.

2. Välj arbets ytan Log Analytics och klicka sedan på **Aktivera**. När Uppdateringshantering aktive ras visas en blå banderoll.

    ![Aktivera uppdateringshantering](media/update-mgmt-enable-runbook/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Välj den virtuella Azure-dator som ska hanteras

Med Uppdateringshantering aktiverat kan du lägga till en virtuell Azure-dator för att ta emot uppdateringar.

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Välj **Lägg till virtuella Azure-datorer** för att lägga till den virtuella datorn.

3. Välj den virtuella datorn i listan och klicka på **Aktivera** för att konfigurera den virtuella datorn för uppdateringar.

   ![Aktivera Uppdateringshantering för virtuell dator](media/update-mgmt-enable-runbook/enable-update.png)

    > [!NOTE]
    > Om du försöker aktivera en annan funktion innan installationen av Uppdateringshantering har slutförts visas följande meddelande:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Du måste uppdatera till de senaste Azure-modulerna och importera modulen [AZ. OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) för att kunna aktivera uppdateringshantering för dina virtuella datorer.

1. I ditt Automation-konto väljer du **moduler** under **delade resurser**.
2. Välj **Uppdatera Azure-moduler** för att uppdatera Azure-moduler till den senaste versionen.
3. Klicka på **Ja** om du vill uppdatera alla befintliga Azure-moduler till den senaste versionen.

    ![Uppdatera moduler](media/update-mgmt-enable-runbook/update-modules.png)

4. Återgå till **moduler** under **delade resurser**.
5. Välj **Bläddra i galleriet** för att öppna modulens Galleri.
6. Sök efter `Az.OperationalInsights` och importera modulen till ditt Automation-konto.

    ![Importera modulen OperationalInsights](media/update-mgmt-enable-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Importera en Runbook för att aktivera Uppdateringshantering

1. I ditt Automation-konto väljer du **Runbooks** under **process automatisering**.
2. Välj **Sök i galleri**.
3. Sök efter `update and change tracking`.
4. Välj Runbook och klicka på **Importera** på sidan Visa källa.
5. Klicka på **OK** för att importera runbooken till Automation-kontot.

   ![Importera Runbook för installation](media/update-mgmt-enable-runbook/import-from-gallery.png)

6. På sidan Runbook klickar du på **Redigera**och väljer sedan **publicera**.
7. I fönstret publicera Runbook klickar du på **Ja** för att publicera runbooken.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha aktiverat Uppdateringshantering för att en virtuell Azure-dator ska kunna starta denna Runbook. Den kräver en befintlig virtuell dator och resurs grupp med funktionen aktive rad för parametrar.

1. Öppna runbooken **Enable-MultipleSolution** .

   ![Runbook med flera lösningar](media/update-mgmt-enable-runbook/runbook-overview.png)

2. Klicka på Start-knappen och ange parameter värden i följande fält:

   * **VMNAME** – namnet på en befintlig virtuell dator som ska läggas till i uppdateringshantering. Lämna fältet tomt om du vill lägga till alla virtuella datorer i resurs gruppen.
   * **VMRESOURCEGROUP** – namnet på resurs gruppen för de virtuella datorer som ska aktive ras.
   * **SUBSCRIPTIONID** -PRENUMERATIONS-ID för den nya virtuella dator som ska aktive ras. Lämna fältet tomt om du vill använda arbets ytans prenumeration. När du använder ett annat prenumerations-ID lägger du till kör som-kontot för ditt Automation-konto som deltagare för prenumerationen.
   * **ALREADYONBOARDEDVM** – namnet på den virtuella dator som redan har Aktiver ATS manuellt för uppdateringar.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – namnet på den resurs grupp som den virtuella datorn tillhör.
   * **SOLUTIONTYPE** – ange **uppdateringar**.

   ![Runbookparametrarna Enable-MultipleSolution](media/update-mgmt-enable-runbook/runbook-parameters.png)

3. Välj **OK** för att starta runbookjobbet.

4. Övervaka förloppet för Runbook-jobbet och eventuella fel från **jobb** sidan.

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella datorer](update-mgmt-manage-updates-for-vm.md).

* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](../troubleshoot/update-management.md).
