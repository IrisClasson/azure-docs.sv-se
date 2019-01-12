---
title: Använda Azure Resource Manager-mallar i Azure Stack | Microsoft Docs
description: Lär dig hur du använder Azure Resource Manager-mallar i Azure Stack för att etablera resurser.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 3738cd6387c22ca0917fa257a942aaa1009ee08c
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246678"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Använda Azure Resource Manager-mallar i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda Azure Resource Manager-mallar för att distribuera och etablera alla resurser för ditt program i en enda, samordnad åtgärd. Du kan också distribuera om mallar för att göra ändringar i resurser i en resursgrupp.

Dessa mallar kan distribueras med Microsoft Azure Stack-portalen, PowerShell, kommandorad och Visual Studio.

Följande snabbstartsmallar är tillgängliga på [GitHub](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Distribuera SharePoint-Server (distribution med hög tillgänglighet)

Använda PowerShell DSC-tillägget för [skapa en SharePoint Server 2013-servergrupp](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) som innehåller följande resurser:

* Ett virtuellt nätverk
* Tre lagringskonton
* Två externa belastningsutjämnare
* En virtuell dator (VM) som konfigurerats som en domänkontrollant för en ny skog med en enda domän
* En virtuell dator som konfigurerats som en fristående server för SQL Server 2014
* En dator som en SharePoint Server 2013-servergrupp med en dator

## <a name="deploy-ad-non-high-availability-deployment"></a>Distribuera AD (icke-hög-tillgänglighet-distribution)

Använda PowerShell DSC-tillägget för [skapa en AD-domänkontrollantserver](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) som innehåller följande resurser:

* Ett virtuellt nätverk
* Ett lagringskonto
* En extern belastningsutjämnare
* En virtuell dator (VM) som konfigurerats som en domänkontrollant för en ny skog med en enda domän

## <a name="deploy-adsql-non-high-availability-deployment"></a>Distribuera AD/SQL (icke-hög-tillgänglighet-distribution)

Använda PowerShell DSC-tillägget för [skapa en fristående server för SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) som innehåller följande resurser:

* Ett virtuellt nätverk
* Två lagringskonton
* En extern belastningsutjämnare
* En virtuell dator (VM) som konfigurerats som en domänkontrollant för en ny skog med en enda domän
* En virtuell dator som konfigurerats som en fristående server för SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Använda tillägget PowerShell DSC för att konfigurera en befintlig virtuell dator lokala Configuration Manager (LCM) och registrera den till en Azure Automation-konto DSC-Hämtningsserver.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Skapa en virtuell dator från en användaravbildning

[Skapa en virtuell dator från en anpassad användaravbildning](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-from-user-image). Den här mallen distribuerar också ett virtuellt nätverk (med DNS), offentlig IP-adress och ett nätverksgränssnitt.

## <a name="basic-virtual-machine"></a>Grundläggande virtuell dator

[Distribuera en virtuell Windows-dator](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) som innehåller ett virtuellt nätverk (med DNS), offentlig IP-adress och ett nätverksgränssnitt.

## <a name="cancel-a-running-template-deployment"></a>Avbryt en malldistribution som körs

Om du vill avbryta en malldistribution som körs använder den [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) PowerShell-cmdlet.

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med portalen](azure-stack-deploy-template-portal.md)
* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
